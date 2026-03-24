# Ejercicio Resuelto 1: Componente Tarjeta de Producto

**Nivel:** Intermedio  
**Tiempo estimado:** 60-90 minutos  
**Conceptos:** JSX, props, renderizado condicional, diseño atómico, estilos con CSS Modules

---

## Enunciado

Construir un sistema de componentes para mostrar tarjetas de producto en un catálogo de e-commerce, aplicando los principios del Diseño Atómico. El sistema debe incluir:

1. Un átomo `Badge` para mostrar etiquetas de estado.
2. Un átomo `Button` con variantes.
3. Una molécula `ProductCard` que combine los átomos anteriores.
4. Una página de catálogo que muestre una cuadrícula de productos.

### Requisitos funcionales

- Mostrar imagen, nombre, precio, categoría y estado de stock de cada producto.
- Si el producto no tiene stock, el botón debe estar deshabilitado y mostrar un badge "Agotado".
- Si el producto tiene descuento, mostrar el precio original tachado y el precio con descuento.
- Al hacer clic en "Añadir al carrito", mostrar un contador en la cabecera.

---

## Solución paso a paso

### Paso 1: Configurar el proyecto

```bash
npm create vite@latest catalogo-react -- --template react
cd catalogo-react
npm install
npm run dev
```

### Paso 2: Crear el átomo Badge

```jsx
// src/components/atoms/Badge/Badge.jsx

const variantsMap = {
  success: { bg: '#d1fae5', color: '#065f46', border: '#6ee7b7' },
  error:   { bg: '#fee2e2', color: '#991b1b', border: '#fca5a5' },
  warning: { bg: '#fef3c7', color: '#92400e', border: '#fcd34d' },
  info:    { bg: '#dbeafe', color: '#1e40af', border: '#93c5fd' },
  default: { bg: '#f3f4f6', color: '#374151', border: '#d1d5db' },
};

function Badge({ children, variant = 'default' }) {
  const styles = variantsMap[variant] ?? variantsMap.default;

  return (
    <span
      style={{
        display: 'inline-block',
        padding: '2px 8px',
        borderRadius: '9999px',
        fontSize: '12px',
        fontWeight: 600,
        backgroundColor: styles.bg,
        color: styles.color,
        border: `1px solid ${styles.border}`,
      }}
    >
      {children}
    </span>
  );
}

export default Badge;
```

**¿Por qué así?**
- Usamos `inline styles` con un mapa de variantes en lugar de clases CSS para mantener el componente auto-contenido en este ejemplo.
- En un proyecto real usaríamos CSS Modules o Tailwind CSS para separar estilos.
- El componente es completamente agnóstico de su contexto: solo sabe cómo pintarse.

### Paso 3: Crear el átomo Button

```jsx
// src/components/atoms/Button/Button.jsx

const variants = {
  primary:   { bg: '#0070f3', color: 'white',   border: '#0070f3'  },
  secondary: { bg: 'white',   color: '#0070f3', border: '#0070f3'  },
  danger:    { bg: '#ef4444', color: 'white',   border: '#ef4444'  },
  ghost:     { bg: 'transparent', color: '#374151', border: 'transparent' },
};

function Button({
  children,
  variant = 'primary',
  disabled = false,
  onClick,
  type = 'button',
  fullWidth = false,
}) {
  const style = variants[variant] ?? variants.primary;

  return (
    <button
      type={type}
      disabled={disabled}
      onClick={onClick}
      style={{
        display: 'inline-flex',
        alignItems: 'center',
        justifyContent: 'center',
        gap: '8px',
        padding: '8px 16px',
        borderRadius: '8px',
        border: `1px solid ${style.border}`,
        backgroundColor: disabled ? '#e5e7eb' : style.bg,
        color: disabled ? '#9ca3af' : style.color,
        fontSize: '14px',
        fontWeight: 500,
        cursor: disabled ? 'not-allowed' : 'pointer',
        width: fullWidth ? '100%' : 'auto',
        transition: 'opacity 0.2s',
      }}
    >
      {children}
    </button>
  );
}

export default Button;
```

**¿Por qué así?**
- El botón acepta una prop `disabled` para manejar el estado de "no disponible".
- `fullWidth` es un ejemplo de prop booleana para variaciones de layout.
- Los estilos del estado deshabilitado sobreescriben el color de fondo y cursor.

### Paso 4: Crear la molécula ProductCard

```jsx
// src/components/molecules/ProductCard/ProductCard.jsx
import Badge from '../../atoms/Badge/Badge';
import Button from '../../atoms/Button/Button';

function ProductCard({ product, onAddToCart }) {
  const {
    id,
    nombre,
    precio,
    precioOriginal,   // Si existe, hay descuento
    imagen,
    categoria,
    enStock,
  } = product;

  const tieneDescuento = precioOriginal && precioOriginal > precio;
  const porcentajeDescuento = tieneDescuento
    ? Math.round((1 - precio / precioOriginal) * 100)
    : 0;

  return (
    <article
      style={{
        border: '1px solid #e5e7eb',
        borderRadius: '12px',
        overflow: 'hidden',
        backgroundColor: 'white',
        display: 'flex',
        flexDirection: 'column',
        transition: 'box-shadow 0.2s',
      }}
    >
      {/* Imagen */}
      <div style={{ position: 'relative' }}>
        <img
          src={imagen}
          alt={nombre}
          style={{ width: '100%', height: '200px', objectFit: 'cover' }}
        />
        {/* Badges superpuestos a la imagen */}
        <div
          style={{
            position: 'absolute',
            top: '8px',
            left: '8px',
            display: 'flex',
            gap: '4px',
          }}
        >
          {!enStock && <Badge variant="error">Agotado</Badge>}
          {tieneDescuento && (
            <Badge variant="success">-{porcentajeDescuento}%</Badge>
          )}
        </div>
      </div>

      {/* Contenido */}
      <div style={{ padding: '16px', flex: 1, display: 'flex', flexDirection: 'column', gap: '8px' }}>
        <span style={{ fontSize: '12px', color: '#6b7280', textTransform: 'uppercase', letterSpacing: '0.05em' }}>
          {categoria}
        </span>

        <h3 style={{ margin: 0, fontSize: '16px', fontWeight: 600, color: '#111827' }}>
          {nombre}
        </h3>

        {/* Precio */}
        <div style={{ display: 'flex', alignItems: 'baseline', gap: '8px' }}>
          <span style={{ fontSize: '20px', fontWeight: 700, color: '#0070f3' }}>
            ${precio.toFixed(2)}
          </span>
          {tieneDescuento && (
            <span style={{ fontSize: '14px', color: '#9ca3af', textDecoration: 'line-through' }}>
              ${precioOriginal.toFixed(2)}
            </span>
          )}
        </div>
      </div>

      {/* Acción */}
      <div style={{ padding: '0 16px 16px' }}>
        <Button
          variant="primary"
          disabled={!enStock}
          fullWidth
          onClick={() => onAddToCart(id)}
        >
          {enStock ? '🛒 Añadir al carrito' : 'No disponible'}
        </Button>
      </div>
    </article>
  );
}

export default ProductCard;
```

**¿Por qué así?**
- La tarjeta maneja su propia lógica de presentación (calcular descuento, estado de stock).
- Solo comunica hacia afuera eventos de usuario (`onAddToCart`), no modifica el estado directamente.
- El uso de `article` como elemento raíz es semánticamente correcto para una tarjeta de producto.

### Paso 5: Crear la página principal

```jsx
// src/App.jsx
import { useState } from 'react';
import ProductCard from './components/molecules/ProductCard/ProductCard';

// Datos de muestra (en un proyecto real vendrían de una API)
const PRODUCTOS_EJEMPLO = [
  {
    id: 1,
    nombre: 'Auriculares Bluetooth Premium',
    precio: 79.99,
    precioOriginal: 129.99,
    imagen: 'https://placehold.co/400x300/0070f3/white?text=Auriculares',
    categoria: 'Electrónica',
    enStock: true,
  },
  {
    id: 2,
    nombre: 'Teclado Mecánico RGB',
    precio: 149.99,
    imagen: 'https://placehold.co/400x300/7c3aed/white?text=Teclado',
    categoria: 'Periféricos',
    enStock: true,
  },
  {
    id: 3,
    nombre: 'Webcam 4K Ultra HD',
    precio: 199.99,
    precioOriginal: 249.99,
    imagen: 'https://placehold.co/400x300/059669/white?text=Webcam',
    categoria: 'Electrónica',
    enStock: false,
  },
  {
    id: 4,
    nombre: 'Mouse Ergonómico Inalámbrico',
    precio: 59.99,
    imagen: 'https://placehold.co/400x300/dc2626/white?text=Mouse',
    categoria: 'Periféricos',
    enStock: true,
  },
  {
    id: 5,
    nombre: 'Monitor Curvo 27"',
    precio: 399.99,
    precioOriginal: 499.99,
    imagen: 'https://placehold.co/400x300/d97706/white?text=Monitor',
    categoria: 'Monitores',
    enStock: true,
  },
  {
    id: 6,
    nombre: 'Hub USB-C 7 en 1',
    precio: 49.99,
    imagen: 'https://placehold.co/400x300/0891b2/white?text=Hub+USB',
    categoria: 'Accesorios',
    enStock: false,
  },
];

function App() {
  const [carrito, setCarrito] = useState([]);

  const agregarAlCarrito = (productoId) => {
    setCarrito(prev => {
      const existente = prev.find(item => item.id === productoId);
      if (existente) {
        return prev.map(item =>
          item.id === productoId
            ? { ...item, cantidad: item.cantidad + 1 }
            : item
        );
      }
      return [...prev, { id: productoId, cantidad: 1 }];
    });
  };

  const totalItems = carrito.reduce((sum, item) => sum + item.cantidad, 0);

  return (
    <div style={{ minHeight: '100vh', backgroundColor: '#f9fafb' }}>
      {/* Header */}
      <header
        style={{
          backgroundColor: 'white',
          borderBottom: '1px solid #e5e7eb',
          padding: '16px 32px',
          display: 'flex',
          justifyContent: 'space-between',
          alignItems: 'center',
          position: 'sticky',
          top: 0,
          zIndex: 10,
        }}
      >
        <h1 style={{ margin: 0, fontSize: '20px', fontWeight: 700, color: '#0070f3' }}>
          🛍️ Tech Store
        </h1>
        <div style={{ display: 'flex', alignItems: 'center', gap: '8px' }}>
          <span style={{ fontSize: '20px' }}>🛒</span>
          {totalItems > 0 && (
            <span
              style={{
                backgroundColor: '#0070f3',
                color: 'white',
                borderRadius: '9999px',
                padding: '2px 8px',
                fontSize: '14px',
                fontWeight: 700,
              }}
            >
              {totalItems}
            </span>
          )}
        </div>
      </header>

      {/* Catálogo */}
      <main style={{ maxWidth: '1200px', margin: '0 auto', padding: '32px' }}>
        <h2 style={{ marginBottom: '24px', color: '#111827' }}>
          Nuestros Productos
          <span style={{ marginLeft: '12px', fontSize: '16px', color: '#6b7280', fontWeight: 400 }}>
            ({PRODUCTOS_EJEMPLO.length} productos)
          </span>
        </h2>

        <div
          style={{
            display: 'grid',
            gridTemplateColumns: 'repeat(auto-fill, minmax(280px, 1fr))',
            gap: '24px',
          }}
        >
          {PRODUCTOS_EJEMPLO.map(producto => (
            <ProductCard
              key={producto.id}
              product={producto}
              onAddToCart={agregarAlCarrito}
            />
          ))}
        </div>
      </main>
    </div>
  );
}

export default App;
```

### Paso 6: Limpiar el CSS base de Vite

```css
/* src/index.css — Reemplazar el contenido con: */
*, *::before, *::after {
  box-sizing: border-box;
}

body {
  margin: 0;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  -webkit-font-smoothing: antialiased;
}
```

---

## Resultado esperado

Al ejecutar `npm run dev`, deberías ver:

- Una cabecera con el nombre "Tech Store" y un contador de carrito.
- 6 tarjetas de producto organizadas en una cuadrícula responsive.
- 2 productos marcados como "Agotado" con su botón deshabilitado.
- 3 productos con badge de descuento y precio tachado.
- Al hacer clic en "Añadir al carrito", el contador de la cabecera se actualiza.

---

## Análisis del Diseño Atómico Aplicado

| Componente | Nivel | Responsabilidad |
|-----------|-------|-----------------|
| `Badge` | Átomo | Mostrar una etiqueta con color según variante |
| `Button` | Átomo | Elemento interactivo configurable |
| `ProductCard` | Molécula | Mostrar información de un producto y emitir evento de compra |
| `App` | Página | Orquestar datos y estado, renderizar la cuadrícula |

---

## Extensiones propuestas

1. **Filtro por categoría**: Añadir botones para filtrar productos por categoría.
2. **Búsqueda en tiempo real**: Input que filtre productos mientras el usuario escribe.
3. **Modal del carrito**: Mostrar un resumen del carrito al hacer clic en el ícono.
4. **Persistencia**: Guardar el carrito en `localStorage` con un custom hook `useLocalStorage`.

```jsx
// Ejemplo de extensión: custom hook para localStorage
function useLocalStorage(clave, valorInicial) {
  const [valor, setValor] = useState(() => {
    try {
      const item = window.localStorage.getItem(clave);
      return item ? JSON.parse(item) : valorInicial;
    } catch {
      return valorInicial;
    }
  });

  const guardar = (nuevoValor) => {
    setValor(nuevoValor);
    window.localStorage.setItem(clave, JSON.stringify(nuevoValor));
  };

  return [valor, guardar];
}

// Uso en App.jsx
const [carrito, setCarrito] = useLocalStorage('carrito', []);
```

---

## Tips Empresariales del Ejercicio

> **🏢 Tip 1:** La separación en átomos y moléculas desde el principio hace que el sistema sea escalable. Si mañana el equipo de diseño cambia el color del botón, solo se modifica el átomo `Button` y el cambio se propaga en toda la aplicación.

> **🏢 Tip 2:** Usar `data-testid` en elementos interactivos desde el principio facilita las pruebas automáticas con Cypress o Playwright sin acoplarse a detalles de implementación.

> **🏢 Tip 3:** Los props de callback (`onAddToCart`, `onClick`) siempre deben nombrarse con el prefijo `on` para seguir la convención de React. Esto facilita la lectura del código en PR reviews.

---

*← [Volver a Ejercicios](../README.md) | Siguiente: [Ejercicio 2: Lista de Tareas con React 19 →](./ejercicio-2-lista-tareas-react19.md)*
