# Diseño Atómico y Organización de Componentes React

El **Diseño Atómico** es una metodología de diseño de UI propuesta por **Brad Frost** en 2013. Provee un vocabulario compartido entre diseñadores y desarrolladores, y es especialmente útil en proyectos React de escala empresarial donde múltiples equipos trabajan en la misma base de código.

---

## 1. Principios del Diseño Atómico

La metodología toma prestados conceptos de la química: así como la materia está compuesta de átomos que se combinan en moléculas y estructuras más complejas, nuestra UI se construye de piezas pequeñas que se ensamblan en piezas más grandes.

### Los 5 niveles

```
Átomos → Moléculas → Organismos → Templates → Páginas
  ↑           ↑           ↑            ↑          ↑
Más simple                                    Más complejo
```

| Nivel | Descripción | Ejemplos |
|-------|-------------|----------|
| **Átomos** | Unidades UI más pequeñas e indivisibles | Botón, Input, Label, Icono, Avatar |
| **Moléculas** | Grupos de átomos con función específica | Campo de formulario, Tarjeta de producto, Item de menú |
| **Organismos** | Secciones complejas que combinan moléculas | Header, Footer, Formulario completo, Carrusel |
| **Templates** | Estructura de página sin datos reales | Layout de página de producto |
| **Páginas** | Templates con datos reales | Página de producto con datos de la API |

---

## 2. Los 5 Niveles en Detalle con React

### Átomos

Los átomos son los bloques de construcción fundamentales. Deben ser:
- **Independientes**: sin dependencias de contexto de negocio.
- **Reutilizables**: usables en cualquier parte del sistema.
- **Configurables**: su apariencia y comportamiento se controla totalmente via props.

```jsx
// src/components/atoms/Button/Button.jsx
function Button({
  children,
  variant = 'primary',   // 'primary' | 'secondary' | 'danger' | 'ghost'
  size = 'md',           // 'sm' | 'md' | 'lg'
  disabled = false,
  loading = false,
  onClick,
  type = 'button',
  ...props
}) {
  return (
    <button
      type={type}
      disabled={disabled || loading}
      onClick={onClick}
      className={`btn btn--${variant} btn--${size} ${loading ? 'btn--loading' : ''}`}
      {...props}
    >
      {loading ? <Spinner size="sm" /> : children}
    </button>
  );
}

export default Button;
```

```jsx
// src/components/atoms/Input/Input.jsx
function Input({
  id,
  name,
  type = 'text',
  placeholder,
  value,
  onChange,
  disabled = false,
  error,
  ...props
}) {
  return (
    <input
      id={id}
      name={name}
      type={type}
      placeholder={placeholder}
      value={value}
      onChange={onChange}
      disabled={disabled}
      aria-invalid={!!error}
      aria-describedby={error ? `${id}-error` : undefined}
      className={`input ${error ? 'input--error' : ''}`}
      {...props}
    />
  );
}

export default Input;
```

```jsx
// src/components/atoms/Badge/Badge.jsx
function Badge({ children, variant = 'default' }) {
  return (
    <span className={`badge badge--${variant}`}>
      {children}
    </span>
  );
}

export default Badge;
```

```jsx
// src/components/atoms/Avatar/Avatar.jsx
function Avatar({ src, alt, size = 'md', initials }) {
  if (!src && initials) {
    return (
      <div className={`avatar avatar--${size} avatar--initials`} aria-label={alt}>
        {initials.substring(0, 2).toUpperCase()}
      </div>
    );
  }

  return (
    <img
      src={src}
      alt={alt}
      className={`avatar avatar--${size}`}
    />
  );
}

export default Avatar;
```

### Moléculas

Las moléculas son combinaciones de átomos que forman una unidad funcional con un propósito específico.

```jsx
// src/components/molecules/FormField/FormField.jsx
import Input from '../../atoms/Input/Input';

function FormField({ id, label, error, helpText, required, ...inputProps }) {
  return (
    <div className="form-field">
      <label htmlFor={id} className="form-field__label">
        {label}
        {required && <span className="form-field__required" aria-hidden>*</span>}
      </label>

      <Input id={id} error={error} {...inputProps} />

      {helpText && !error && (
        <p className="form-field__help">{helpText}</p>
      )}
      {error && (
        <p id={`${id}-error`} className="form-field__error" role="alert">
          {error}
        </p>
      )}
    </div>
  );
}

export default FormField;
```

```jsx
// src/components/molecules/ProductCard/ProductCard.jsx
import Button from '../../atoms/Button/Button';
import Badge from '../../atoms/Badge/Badge';

function ProductCard({ product, onAddToCart }) {
  const { id, nombre, precio, imagen, categoria, enStock } = product;

  return (
    <article className="product-card">
      <div className="product-card__image-wrapper">
        <img src={imagen} alt={nombre} className="product-card__image" />
        {!enStock && (
          <Badge variant="error">Agotado</Badge>
        )}
      </div>

      <div className="product-card__content">
        <span className="product-card__category">{categoria}</span>
        <h3 className="product-card__name">{nombre}</h3>
        <p className="product-card__price">
          ${precio.toFixed(2)}
        </p>
      </div>

      <div className="product-card__footer">
        <Button
          variant="primary"
          disabled={!enStock}
          onClick={() => onAddToCart(id)}
        >
          {enStock ? 'Añadir al carrito' : 'No disponible'}
        </Button>
      </div>
    </article>
  );
}

export default ProductCard;
```

```jsx
// src/components/molecules/SearchBar/SearchBar.jsx
import { useState } from 'react';
import Input from '../../atoms/Input/Input';
import Button from '../../atoms/Button/Button';

function SearchBar({ onSearch, placeholder = 'Buscar...' }) {
  const [query, setQuery] = useState('');

  const handleSubmit = (e) => {
    e.preventDefault();
    onSearch(query.trim());
  };

  return (
    <form className="search-bar" onSubmit={handleSubmit} role="search">
      <Input
        type="search"
        value={query}
        onChange={e => setQuery(e.target.value)}
        placeholder={placeholder}
        aria-label="Campo de búsqueda"
      />
      <Button type="submit" variant="primary">
        Buscar
      </Button>
    </form>
  );
}

export default SearchBar;
```

### Organismos

Los organismos son componentes más complejos que combinan moléculas y forman secciones reconocibles de la interfaz.

```jsx
// src/components/organisms/Header/Header.jsx
import Avatar from '../../atoms/Avatar/Avatar';
import Button from '../../atoms/Button/Button';
import SearchBar from '../../molecules/SearchBar/SearchBar';

function Header({ usuario, logoSrc, onSearch, onLogout, itemsCarrito = 0 }) {
  return (
    <header className="header">
      <div className="header__brand">
        <img src={logoSrc} alt="Logo empresa" className="header__logo" />
      </div>

      <nav className="header__nav" aria-label="Navegación principal">
        <SearchBar onSearch={onSearch} placeholder="Buscar productos..." />
      </nav>

      <div className="header__actions">
        <Button variant="ghost" aria-label={`Carrito: ${itemsCarrito} items`}>
          🛒 {itemsCarrito > 0 && <span className="badge">{itemsCarrito}</span>}
        </Button>

        <div className="header__user">
          <Avatar
            src={usuario.foto}
            alt={usuario.nombre}
            initials={usuario.nombre}
            size="sm"
          />
          <Button variant="ghost" onClick={onLogout}>
            Cerrar sesión
          </Button>
        </div>
      </div>
    </header>
  );
}

export default Header;
```

```jsx
// src/components/organisms/ProductGrid/ProductGrid.jsx
import ProductCard from '../../molecules/ProductCard/ProductCard';

function ProductGrid({ products, onAddToCart, isLoading }) {
  if (isLoading) {
    return (
      <div className="product-grid product-grid--loading">
        {Array.from({ length: 8 }).map((_, i) => (
          <div key={i} className="product-card product-card--skeleton" aria-hidden />
        ))}
      </div>
    );
  }

  if (products.length === 0) {
    return (
      <div className="product-grid product-grid--empty">
        <p>No se encontraron productos</p>
      </div>
    );
  }

  return (
    <section className="product-grid" aria-label="Catálogo de productos">
      {products.map(product => (
        <ProductCard
          key={product.id}
          product={product}
          onAddToCart={onAddToCart}
        />
      ))}
    </section>
  );
}

export default ProductGrid;
```

### Templates

Los templates definen la estructura de la página usando componentes reales, pero con datos de placeholder o props vacías. Son el "esqueleto" de la página.

```jsx
// src/components/templates/CatalogoTemplate/CatalogoTemplate.jsx
import Header from '../../organisms/Header/Header';
import ProductGrid from '../../organisms/ProductGrid/ProductGrid';
import Footer from '../../organisms/Footer/Footer';

function CatalogoTemplate({
  usuario,
  products,
  isLoading,
  itemsCarrito,
  onSearch,
  onAddToCart,
  onLogout,
}) {
  return (
    <div className="page-layout">
      <Header
        usuario={usuario}
        logoSrc="/logo.svg"
        onSearch={onSearch}
        onLogout={onLogout}
        itemsCarrito={itemsCarrito}
      />

      <main className="page-layout__main">
        <h1 className="visually-hidden">Catálogo de productos</h1>
        <ProductGrid
          products={products}
          onAddToCart={onAddToCart}
          isLoading={isLoading}
        />
      </main>

      <Footer />
    </div>
  );
}

export default CatalogoTemplate;
```

### Páginas

Las páginas conectan los templates con los datos reales (del servidor, contexto o estado global).

```jsx
// src/pages/CatalogoPage.jsx
import { useState, useEffect } from 'react';
import CatalogoTemplate from '../components/templates/CatalogoTemplate/CatalogoTemplate';
import { useAuth } from '../hooks/useAuth';
import { useCarrito } from '../hooks/useCarrito';

function CatalogoPage() {
  const { usuario, logout } = useAuth();
  const { items, agregarItem } = useCarrito();
  const [productos, setProductos] = useState([]);
  const [cargando, setCargando] = useState(true);
  const [filtro, setFiltro] = useState('');

  useEffect(() => {
    fetch(`/api/productos?q=${filtro}`)
      .then(res => res.json())
      .then(data => {
        setProductos(data);
        setCargando(false);
      });
  }, [filtro]);

  return (
    <CatalogoTemplate
      usuario={usuario}
      products={productos}
      isLoading={cargando}
      itemsCarrito={items.length}
      onSearch={setFiltro}
      onAddToCart={agregarItem}
      onLogout={logout}
    />
  );
}

export default CatalogoPage;
```

---

## 3. Estructuración de Proyectos React

### Estructura recomendada para proyectos empresariales

```
src/
├── components/          ← Diseño atómico
│   ├── atoms/
│   │   ├── Button/
│   │   │   ├── Button.jsx
│   │   │   ├── Button.test.jsx
│   │   │   ├── Button.stories.jsx    ← Storybook (opcional)
│   │   │   └── index.js              ← Re-export
│   │   ├── Input/
│   │   ├── Badge/
│   │   ├── Avatar/
│   │   └── index.js                  ← Barrel export de todos los átomos
│   ├── molecules/
│   │   ├── FormField/
│   │   ├── ProductCard/
│   │   ├── SearchBar/
│   │   └── index.js
│   ├── organisms/
│   │   ├── Header/
│   │   ├── ProductGrid/
│   │   ├── Footer/
│   │   └── index.js
│   └── templates/
│       ├── CatalogoTemplate/
│       ├── DetalleTemplate/
│       └── index.js
│
├── pages/               ← Una por ruta de la aplicación
│   ├── CatalogoPage.jsx
│   ├── DetalleProductoPage.jsx
│   ├── CarritoPage.jsx
│   └── index.js
│
├── hooks/               ← Custom hooks reutilizables
│   ├── useAuth.js
│   ├── useCarrito.js
│   ├── useLocalStorage.js
│   └── index.js
│
├── context/             ← Contextos de React
│   ├── AuthContext.jsx
│   ├── CarritoContext.jsx
│   └── index.js
│
├── services/            ← Lógica de API / comunicación con servidor
│   ├── api.js           ← Configuración base (axios/fetch)
│   ├── productos.js
│   ├── auth.js
│   └── index.js
│
├── utils/               ← Funciones utilitarias puras
│   ├── formatters.js    ← formatPrecio, formatFecha, etc.
│   ├── validators.js
│   └── constants.js
│
├── assets/              ← Imágenes, fuentes, iconos
│   ├── images/
│   └── fonts/
│
├── styles/              ← Estilos globales y variables CSS
│   ├── globals.css
│   ├── variables.css
│   └── utils.css
│
├── App.jsx
└── main.jsx
```

### Barrel exports (archivos index.js)

Los barrel exports permiten importar desde el nivel del directorio sin especificar la ruta completa del archivo:

```js
// src/components/atoms/index.js
export { default as Button } from './Button/Button';
export { default as Input } from './Input/Input';
export { default as Badge } from './Badge/Badge';
export { default as Avatar } from './Avatar/Avatar';
export { default as Spinner } from './Spinner/Spinner';
```

```jsx
// Sin barrel exports
import Button from '../components/atoms/Button/Button';
import Input from '../components/atoms/Input/Input';
import Badge from '../components/atoms/Badge/Badge';

// Con barrel exports
import { Button, Input, Badge } from '../components/atoms';
```

> **⚠️ Advertencia:** Los barrel exports pueden perjudicar el tree-shaking si tu bundler no está bien configurado. Con Vite y ES Modules modernos, generalmente no hay problemas.

---

## 4. Patrones de Organización de Ficheros

### Patrón 1: Colocation (Co-localización)

Mantener todos los archivos relacionados con un componente en la misma carpeta:

```
Button/
├── Button.jsx       ← Componente
├── Button.test.jsx  ← Tests
├── Button.css       ← Estilos (o Button.module.css)
├── Button.stories.jsx ← Documentación visual (Storybook)
└── index.js         ← Barrel export
```

**Ventaja:** Todo lo que necesitas para un componente está en un lugar. Facilita borrar o mover componentes.

### Patrón 2: Separación por tipo de archivo

```
src/
├── components/
│   ├── Button.jsx
│   ├── Input.jsx
├── styles/
│   ├── Button.css
│   ├── Input.css
├── tests/
│   ├── Button.test.jsx
│   ├── Input.test.jsx
```

**Cuándo usarlo:** Proyectos muy pequeños o herencia de proyectos más antiguos. No recomendado para proyectos nuevos.

### Patrón 3: Feature-based (por funcionalidad)

Organiza por dominio de negocio en lugar de por tipo de archivo:

```
src/
├── features/
│   ├── auth/
│   │   ├── components/    ← Componentes específicos de auth
│   │   │   ├── LoginForm/
│   │   │   └── RegisterForm/
│   │   ├── hooks/
│   │   │   └── useAuth.js
│   │   ├── services/
│   │   │   └── auth.service.js
│   │   └── index.js
│   │
│   ├── catalogo/
│   │   ├── components/
│   │   │   ├── ProductCard/
│   │   │   └── ProductGrid/
│   │   ├── hooks/
│   │   │   └── useProductos.js
│   │   ├── services/
│   │   │   └── productos.service.js
│   │   └── index.js
│   │
│   └── carrito/
│       ├── components/
│       ├── context/
│       └── index.js
│
├── shared/              ← Componentes y utilidades compartidas entre features
│   ├── components/
│   │   ├── atoms/
│   │   ├── molecules/
│   │   └── organisms/
│   ├── hooks/
│   └── utils/
│
└── pages/
    ├── CatalogoPage.jsx
    └── LoginPage.jsx
```

**Cuándo usarlo:** Proyectos grandes con múltiples equipos trabajando en features diferentes. Minimiza los conflictos de merge.

> **💡 Tip empresarial:** La arquitectura **Feature-based + Diseño Atómico en shared/** es el estándar que usan equipos como el de Airbnb y Netflix. Los componentes atómicos viven en `shared/` y son mantenidos por el "Design System Team", mientras los features son propiedad de los equipos de producto.

### Alias de rutas con Vite

Los alias eliminan las rutas relativas largas (`../../../`):

```js
// vite.config.js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import path from 'path';

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
      '@atoms': path.resolve(__dirname, './src/components/atoms'),
      '@molecules': path.resolve(__dirname, './src/components/molecules'),
      '@organisms': path.resolve(__dirname, './src/components/organisms'),
      '@templates': path.resolve(__dirname, './src/components/templates'),
      '@pages': path.resolve(__dirname, './src/pages'),
      '@hooks': path.resolve(__dirname, './src/hooks'),
      '@services': path.resolve(__dirname, './src/services'),
      '@utils': path.resolve(__dirname, './src/utils'),
    },
  },
});
```

```jsx
// Sin alias
import Button from '../../../components/atoms/Button/Button';

// Con alias
import Button from '@atoms/Button/Button';
// o usando el barrel:
import { Button } from '@atoms';
```

---

## Diseño Atómico y Design Systems

### Storybook: documentación visual de componentes

**Storybook** es la herramienta estándar para documentar componentes atómicos. Permite visualizar y probar componentes en aislamiento.

```bash
# Instalar Storybook
npx storybook@latest init
```

```jsx
// Button.stories.jsx
import Button from './Button';

export default {
  title: 'Atoms/Button',
  component: Button,
  argTypes: {
    variant: {
      control: 'select',
      options: ['primary', 'secondary', 'danger', 'ghost'],
    },
    size: {
      control: 'select',
      options: ['sm', 'md', 'lg'],
    },
  },
};

export const Primary = {
  args: {
    children: 'Botón primario',
    variant: 'primary',
  },
};

export const Disabled = {
  args: {
    children: 'Deshabilitado',
    disabled: true,
  },
};

export const Loading = {
  args: {
    children: 'Cargando...',
    loading: true,
  },
};
```

> **💡 Tip empresarial:** Storybook es el estándar de facto para Design Systems en empresas grandes. Permite que diseñadores y QA revisen componentes independientemente del contexto de la aplicación. Airbnb, IBM, Shopify y GitHub lo usan para sus sistemas de diseño.

---

## Resumen: ¿Cómo decidir la estructura?

| Tamaño del proyecto | Arquitectura recomendada |
|--------------------|--------------------------|
| **Pequeño** (<5 desarrolladores) | Diseño Atómico plano en `components/` |
| **Mediano** (5-20 desarrolladores) | Diseño Atómico + separación por tipo |
| **Grande** (>20 desarrolladores) | Feature-based + Atómico en `shared/` |
| **Enterprise** (múltiples equipos) | Monorepo (Nx/Turborepo) + Design System separado |

---

## Recursos Adicionales

- [Atomic Design — Brad Frost (libro gratuito)](https://atomicdesign.bradfrost.com/table-of-contents/)
- [Storybook — Documentación](https://storybook.js.org/docs)
- [Bulletproof React — Arquitectura de proyectos](https://github.com/alan2207/bulletproof-react)
- [Component Driven Development](https://www.componentdriven.org)
- [Design Systems — Figma](https://www.figma.com/blog/design-systems-for-developers/)

---

*← [JSX en profundidad](../jsx/README.md) | [Volver al índice](../README.md) | Siguiente: [Ejercicios resueltos →](../ejercicios/resueltos/)*
