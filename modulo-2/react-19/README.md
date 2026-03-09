# Novedades en React 19

React 19 es la actualización más significativa de React desde la introducción de los Hooks en la versión 16.8. Esta versión consolida años de trabajo experimental en el Concurrent Mode y añade características completamente nuevas que cambian la forma en que construimos aplicaciones.

> **📦 Instalación de React 19:**
> ```bash
> npm install react@19 react-dom@19
> ```

---

## 1. Mejoras de Rendimiento

### Compilación más inteligente con el React Compiler

React 19 introduce el **React Compiler** (antes conocido como React Forget), que transforma automáticamente el código de tus componentes para optimizar las re-renderizaciones sin que tengas que escribir `useMemo`, `useCallback` o `React.memo` manualmente.

```jsx
// Antes de React 19: debías memoizar manualmente
import { useMemo, useCallback, memo } from 'react';

const ComponenteOptimizado = memo(function ComponenteOptimizado({ items, onSelect }) {
  const itemsFiltrados = useMemo(
    () => items.filter(i => i.activo),
    [items]
  );

  const handleSelect = useCallback((id) => {
    onSelect(id);
  }, [onSelect]);

  return <Lista items={itemsFiltrados} onSelect={handleSelect} />;
});
```

```jsx
// Con React 19 + React Compiler: el compilador lo hace por ti
function ComponenteOptimizado({ items, onSelect }) {
  const itemsFiltrados = items.filter(i => i.activo); // ← El compilador memoiza esto
  
  return (
    <Lista
      items={itemsFiltrados}
      onSelect={(id) => onSelect(id)} // ← Y esto también
    />
  );
}
```

> **⚠️ Importante:** El React Compiler es opt-in en React 19 y requiere configuración en Babel/Vite. Próximamente será el comportamiento por defecto.

### Instalación del React Compiler

```bash
npm install -D babel-plugin-react-compiler
```

```js
// vite.config.js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [
    react({
      babel: {
        plugins: [
          ['babel-plugin-react-compiler', {}],
        ],
      },
    }),
  ],
});
```

### Mejoras en el hidratado del servidor

React 19 mejora la **hidratación progresiva** (SSR), permitiendo que partes individuales de la página se hidraten de forma independiente sin bloquear el resto de la UI.

---

## 2. Nuevo Sistema de Renderizado

### Server Components (Componentes de Servidor)

Los **React Server Components (RSC)** permiten ejecutar componentes directamente en el servidor, sin enviar JavaScript al cliente. Son especialmente útiles para:

- Acceso directo a bases de datos o APIs internas.
- Reducción del tamaño del bundle JavaScript.
- Renderizado de contenido estático sin interactividad.

```jsx
// ProductoDetalle.server.jsx (componente de servidor)
// Este componente se ejecuta en el servidor y NO envía JS al cliente
async function ProductoDetalle({ id }) {
  // Acceso directo a la base de datos, sin pasar por una API
  const producto = await db.productos.findById(id);

  return (
    <div>
      <h1>{producto.nombre}</h1>
      <p>{producto.descripcion}</p>
      <p>Precio: ${producto.precio}</p>
      {/* Solo los componentes de cliente necesitan JS */}
      <BotonAgregarAlCarrito productoId={id} />
    </div>
  );
}
```

```jsx
// BotonAgregarAlCarrito.jsx (componente de cliente, marcado con 'use client')
'use client';

import { useState } from 'react';

function BotonAgregarAlCarrito({ productoId }) {
  const [agregado, setAgregado] = useState(false);

  return (
    <button onClick={() => setAgregado(true)}>
      {agregado ? '✓ Agregado' : 'Agregar al carrito'}
    </button>
  );
}
```

> **💡 Tip empresarial:** Los Server Components están integrados de forma nativa en frameworks como **Next.js 14+** y **Remix**. En aplicaciones empresariales, reducen el Time to First Byte (TTFB) y mejoran el SEO al no depender de JavaScript del cliente para renderizar contenido.

### La directiva `'use client'`

Sin la directiva `'use client'`, un componente en Next.js 14+ se asume como Server Component. La directiva le dice a React que este componente debe ejecutarse en el cliente (navegador).

```jsx
'use client'; // Marca el archivo como componente de cliente

import { useState, useEffect } from 'react';

function ContadorInteractivo() {
  const [n, setN] = useState(0);
  return <button onClick={() => setN(n + 1)}>{n}</button>;
}
```

---

## 3. Concurrent Mode y sus Beneficios

### ¿Qué es el Concurrent Mode?

El **Concurrent Mode** es un conjunto de características que permiten a React interrumpir, pausar y reanudar el renderizado según la prioridad de las tareas. En lugar de bloquear el hilo principal mientras renderiza, React puede responder a interacciones del usuario mientras procesa actualizaciones menos urgentes.

**Antes (modo legacy):**
```
Interacción del usuario → React renderiza TODO → La UI se actualiza
          ↑ Si el renderizado es lento, la UI se congela
```

**Con Concurrent Mode:**
```
Interacción del usuario → React empieza a renderizar
          ↓ (el usuario hace clic en otro botón)
React pausa el renderizado → Responde al clic → Retoma el renderizado
```

### useTransition: marcar actualizaciones como no urgentes

```jsx
import { useState, useTransition } from 'react';

function BuscadorProductos() {
  const [query, setQuery] = useState('');
  const [resultados, setResultados] = useState([]);
  const [isPending, startTransition] = useTransition();

  const handleChange = (e) => {
    // Actualización urgente: mostrar lo que el usuario escribe inmediatamente
    setQuery(e.target.value);

    // Actualización no urgente: la búsqueda puede esperar
    startTransition(() => {
      const resultadosFiltrados = buscarProductos(e.target.value);
      setResultados(resultadosFiltrados);
    });
  };

  return (
    <div>
      <input value={query} onChange={handleChange} placeholder="Buscar..." />
      {isPending ? (
        <p>Buscando...</p>
      ) : (
        <ListaResultados items={resultados} />
      )}
    </div>
  );
}
```

### useDeferredValue: diferir valores para renderizados pesados

```jsx
import { useState, useDeferredValue } from 'react';

function ListaFiltrable({ items }) {
  const [filtro, setFiltro] = useState('');
  
  // El valor diferido se actualiza con menor prioridad
  const filtroDiferido = useDeferredValue(filtro);
  
  // ListaPesada solo se re-renderiza con filtroDiferido, 
  // no con cada pulsación de tecla
  const itemsFiltrados = items.filter(i =>
    i.nombre.toLowerCase().includes(filtroDiferido.toLowerCase())
  );

  return (
    <div>
      <input
        value={filtro}
        onChange={e => setFiltro(e.target.value)}
        placeholder="Filtrar..."
      />
      <ListaPesada items={itemsFiltrados} />
    </div>
  );
}
```

### Suspense: componentes que esperan

`<Suspense>` permite mostrar un fallback mientras un componente hijo está "esperando" (cargando datos, cargando código, etc.).

```jsx
import { Suspense, lazy } from 'react';

// Lazy loading: el código de GraficoPesado se carga bajo demanda
const GraficoPesado = lazy(() => import('./GraficoPesado'));

function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>
      <Suspense fallback={<p>Cargando gráfico...</p>}>
        <GraficoPesado />
      </Suspense>
    </div>
  );
}
```

> **💡 Tip empresarial:** Usar `React.lazy` y `<Suspense>` para lazy loading reduce el tamaño del bundle inicial en aplicaciones grandes. En e-commerce, cada 100ms de mejora en la carga se traduce en ~1% más de conversión (Amazon, 2023).

---

## 4. Actions y el Nuevo Modelo de Estado

### El problema que resuelven las Actions

Antes de React 19, manejar formularios con async/await requería mucho código repetitivo:

```jsx
// Antes de React 19
function FormularioContacto() {
  const [cargando, setCargando] = useState(false);
  const [error, setError] = useState(null);
  const [exito, setExito] = useState(false);

  const handleSubmit = async (e) => {
    e.preventDefault();
    setCargando(true);
    setError(null);
    try {
      await enviarFormulario(new FormData(e.target));
      setExito(true);
    } catch (err) {
      setError(err.message);
    } finally {
      setCargando(false);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      {/* ... */}
      {cargando && <p>Enviando...</p>}
      {error && <p className="error">{error}</p>}
      {exito && <p>¡Enviado con éxito!</p>}
    </form>
  );
}
```

### useActionState: el nuevo patrón para formularios

React 19 introduce `useActionState` para simplificar este patrón:

```jsx
import { useActionState } from 'react';

async function accionEnviarFormulario(estadoAnterior, formData) {
  // Esta función se ejecuta cuando se hace submit del formulario
  const nombre = formData.get('nombre');
  const email = formData.get('email');

  if (!email.includes('@')) {
    return { error: 'Email inválido', exito: false };
  }

  try {
    await enviarAlServidor({ nombre, email });
    return { error: null, exito: true };
  } catch (err) {
    return { error: 'Error al enviar. Intenta de nuevo.', exito: false };
  }
}

function FormularioContacto() {
  const [estado, dispatch, isPending] = useActionState(
    accionEnviarFormulario,
    { error: null, exito: false } // estado inicial
  );

  return (
    <form action={dispatch}>
      <input name="nombre" placeholder="Tu nombre" required />
      <input name="email" type="email" placeholder="Tu email" required />
      
      <button type="submit" disabled={isPending}>
        {isPending ? 'Enviando...' : 'Enviar'}
      </button>

      {estado.error && <p className="error">{estado.error}</p>}
      {estado.exito && <p className="exito">¡Mensaje enviado!</p>}
    </form>
  );
}
```

### useFormStatus: estado del formulario en componentes hijos

```jsx
import { useFormStatus } from 'react-dom';

// Este componente puede vivir en cualquier parte del árbol dentro del <form>
function BotonSubmit() {
  const { pending } = useFormStatus();

  return (
    <button type="submit" disabled={pending}>
      {pending ? 'Procesando...' : 'Enviar'}
    </button>
  );
}

function Formulario() {
  return (
    <form action={miAccion}>
      <input name="dato" />
      <BotonSubmit /> {/* ← Sabe automáticamente si el form está procesando */}
    </form>
  );
}
```

### useOptimistic: actualizaciones optimistas

Las **actualizaciones optimistas** muestran el resultado esperado antes de que la operación del servidor complete, haciendo que la UI se sienta instantánea.

```jsx
import { useOptimistic, useState } from 'react';

async function toggleFavorito(id) {
  await fetch(`/api/favoritos/${id}`, { method: 'POST' });
}

function ListaProductos({ productosIniciales }) {
  const [productos, setProductos] = useState(productosIniciales);
  
  const [productosOptimistas, agregarOptimista] = useOptimistic(
    productos,
    (estadoActual, idToggled) =>
      estadoActual.map(p =>
        p.id === idToggled ? { ...p, favorito: !p.favorito } : p
      )
  );

  const handleToggle = async (id) => {
    // Actualización optimista: muestra el cambio INMEDIATAMENTE
    agregarOptimista(id);
    // Luego sincroniza con el servidor
    await toggleFavorito(id);
    // Si falla, React revierte automáticamente al estado anterior
  };

  return (
    <ul>
      {productosOptimistas.map(p => (
        <li key={p.id}>
          {p.nombre}
          <button onClick={() => handleToggle(p.id)}>
            {p.favorito ? '❤️' : '🤍'}
          </button>
        </li>
      ))}
    </ul>
  );
}
```

> **💡 Tip empresarial:** Las actualizaciones optimistas son estándar en apps como Twitter/X (likes), Slack (mensajes) y Trello (mover tarjetas). Reducen la latencia percibida en un 70-90% para operaciones comunes.

---

## 5. Asset Loading

React 19 introduce soporte nativo para precarga de recursos mediante nuevas APIs del paquete `react-dom`.

### Precarga de recursos críticos

```jsx
import { prefetchDNS, preconnect, preload, preinit } from 'react-dom';

function App() {
  // Precarga del DNS del servidor de imágenes
  prefetchDNS('https://cdn.miempresa.com');
  
  // Establece la conexión anticipada
  preconnect('https://fonts.googleapis.com');
  
  // Precarga una fuente web
  preload('https://fonts.gstatic.com/mifuente.woff2', { as: 'font' });
  
  // Precarga e inicializa un script
  preinit('https://analytics.miempresa.com/tracker.js', { as: 'script' });

  return <main>...</main>;
}
```

### Carga de recursos en componentes

```jsx
function PaginaProducto({ id }) {
  // Inicia la carga del CSS del módulo de carrito anticipadamente
  preinit('/estilos/carrito.css', { as: 'style' });

  return (
    <div>
      <DetalleProducto id={id} />
      <Suspense fallback={<p>Cargando carrito...</p>}>
        <Carrito />
      </Suspense>
    </div>
  );
}
```

---

## 6. Document Metadata

React 19 permite gestionar los metadatos del documento (`<title>`, `<meta>`, `<link>`) directamente desde los componentes, sin necesidad de librerías externas como `react-helmet`.

```jsx
function PaginaProducto({ producto }) {
  return (
    <article>
      {/* React 19 eleva automáticamente estos tags al <head> del documento */}
      <title>{producto.nombre} — Mi Tienda</title>
      <meta name="description" content={producto.descripcion} />
      <meta property="og:image" content={producto.imagen} />
      <link rel="canonical" href={`https://mitienda.com/productos/${producto.slug}`} />

      <h1>{producto.nombre}</h1>
      <p>{producto.descripcion}</p>
    </article>
  );
}
```

### Comparativa con react-helmet

```jsx
// Antes (con react-helmet)
import { Helmet } from 'react-helmet-async';

function Pagina({ titulo }) {
  return (
    <div>
      <Helmet>
        <title>{titulo}</title>
        <meta name="description" content="..." />
      </Helmet>
      <h1>{titulo}</h1>
    </div>
  );
}

// Ahora (React 19 nativo)
function Pagina({ titulo }) {
  return (
    <div>
      <title>{titulo}</title>
      <meta name="description" content="..." />
      <h1>{titulo}</h1>
    </div>
  );
}
```

> **💡 Tip empresarial:** Esta característica simplifica el SEO técnico en aplicaciones React. Ya no necesitas `react-helmet` ni `react-helmet-async`, reduciendo dependencias del proyecto.

---

## 7. React Compiler (Optimización Automática)

### ¿Qué hace exactamente el React Compiler?

El React Compiler analiza tu código en tiempo de compilación y aplica automáticamente las optimizaciones que antes debías hacer manualmente:

| Sin compilador (manual) | Con compilador (automático) |
|------------------------|----------------------------|
| `useMemo(() => calcular(a, b), [a, b])` | `const resultado = calcular(a, b)` |
| `useCallback((x) => fn(x), [fn])` | `const handler = (x) => fn(x)` |
| `export default memo(MiComponente)` | `export default MiComponente` |

### Requisitos para usar el compilador

El React Compiler **solo optimiza código que sigue las reglas de React**:

1. Los componentes deben ser funciones puras.
2. Los hooks deben seguir las Reglas de los Hooks.
3. Las props y el estado no deben mutarse directamente.

```jsx
// ✅ El compilador puede optimizar esto
function Lista({ items }) {
  const itemsOrdenados = [...items].sort((a, b) => a.nombre.localeCompare(b.nombre));
  return <ul>{itemsOrdenados.map(i => <li key={i.id}>{i.nombre}</li>)}</ul>;
}

// ❌ El compilador NO puede optimizar esto (mutación directa)
function ListaMala({ items }) {
  items.sort(); // ← Mutación del prop: esto viola las reglas de React
  return <ul>{items.map(i => <li key={i.id}>{i.nombre}</li>)}</ul>;
}
```

### Verificar qué optimiza el compilador

```bash
# Instalar el playground del compilador
npx react-compiler-healthcheck
```

> **💡 Tip empresarial:** El React Compiler es una razón de peso para migrar a React 19. En benchmarks de aplicaciones reales, reduce el tiempo de re-render en un 20-40% sin cambios de código.

---

## Resumen de las Novedades de React 19

| Característica | Beneficio principal | ¿Cuándo usarla? |
|----------------|---------------------|-----------------|
| React Compiler | Memoización automática | Siempre que sea posible |
| Server Components | Reduce bundle JS, acceso directo a datos | Con Next.js 14+/Remix |
| `useActionState` | Formularios async simplificados | Formularios con servidor |
| `useFormStatus` | Estado del form en subcomponentes | Botones de submit reutilizables |
| `useOptimistic` | UI instantánea con rollback | Likes, favoritos, drag & drop |
| Asset Loading | Precarga de recursos | Páginas con muchos recursos |
| Document Metadata | SEO sin librerías externas | Todas las páginas |
| Concurrent Mode | UI responsiva bajo carga | Apps con renders pesados |

---

## Recursos Adicionales

- [React 19 Release Notes](https://react.dev/blog/2024/12/05/react-19)
- [React Compiler — Documentación](https://react.dev/learn/react-compiler)
- [useActionState — API Reference](https://react.dev/reference/react/useActionState)
- [React Server Components RFC](https://github.com/reactjs/rfcs/blob/main/text/0188-server-components.md)
- [Tanner Linsley — The new React Query + RSC integration](https://tanstack.com/query/latest/docs/framework/react/guides/advanced-ssr)

---

*← [Fundamentos de React](../react-fundamentos/README.md) | [Volver al índice](../README.md) | Siguiente: [JSX en profundidad →](../jsx/README.md)*
