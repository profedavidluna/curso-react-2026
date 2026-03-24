# Fundamentos de React

## ¿Qué es React y por qué usarlo?

**React** es una biblioteca de JavaScript de código abierto desarrollada por Meta (Facebook) en 2013, diseñada para construir interfaces de usuario (UI) de forma declarativa, eficiente y composable.

React **no** es un framework completo: no incluye enrutamiento, manejo de estado global, ni peticiones HTTP. Esta filosofía de "biblioteca pequeña y enfocada" es precisamente una de sus fortalezas: permite integrarse en cualquier stack tecnológico y te da libertad para elegir las herramientas que mejor se adapten a tu proyecto.

### Características clave

| Característica | Descripción |
|----------------|-------------|
| **Declarativo** | Describes *qué* debe verse en pantalla, no *cómo* manipular el DOM paso a paso |
| **Basado en componentes** | La UI se divide en piezas reutilizables e independientes |
| **Learn Once, Write Anywhere** | Usa React en la web (react-dom), móvil (React Native), VR (React 360), e incluso servidores |
| **Unidirectional Data Flow** | Los datos fluyen de componente padre a componente hijo, haciendo el código predecible |
| **Ecosistema maduro** | NPM con miles de librerías compatibles, comunidad activa, adopción masiva |

### ¿Por qué elegir React en 2026?

1. **Mercado laboral**: React domina el mercado de trabajo front-end. Según el Stack Overflow Developer Survey 2024, React es la biblioteca web más usada (más del 40% de los desarrolladores web).
2. **Mantenimiento y longevidad**: Respaldado por Meta y utilizado en productos con miles de millones de usuarios (Facebook, Instagram, WhatsApp Web).
3. **Ecosistema**: Next.js, Remix, React Native, Expo, Astro y decenas de soluciones empresariales están construidas sobre React.
4. **React 19**: La versión más reciente introduce mejoras transformadoras que acercan React a los frameworks "full-stack".

> **💡 Caso de uso empresarial:** Empresas como Airbnb, Netflix, Uber, Shopify, Twitter/X, LinkedIn y Microsoft usan React en sus productos principales. Al elegir React, tu equipo se beneficia de la madurez de estas soluciones.

---

## Virtual DOM y su funcionamiento

### El problema del DOM nativo

El **DOM (Document Object Model)** es la representación en memoria del HTML de una página. Manipularlo directamente con `document.getElementById`, `innerHTML`, etc., puede ser lento cuando se hacen muchas operaciones porque:

1. Cada modificación al DOM puede desencadenar un **reflow** (recálculo del layout) y un **repaint** (redibujado de píxeles).
2. En aplicaciones grandes con cientos de actualizaciones por segundo, esto se convierte en un cuello de botella de rendimiento.

### El Virtual DOM como solución

React introduce el concepto de **Virtual DOM (VDOM)**: una representación ligera del DOM real, almacenada en memoria como objetos JavaScript.

```
Estado actualizado
        ↓
  Virtual DOM nuevo
        ↓
   Diffing (comparación)
        ↓
   Patch mínimo sobre
     el DOM real
```

### El proceso de reconciliación

Cuando el estado de un componente cambia, React:

1. **Renderiza** un nuevo Virtual DOM en memoria (muy rápido, solo objetos JS).
2. Ejecuta el algoritmo de **diff** (diferenciación) comparando el nuevo VDOM con el anterior.
3. Calcula el conjunto mínimo de cambios necesarios (el "patch").
4. Aplica **solo esos cambios** al DOM real del navegador.

```jsx
// Cuando este estado cambia...
const [count, setCount] = useState(0);

// ...React solo actualiza el nodo de texto del número,
// no re-renderiza todo el árbol DOM
return <p>Contador: {count}</p>;
```

### Ejemplo ilustrativo

```jsx
// Antes del cambio
<ul>
  <li key="a">Elemento A</li>
  <li key="b">Elemento B</li>
</ul>

// Después del cambio (se añadió un elemento al principio)
<ul>
  <li key="c">Elemento C</li>  ← Solo este nodo se CREA en el DOM real
  <li key="a">Elemento A</li>  ← Sin cambios
  <li key="b">Elemento B</li>  ← Sin cambios
</ul>
```

> **⚠️ Nota importante:** Las `key` en listas son fundamentales para que el algoritmo de diff sea eficiente. Sin ellas, React desmonta y recrea todos los nodos de la lista. Ver la sección JSX para más detalle.

### React Fiber (el motor interno)

Desde React 16, el algoritmo de reconciliación se reescribió con **Fiber**: una arquitectura que permite dividir el trabajo de renderizado en unidades pequeñas e **interrumpirlo** si llega trabajo más prioritario (como responder a una entrada del usuario). Esta es la base del **Concurrent Mode** de React 19.

> **💡 Tip empresarial:** No necesitas entender Fiber en profundidad para ser productivo con React. Pero entenderlo te ayuda a diagnosticar problemas de rendimiento y a usar correctamente herramientas como `React.memo`, `useMemo` y `useCallback`.

---

## Arquitectura basada en componentes

### ¿Qué es un componente?

Un **componente** es una función JavaScript que:
- Acepta propiedades de entrada (llamadas **props**).
- Devuelve elementos React que describen lo que debe mostrarse en la pantalla.

```jsx
// Componente funcional básico
function Saludo({ nombre }) {
  return <h1>¡Hola, {nombre}!</h1>;
}

// Uso del componente
<Saludo nombre="María" />
```

### Principios de la arquitectura de componentes

#### 1. Responsabilidad única

Cada componente debe hacer **una sola cosa** bien. Si un componente crece demasiado, es señal de que debe dividirse.

```jsx
// ❌ Componente con demasiadas responsabilidades
function PaginaUsuario() {
  // Maneja formulario, lista, gráfico, navegación...
}

// ✅ Componentes con responsabilidad única
function PerfilUsuario({ usuario }) { /* solo muestra el perfil */ }
function ListaPedidos({ pedidos }) { /* solo muestra pedidos */ }
function GraficoActividad({ datos }) { /* solo muestra el gráfico */ }
```

#### 2. Composición sobre herencia

React favorece la **composición** (combinar componentes) sobre la herencia de clases.

```jsx
// Componentes pequeños que se componen
function Avatar({ src, alt }) {
  return <img src={src} alt={alt} className="avatar" />;
}

function NombreUsuario({ nombre, rol }) {
  return (
    <div>
      <strong>{nombre}</strong>
      <span>{rol}</span>
    </div>
  );
}

// Componente compuesto
function TarjetaUsuario({ usuario }) {
  return (
    <div className="tarjeta">
      <Avatar src={usuario.foto} alt={usuario.nombre} />
      <NombreUsuario nombre={usuario.nombre} rol={usuario.rol} />
    </div>
  );
}
```

#### 3. Flujo de datos unidireccional

Los datos fluyen **hacia abajo** (de padre a hijo mediante props) y los eventos fluyen **hacia arriba** (mediante callbacks).

```
App (estado global)
 ├── Header (recibe datos por props)
 ├── Main
 │    ├── ProductList (recibe lista de productos)
 │    │    └── ProductCard (recibe un producto)
 │    └── ShoppingCart (recibe items del carrito)
 └── Footer
```

```jsx
// El padre controla el estado
function App() {
  const [carrito, setCarrito] = useState([]);

  const agregarAlCarrito = (producto) => {
    setCarrito(prev => [...prev, producto]);
  };

  return (
    <div>
      <ListaProductos onAgregar={agregarAlCarrito} />
      <Carrito items={carrito} />
    </div>
  );
}

// El hijo emite eventos hacia arriba
function ProductCard({ producto, onAgregar }) {
  return (
    <div>
      <h3>{producto.nombre}</h3>
      <button onClick={() => onAgregar(producto)}>
        Añadir al carrito
      </button>
    </div>
  );
}
```

#### 4. Props vs State

| | Props | State |
|--|-------|-------|
| **Origen** | Del componente padre | Del propio componente |
| **Mutabilidad** | Inmutables (solo lectura) | Mutable (con `setState`) |
| **Propósito** | Configurar un componente | Datos que cambian con el tiempo |
| **Ejemplo** | `color="rojo"`, `usuario={obj}` | `const [abierto, setAbierto] = useState(false)` |

---

## React vs otras bibliotecas/frameworks

### Comparativa general

| | React | Vue 3 | Angular | Svelte |
|--|-------|-------|---------|--------|
| **Tipo** | Biblioteca UI | Framework progresivo | Framework completo | Compilador |
| **Curva de aprendizaje** | Media | Baja | Alta | Baja |
| **Tamaño del bundle** | ~45KB | ~34KB | ~130KB | Muy pequeño |
| **Adopción empresarial** | Muy alta | Media | Alta | Creciente |
| **Paradigma** | Declarativo/Funcional | Opciones API / Composition API | OOP/Decoradores | Reactivo compilado |
| **Tipado** | TypeScript opcional | TypeScript opcional | TypeScript nativo | TypeScript opcional |

### React vs Vue 3

**Vue 3** tiene una sintaxis más accesible para principiantes y una curva de aprendizaje menor. Sin embargo:
- React tiene un ecosistema más amplio y mayor demanda laboral.
- Vue ofrece `<template>`, `<script>` y `<style>` en un solo archivo (SFC), lo que algunos encuentran más ordenado.
- React es más flexible; Vue es más opinado.

```vue
<!-- Vue 3 SFC -->
<template>
  <h1>{{ saludo }}</h1>
</template>
<script setup>
const saludo = 'Hola desde Vue';
</script>
```

```jsx
// React equivalente
function Saludo() {
  const saludo = 'Hola desde React';
  return <h1>{saludo}</h1>;
}
```

### React vs Angular

**Angular** es un framework completo (routing, HTTP, forms, DI, testing) mantenido por Google. Sus diferencias clave:
- Angular **requiere TypeScript** de forma nativa; React lo sugiere.
- Angular tiene una curva de aprendizaje más empinada pero más estructura en proyectos grandes.
- Angular usa inyección de dependencias y decoradores (`@Component`, `@Injectable`).
- React 19 con Server Components se acerca a las capacidades full-stack de Angular.

> **💡 Tip empresarial:** Muchas empresas con proyectos legacy de Angular están migrando gradualmente a React. Conocer ambos aumenta tu empleabilidad significativamente.

### React vs Svelte

**Svelte** es un compilador que genera JavaScript puro sin runtime. Sus ventajas:
- Bundles más pequeños (no hay runtime de React).
- Sintaxis más concisa para casos simples.

Sin embargo, React domina el ecosistema empresarial y tiene más soporte de librerías de terceros.

### ¿Cuándo elegir React?

- Proyectos de mediana a gran escala con equipos múltiples.
- Cuando necesitas un ecosistema maduro (routing, state management, testing).
- Si planeas usar React Native para una aplicación móvil complementaria.
- Cuando el equipo ya tiene experiencia en React.
- Para proyectos donde la contratación de talento es importante.

---

## Conceptos esenciales para empezar

### Tu primer componente React

```jsx
// src/components/BienvenidaCurso.jsx

function BienvenidaCurso({ nombreEstudiante, modulo }) {
  return (
    <div className="bienvenida">
      <h1>¡Bienvenido, {nombreEstudiante}!</h1>
      <p>Estás en el {modulo}. ¡Comencemos!</p>
    </div>
  );
}

export default BienvenidaCurso;
```

```jsx
// src/App.jsx
import BienvenidaCurso from './components/BienvenidaCurso';

function App() {
  return (
    <BienvenidaCurso
      nombreEstudiante="Ana García"
      modulo="Módulo 2: React 19 y JSX"
    />
  );
}

export default App;
```

### useState: el hook más fundamental

```jsx
import { useState } from 'react';

function Contador() {
  const [cuenta, setCuenta] = useState(0); // valor inicial = 0

  return (
    <div>
      <p>Contador: {cuenta}</p>
      <button onClick={() => setCuenta(cuenta + 1)}>Incrementar</button>
      <button onClick={() => setCuenta(cuenta - 1)}>Decrementar</button>
      <button onClick={() => setCuenta(0)}>Resetear</button>
    </div>
  );
}
```

### useEffect: sincronización con efectos externos

```jsx
import { useState, useEffect } from 'react';

function RelojDigital() {
  const [hora, setHora] = useState(new Date());

  useEffect(() => {
    // Efecto: actualizar la hora cada segundo
    const intervalo = setInterval(() => {
      setHora(new Date());
    }, 1000);

    // Cleanup: limpiar el intervalo cuando el componente se desmonte
    return () => clearInterval(intervalo);
  }, []); // [] = ejecutar solo al montar

  return (
    <p>
      {hora.toLocaleTimeString('es-ES')}
    </p>
  );
}
```

---

## Buenas Prácticas de Componentes

### Nomenclatura

```jsx
// ✅ PascalCase para componentes
function TarjetaProducto() {}
const BotonAccion = () => {};

// ✅ camelCase para variables y funciones internas
const precioFormateado = formatearPrecio(precio);
const handleClick = () => {};

// ✅ Archivos nombrados igual que el componente
// TarjetaProducto.jsx → export default TarjetaProducto
```

### Exportaciones

```jsx
// ✅ Export default para el componente principal del archivo
export default TarjetaProducto;

// ✅ Named exports para utilidades o subcomponentes
export { PrecioProducto, BotonAgregar };
```

### PropTypes y TypeScript

```jsx
// Con PropTypes (JS puro)
import PropTypes from 'prop-types';

TarjetaProducto.propTypes = {
  nombre: PropTypes.string.isRequired,
  precio: PropTypes.number.isRequired,
  imagen: PropTypes.string,
};

TarjetaProducto.defaultProps = {
  imagen: '/images/placeholder.jpg',
};
```

```tsx
// Con TypeScript (recomendado en proyectos empresariales)
interface Props {
  nombre: string;
  precio: number;
  imagen?: string;
}

function TarjetaProducto({ nombre, precio, imagen = '/images/placeholder.jpg' }: Props) {
  // ...
}
```

> **💡 Tip empresarial:** En proyectos nuevos, siempre usa **TypeScript**. La inversión inicial en tipos se recupera rápidamente en mantenibilidad, refactorización segura y productividad del equipo. El 78% de los proyectos React empresariales en 2024 usan TypeScript.

---

## Recursos Adicionales

- [React — Thinking in React](https://react.dev/learn/thinking-in-react)
- [React — Describing the UI](https://react.dev/learn/describing-the-ui)
- [Dan Abramov — A Complete Guide to useEffect](https://overreacted.io/a-complete-guide-to-useeffect/)
- [React Patterns](https://reactpatterns.com)
- [Bulletproof React — arquitectura de proyectos](https://github.com/alan2207/bulletproof-react)

---

*← [Volver al índice del Módulo 2](../README.md) | Siguiente: [Novedades en React 19 →](../react-19/README.md)*
