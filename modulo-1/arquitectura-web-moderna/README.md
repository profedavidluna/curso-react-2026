# Arquitectura Web Moderna

La arquitectura que elige un equipo determina el rendimiento, la escalabilidad, la experiencia de desarrollo y los costos operativos de una aplicación. Para un desarrollador empresarial, comprender estos conceptos es fundamental para tomar decisiones técnicas informadas.

---

## 1. Arquitectura de SPAs (Single Page Applications)

### ¿Qué es una SPA?

Una **Single Page Application** es una aplicación web que carga **un único documento HTML** y actualiza dinámicamente el contenido de esa página mediante JavaScript, sin recargar el navegador completo.

### Flujo de una SPA vs una Aplicación Tradicional

```
Aplicación Web Tradicional (Multi-Page):
Usuario → hace clic → Servidor → procesa → envía HTML completo → Navegador muestra nueva página
                                                        (cada clic = descarga completa)

Single Page Application:
Usuario → hace clic → JavaScript → actualiza el DOM → Router cambia la URL
                        ↓
                  Si necesita datos:
                  Fetch a API REST/GraphQL → JSON → Actualiza solo lo que cambió
```

### ✅ Ventajas de las SPAs

| Ventaja | Descripción | Ejemplo |
|---------|-------------|---------|
| **UX tipo app nativa** | Transiciones fluidas sin parpadeo de página | Gmail, Figma, Notion |
| **Separación frontend/backend** | El backend expone APIs que consumen múltiples clientes (web, móvil, etc.) | API usada por React Web + React Native |
| **Desarrollo independiente** | Los equipos de frontend y backend trabajan en paralelo | Entregas más rápidas |
| **Estado rico del cliente** | Fácil gestión de estado complejo (filtros, multi-step forms) | Dashboards de análisis |
| **Carga inicial cacheada** | Los assets JS/CSS se cachean; solo se descargan datos (JSON) en cada navegación | App funciona offline con Service Workers |

### ⚠️ Desventajas y Cuándo No Usar SPAs

| Desventaja | Impacto | Solución |
|-----------|---------|----------|
| **SEO deficiente** (por defecto) | Los motores de búsqueda pueden no indexar el contenido dinámico | SSR o SSG (Next.js) |
| **Tiempo de carga inicial alto** | El usuario espera que descargue el bundle JS completo antes de ver algo | Code Splitting, Lazy Loading |
| **Complejidad de gestión de estado** | El estado del cliente crece y se vuelve difícil de manejar | Redux, Zustand, React Query |
| **Historial y navegación** | El botón "atrás" y los bookmarks requieren configuración del Router | React Router, TanStack Router |

---

## 2. Bundlers y Herramientas de Build

Un **bundler** toma todos tus archivos fuente (JS, CSS, imágenes, fuentes) y los transforma en un bundle optimizado listo para producción.

### ¿Por qué necesitamos un Bundler?

```javascript
// En desarrollo escribimos módulos ES6 modernos:
import React from 'react';
import { format } from 'date-fns';
import styles from './App.module.css';

// Problemas sin bundler:
// 1. Los navegadores no soportan 'node_modules' directamente
// 2. Múltiples peticiones HTTP (1 por archivo) = lento
// 3. JSX no es JavaScript nativo del navegador
// 4. TypeScript necesita compilación

// El bundler resuelve todo esto:
// 1. Resuelve y agrupa dependencias de node_modules
// 2. Transpila JSX y TypeScript a JavaScript estándar
// 3. Genera 1-3 archivos optimizados (bundle)
// 4. Minifica y elimina código muerto (Tree Shaking)
```

---

### Vite vs Webpack: La Comparativa Definitiva

| Característica | **Vite** | **Webpack** |
|---------------|---------|------------|
| **Arquitectura** | ESModules nativos en dev + Rollup en prod | Bundle completo en dev y prod |
| **Velocidad de inicio (dev)** | ⚡ Instantáneo (< 1 segundo) | 🐢 10-60+ segundos (según tamaño) |
| **Hot Module Replacement** | Ultrarrápido (actualiza solo el módulo) | Lento (re-bundlea el módulo y sus dependencias) |
| **Configuración** | Mínima out-of-the-box | Extensa y compleja (`webpack.config.js`) |
| **Ecosistema de plugins** | Creciente (compatible con Rollup) | Masivo y maduro |
| **Bundle de producción** | Rollup (tree-shaking excelente) | Optimizado con varios plugins |
| **Recomendado para** | ✅ Proyectos nuevos, React, Vue | Legacy, configuraciones muy específicas |
| **Usado por** | React (create-react-app migra a Vite), Vue CLI | Create React App (deprecated), grandes empresas con configuraciones complejas |

---

### Conceptos Clave de los Bundlers

```javascript
// 1. CODE SPLITTING — Dividir el bundle en chunks lazy-loaded
// En lugar de un bundle gigante, carga solo lo necesario

// React.lazy + Suspense para code splitting automático
const PaginaReportes = React.lazy(() => import('./pages/PaginaReportes'));
const PaginaDashboard = React.lazy(() => import('./pages/PaginaDashboard'));

function App() {
  return (
    <React.Suspense fallback={<SpinnerCarga />}>
      <Routes>
        <Route path="/dashboard" element={<PaginaDashboard />} />
        <Route path="/reportes" element={<PaginaReportes />} />
        {/* PaginaReportes solo se descarga cuando el usuario navega a /reportes */}
      </Routes>
    </React.Suspense>
  );
}

// 2. TREE SHAKING — Eliminar código no utilizado
// Si importas solo una función de lodash, el bundler omite el resto
import { debounce } from 'lodash-es'; // ✅ Tree-shakeable
import _ from 'lodash';               // ❌ Importa la librería completa (70KB)

// 3. ENVIRONMENT VARIABLES — Configuración por entorno
// Archivo .env.development
// VITE_API_URL=http://localhost:3000

// Archivo .env.production
// VITE_API_URL=https://api.empresa.com

const API_URL = import.meta.env.VITE_API_URL; // Vite
const API_URL = process.env.REACT_APP_API_URL;  // Create React App
```

---

### Configuración Mínima de Vite para un Proyecto React

```javascript
// vite.config.js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import path from 'path';

export default defineConfig({
  plugins: [react()],
  
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'), // Permite import '@/components/Boton'
    },
  },
  
  build: {
    outDir: 'dist',
    sourcemap: false,           // Deshabilitar sourcemaps en producción
    rollupOptions: {
      output: {
        // Dividir vendors en un chunk separado para mejor caché
        manualChunks: {
          vendor: ['react', 'react-dom'],
          router: ['react-router-dom'],
        },
      },
    },
  },
  
  server: {
    port: 3000,
    proxy: {
      '/api': {
        target: 'http://localhost:8080', // Proxy al backend en desarrollo
        changeOrigin: true,
      },
    },
  },
});
```

---

## 3. Estrategias de Rendering: CSR, SSR y SSG

Esta es una de las decisiones arquitectónicas más importantes. Cada estrategia tiene un impacto directo en el **SEO**, el **rendimiento** (Core Web Vitals) y la **experiencia de usuario**.

### Comparativa Visual del Proceso

```
CSR (Client-Side Rendering):
Servidor → HTML vacío + bundle.js → Navegador descarga JS → Ejecuta React → Renderiza UI
         1. HTML llega vacío      2. JS descarga (lento)  3. Finalmente: UI visible

SSR (Server-Side Rendering):
Servidor → Genera HTML + datos → HTML completo llega al navegador → React "hidrata" el HTML
         1. HTML llega completo (rápido) → 2. React añade interactividad (hydration)

SSG (Static Site Generation):
Build time → Genera archivos HTML estáticos → CDN distribuye HTML → Navegador muestra inmediatamente
         1. HTML ya existe → 2. Sin esperar servidor → 3. Instantáneo desde CDN
```

---

### Client-Side Rendering (CSR)

El navegador descarga un HTML mínimo y JavaScript. JavaScript construye toda la UI en el navegador del cliente.

```
Flujo de CSR:
index.html (vacío) → bundle.js (descarga) → React renderiza → UI visible
                     ↑
              El usuario ve un spinner mientras tanto
```

| Aspecto | CSR |
|---------|-----|
| **SEO** | ❌ Malo por defecto (bots ven HTML vacío) |
| **Tiempo hasta primera interacción** | ❌ Lento (bundle.js debe descargarse y ejecutarse) |
| **Tiempo de navegación interno** | ✅ Muy rápido (sin round-trips al servidor) |
| **Costo del servidor** | ✅ Bajo (solo sirve archivos estáticos) |
| **Mejor para** | Dashboards internos autenticados, herramientas SaaS |

```javascript
// Aplicación CSR pura con Vite + React
// index.html
// <div id="root"></div>
// <script type="module" src="/src/main.jsx"></script>

// main.jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

---

### Server-Side Rendering (SSR)

El servidor genera el HTML completo **con los datos incluidos** en cada petición y lo envía al navegador. React luego "hidrata" ese HTML para hacerlo interactivo.

```
Flujo de SSR:
Petición → Servidor procesa → HTML completo con datos → Navegador muestra → React hidrata
                                                         ↑
                                              El usuario ve la UI de inmediato
```

| Aspecto | SSR |
|---------|-----|
| **SEO** | ✅ Excelente (HTML con contenido real) |
| **First Contentful Paint (FCP)** | ✅ Rápido (HTML ya tiene contenido) |
| **Time to Interactive (TTI)** | ⚠️ Depende del bundle de hidratación |
| **Costo del servidor** | ❌ Alto (servidor procesa cada petición) |
| **Complejidad** | ❌ Alta (gestión de estado servidor/cliente) |
| **Mejor para** | E-commerce, blogs, landing pages con SEO crítico |

```javascript
// Next.js — El framework SSR estándar de la industria para React

// Página con SSR (datos frescos en cada petición)
// app/productos/page.jsx
async function PaginaProductos() {
  // Se ejecuta en el servidor, no en el cliente
  const productos = await fetch('https://api.empresa.com/productos', {
    cache: 'no-store' // Sin caché: datos siempre frescos
  }).then(r => r.json());

  return (
    <main>
      <h1>Catálogo de Productos</h1>
      {productos.map(p => <TarjetaProducto key={p.id} producto={p} />)}
    </main>
  );
}
```

---

### Static Site Generation (SSG)

Las páginas HTML se generan en **tiempo de build**, no en tiempo de petición. Los archivos resultantes se sirven desde una CDN.

```
Flujo de SSG:
npm run build → Genera HTML estático → Sube a CDN → Usuario pide página → CDN responde instantáneo
       ↑
  (solo una vez, no por cada petición)
```

| Aspecto | SSG |
|---------|-----|
| **SEO** | ✅ Excelente |
| **Performance** | ✅ Máxima (archivos estáticos en CDN) |
| **Costo del servidor** | ✅ Mínimo (solo CDN) |
| **Datos** | ⚠️ Pueden quedar obsoletos (rebuild necesario para actualizar) |
| **Mejor para** | Blogs, documentación técnica, landing pages de marketing |

```javascript
// Next.js — Página SSG
// app/blog/[slug]/page.jsx

// Genera los paths estáticos en build time
export async function generateStaticParams() {
  const articulos = await fetch('https://api.empresa.com/blog').then(r => r.json());
  return articulos.map(a => ({ slug: a.slug }));
}

async function PaginaArticulo({ params }) {
  const articulo = await fetch(`https://api.empresa.com/blog/${params.slug}`, {
    cache: 'force-cache' // Usar caché: genera el HTML una sola vez
  }).then(r => r.json());

  return (
    <article>
      <h1>{articulo.titulo}</h1>
      <p>{articulo.contenido}</p>
    </article>
  );
}
```

---

### Cuadro de Decisión: ¿Qué Estrategia Usar?

```
¿El contenido cambia frecuentemente Y es personal/por usuario?
│
├── SÍ → ¿El SEO es importante?
│         ├── SÍ → SSR (Next.js con Server Components)
│         └── NO → CSR (SPA con Vite + React Router)
│
└── NO → ¿El SEO es importante?
          ├── SÍ → SSG (Next.js con generateStaticParams)
          └── NO → CSR o SSG (según conveniencia)

Ejemplos prácticos:
- Dashboard de CRM interno: CSR (solo acceden usuarios autenticados, SEO no importa)
- Tienda de e-commerce: SSR o SSG+ISR (SEO crítico, productos cambian)
- Blog corporativo: SSG (contenido estático, SEO importante, bajo costo)
- App de chat en tiempo real: CSR + WebSockets (datos en tiempo real)
- Panel de administración: CSR (privado, no indexable, UI compleja)
```

---

### ISR: Incremental Static Regeneration

Un híbrido entre SSG y SSR, disponible en Next.js: Las páginas se generan estáticamente pero se **regeneran automáticamente** en background cuando cambian los datos.

```javascript
// Next.js — ISR: lo mejor de SSG + datos actualizables
async function PaginaProducto({ params }) {
  const producto = await fetch(`https://api.empresa.com/productos/${params.id}`, {
    next: { revalidate: 3600 } // Regenerar la página cada hora
  }).then(r => r.json());

  return <DetalleProducto producto={producto} />;
}
```

---

## 4. El Papel de APIs y Microservicios

### De la Arquitectura Monolítica a los Microservicios

```
Arquitectura Monolítica (tradicional):
┌─────────────────────────────────────────┐
│  APLICACIÓN MONOLÍTICA                  │
│  ┌──────────┐  ┌──────────┐  ┌───────┐  │
│  │ Usuarios │  │ Pedidos  │  │ Pagos │  │
│  └──────────┘  └──────────┘  └───────┘  │
│  ┌──────────────────────────────────┐   │
│  │        Base de Datos Única        │   │
└──────────────────────────────────────────┘
 ✅ Simple de desarrollar inicialmente
 ❌ Escalar significa escalar todo
 ❌ Un fallo puede tirar todo el sistema

Arquitectura de Microservicios:
┌──────────┐   ┌──────────┐   ┌──────────┐
│ Servicio │   │ Servicio │   │ Servicio │
│ Usuarios │   │ Pedidos  │   │  Pagos   │
│  :3001   │   │  :3002   │   │  :3003   │
└──────────┘   └──────────┘   └──────────┘
      ↑               ↑              ↑
      └───────────────┴──────────────┘
                      ↑
              API Gateway / BFF
                      ↑
              React Frontend (SPA)

 ✅ Escalabilidad independiente por servicio
 ✅ Un fallo en Pagos no afecta a Usuarios
 ✅ Equipos independientes por servicio
 ❌ Complejidad operacional (Kubernetes, Docker)
 ❌ Gestión de transacciones distribuidas
```

---

### APIs REST vs GraphQL

```javascript
// REST API — Estándar de la industria
// Recursos fijos: /api/usuarios/:id, /api/pedidos/:id

// Problema: Over-fetching y Under-fetching
// Si necesitas nombre + avatar del usuario + sus últimos 3 pedidos:
// GET /api/usuarios/1  → { id, nombre, email, telefono, avatar, ... } (over-fetching)
// GET /api/pedidos?usuarioId=1 → [{ id, fecha, items, ... }] (segunda petición)

// GRAPHQL — Flexible, pide exactamente lo que necesitas
const QUERY_PERFIL_USUARIO = `
  query ObtenerPerfilCompleto($usuarioId: ID!) {
    usuario(id: $usuarioId) {
      nombre           # Solo los campos que necesito
      avatar { url }
      pedidos(ultimos: 3) {
        id
        fecha
        total
      }
    }
  }
`;

// Una sola petición, exactamente los datos necesarios
const { data } = await apolloClient.query({
  query: QUERY_PERFIL_USUARIO,
  variables: { usuarioId: '1' }
});
```

---

### El Patrón BFF (Backend for Frontend)

```
Sin BFF — Frontend habla con múltiples microservicios:
React App → Servicio Usuarios (:3001)
React App → Servicio Pedidos (:3002)
React App → Servicio Inventario (:3003)
React App → Servicio Notificaciones (:3004)
Problemas: CORS, múltiples autenticaciones, composición de datos en el cliente

Con BFF — Un punto de entrada orquestado para el frontend:
React App → BFF (API Gateway React) → Servicio Usuarios
                                     → Servicio Pedidos
                                     → Servicio Inventario
Beneficios:
✅ Un solo punto de autenticación
✅ Composición de datos en el servidor (no en el cliente)
✅ Menor payload al frontend
✅ Caché centralizada
✅ Next.js Route Handlers actúan como BFF nativo
```

---

### Patrones de Comunicación con APIs en React

```javascript
// 1. FETCH NATIVO — Wrapper profesional
class APIClient {
  #baseURL;
  #getToken;

  constructor(baseURL, getToken) {
    this.#baseURL = baseURL;
    this.#getToken = getToken;
  }

  async request(endpoint, options = {}) {
    const token = this.#getToken();
    const config = {
      headers: {
        'Content-Type': 'application/json',
        ...(token ? { 'Authorization': `Bearer ${token}` } : {}),
        ...options.headers,
      },
      ...options,
    };

    const response = await fetch(`${this.#baseURL}${endpoint}`, config);

    if (response.status === 401) {
      // Token expirado — redirigir al login
      window.location.href = '/login';
      return;
    }

    if (!response.ok) {
      const errorData = await response.json().catch(() => ({}));
      throw new APIError(response.status, errorData.mensaje || 'Error en la petición');
    }

    return response.status === 204 ? null : response.json();
  }

  get(endpoint) { return this.request(endpoint); }
  post(endpoint, data) { return this.request(endpoint, { method: 'POST', body: JSON.stringify(data) }); }
  put(endpoint, data) { return this.request(endpoint, { method: 'PUT', body: JSON.stringify(data) }); }
  delete(endpoint) { return this.request(endpoint, { method: 'DELETE' }); }
}

// 2. REACT QUERY / TANSTACK QUERY — El estándar de la industria para APIs
// Maneja caché, revalidación, estados de carga/error automáticamente
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';

function ListaEmpleados() {
  const { data: empleados, isLoading, error } = useQuery({
    queryKey: ['empleados'],           // Clave de caché única
    queryFn: () => apiClient.get('/empleados'),
    staleTime: 5 * 60 * 1000,         // Los datos son "frescos" por 5 minutos
    refetchOnWindowFocus: true,        // Re-fetch al volver a la pestaña
  });

  const queryClient = useQueryClient();
  const crearEmpleado = useMutation({
    mutationFn: (datos) => apiClient.post('/empleados', datos),
    onSuccess: () => {
      // Invalida la caché para forzar re-fetch de la lista
      queryClient.invalidateQueries({ queryKey: ['empleados'] });
    },
  });

  if (isLoading) return <SkeletonLista />;
  if (error) return <MensajeError error={error} />;

  return (
    <>
      <ul>{empleados.map(e => <li key={e.id}>{e.nombre}</li>)}</ul>
      <button onClick={() => crearEmpleado.mutate({ nombre: 'Nuevo Empleado' })}>
        Agregar
      </button>
    </>
  );
}
```

---

### Comunicación en Tiempo Real

```javascript
// WebSockets — Para datos que cambian constantemente
function useCotizacionEnVivo(simbolo) {
  const [cotizacion, setCotizacion] = React.useState(null);

  React.useEffect(() => {
    const ws = new WebSocket(`wss://api.empresa.com/cotizaciones/${simbolo}`);
    
    ws.onmessage = (event) => {
      const datos = JSON.parse(event.data);
      setCotizacion(datos);
    };

    ws.onerror = (error) => console.error('Error WebSocket:', error);

    // Limpiar la conexión al desmontar el componente
    return () => ws.close();
  }, [simbolo]);

  return cotizacion;
}

// Server-Sent Events (SSE) — Solo del servidor al cliente (notificaciones, logs en tiempo real)
function useNotificaciones(usuarioId) {
  const [notificaciones, setNotificaciones] = React.useState([]);

  React.useEffect(() => {
    const eventSource = new EventSource(`/api/notificaciones/${usuarioId}`);
    
    eventSource.onmessage = (event) => {
      const nuevaNotif = JSON.parse(event.data);
      setNotificaciones(prev => [nuevaNotif, ...prev]);
    };

    return () => eventSource.close();
  }, [usuarioId]);

  return notificaciones;
}
```

---

## 📚 Recursos Adicionales

| Recurso | Descripción | Nivel |
|---------|-------------|-------|
| [Next.js Documentation](https://nextjs.org/docs) | El framework React líder para SSR/SSG/ISR | Intermedio |
| [Vite Documentation](https://vitejs.dev/guide/) | Guía oficial de Vite | Básico |
| [TanStack Query Docs](https://tanstack.com/query/latest) | El estándar para manejo de estado servidor | Intermedio |
| [web.dev: Core Web Vitals](https://web.dev/articles/vitals) | Métricas de rendimiento de Google | Intermedio |
| [Architecture of Modern Web Apps](https://docs.microsoft.com/en-us/dotnet/architecture/modern-web-apps-azure/) | Guía de arquitectura de Microsoft | Avanzado |
| [Patterns.dev](https://www.patterns.dev/) | Patrones de diseño para aplicaciones web modernas | Avanzado |
| [The System Design Primer](https://github.com/donnemartin/system-design-primer) | Guía de diseño de sistemas distribuidos | Avanzado |
| [REST API Design Best Practices](https://www.freecodecamp.org/news/rest-api-best-practices-rest-endpoint-design-examples/) | Buenas prácticas de diseño de APIs REST | Intermedio |
