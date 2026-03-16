# Ejercicios Resueltos — Módulo I: Fundamentos Web Modernos

> ⚠️ **Nota para los estudiantes:** Intenta resolver los ejercicios por tu cuenta antes de consultar esta sección. Las soluciones aquí son de referencia, pueden existir múltiples soluciones igualmente válidas.

---

## ✅ Ejercicio 1.1: HTML Semántico — Solución

**Problemas identificados en el código original:**

1. `<div class="page">` → debe ser el layout semántico usando `<body>` y elementos de layout
2. `<div class="top">` → debe ser `<header>`
3. `<div class="logo">` → debería ser `<a>` o `<h1>` dentro del header
4. `<div class="menu">` → debe ser `<nav>`
5. Los enlaces de navegación deberían estar dentro de `<ul>` con `<li>`
6. `<div class="content">` → debe ser `<main>`
7. `<div class="post">` → es contenido autónomo: debe ser `<article>`
8. `<div class="title">` → debe ser un encabezado `<h1>` o `<h2>`
9. `<div class="body">` → debe ser `<p>` (párrafo)
10. `<div class="tags">` → debe ser `<ul>` con `<li>` o `<ul>` con rol de tags
11. Cada `<div>` de tag → debe ser `<li>` o `<span>` con `role="listitem"`
12. `<div class="bottom">` → debe ser `<footer>`
13. La imagen de Twitter no tiene atributo `alt` — **violación crítica de accesibilidad**

```html
<!-- ✅ Versión corregida -->
<body>
  <header>
    <a href="/" aria-label="Ir a la página de inicio de MiEmpresa">
      <span aria-hidden="true">🏢</span> MiEmpresa
    </a>
    <nav aria-label="Menú principal">
      <ul>
        <li><a href="/inicio">Inicio</a></li>
        <li><a href="/productos">Productos</a></li>
        <li><a href="/contacto">Contacto</a></li>
      </ul>
    </nav>
  </header>

  <main>
    <article>
      <h1>Lanzamos React 19</h1>
      <p>React 19 es la versión más nueva...</p>
      <ul aria-label="Etiquetas del artículo">
        <li>React</li>
        <li>JavaScript</li>
      </ul>
    </article>
  </main>

  <footer>
    <p>© 2025 MiEmpresa</p>
    <a 
      href="https://twitter.com/miempresa" 
      target="_blank" 
      rel="noopener noreferrer"
      aria-label="Síguenos en Twitter (abre en nueva pestaña)"
    >
      <img src="twitter.png" alt="Twitter">
    </a>
  </footer>
</body>
```

---

## ✅ Ejercicio 1.2: Formulario Empresarial — Solución

```jsx
// components/FormularioNuevoEmpleado.jsx
function FormularioNuevoEmpleado({ onGuardar }) {
  const handleSubmit = (e) => {
    e.preventDefault();
    const formData = new FormData(e.target);
    const datos = Object.fromEntries(formData.entries());
    onGuardar(datos);
  };

  // Calcular la fecha mínima (hoy)
  const hoy = new Date().toISOString().split('T')[0];

  return (
    <form onSubmit={handleSubmit} noValidate aria-labelledby="form-titulo">
      <h2 id="form-titulo">Registro de Nuevo Empleado</h2>

      <fieldset>
        <legend>Información Personal</legend>

        <div>
          <label htmlFor="nombre">Nombre Completo *</label>
          <input
            id="nombre"
            name="nombre"
            type="text"
            required
            minLength={3}
            maxLength={100}
            placeholder="Ej: Ana García López"
            aria-describedby="nombre-ayuda"
            autoComplete="name"
          />
          <small id="nombre-ayuda">Ingresa tu nombre completo como aparece en tu identificación oficial</small>
        </div>

        <div>
          <label htmlFor="email">Correo Corporativo *</label>
          <input
            id="email"
            name="email"
            type="email"
            required
            pattern=".*@empresa\.com$"
            placeholder="tunombre@empresa.com"
            aria-describedby="email-ayuda"
            autoComplete="work email"
          />
          <small id="email-ayuda">Solo se aceptan correos con dominio @empresa.com</small>
        </div>

        <div>
          <label htmlFor="telefono">Teléfono *</label>
          <input
            id="telefono"
            name="telefono"
            type="tel"
            required
            pattern="[0-9]{10}"
            placeholder="5512345678"
            aria-describedby="telefono-ayuda"
            autoComplete="tel-national"
          />
          <small id="telefono-ayuda">10 dígitos sin espacios ni guiones</small>
        </div>
      </fieldset>

      <fieldset>
        <legend>Información Laboral</legend>

        <div>
          <label htmlFor="departamento">Departamento *</label>
          <select id="departamento" name="departamento" required>
            <option value="">-- Selecciona un departamento --</option>
            <option value="ingenieria">Ingeniería</option>
            <option value="ventas">Ventas</option>
            <option value="rh">Recursos Humanos</option>
            <option value="finanzas">Finanzas</option>
          </select>
        </div>

        <div>
          <label htmlFor="fechaInicio">Fecha de Inicio *</label>
          <input
            id="fechaInicio"
            name="fechaInicio"
            type="date"
            required
            min={hoy}
            aria-describedby="fecha-ayuda"
          />
          <small id="fecha-ayuda">La fecha de inicio no puede ser anterior a hoy</small>
        </div>

        <fieldset>
          <legend>Nivel de Seniority *</legend>
          {['Junior', 'Mid', 'Senior'].map(nivel => (
            <label key={nivel}>
              <input
                type="radio"
                name="nivel"
                value={nivel.toLowerCase()}
                required
              />
              {nivel}
            </label>
          ))}
        </fieldset>
      </fieldset>

      <div>
        <label htmlFor="notas">Notas Adicionales</label>
        <textarea
          id="notas"
          name="notas"
          rows={4}
          maxLength={500}
          placeholder="Información adicional relevante (opcional)"
        />
      </div>

      <div>
        <label>
          <input type="checkbox" name="aceptaPolitica" required />
          Acepto la{' '}
          <a href="/politica-datos" target="_blank" rel="noopener noreferrer">
            política de tratamiento de datos personales
          </a>
          {' '}*
        </label>
      </div>

      <button type="submit">Registrar Empleado</button>
    </form>
  );
}
```

---

## ✅ Ejercicio 1.3: useLocalStorage — Solución

```javascript
// hooks/useLocalStorage.js

/**
 * Hook para persistir estado en localStorage.
 * @param {string} key - Clave de localStorage
 * @param {*} defaultValue - Valor por defecto si la clave no existe
 * @returns {[*, function]} - [valor actual, función para actualizar]
 */
function useLocalStorage(key, defaultValue) {
  // Inicializar desde localStorage (se ejecuta solo una vez gracias al callback de useState)
  const [storedValue, setStoredValue] = React.useState(() => {
    try {
      const item = localStorage.getItem(key);
      return item !== null ? JSON.parse(item) : defaultValue;
    } catch (error) {
      console.warn(`Error al leer localStorage["${key}"]:`, error);
      return defaultValue;
    }
  });

  const setValue = React.useCallback((value) => {
    try {
      // Permite pasar una función actualizadora (como setState)
      const valueToStore = value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (error) {
      console.warn(`Error al escribir localStorage["${key}"]:`, error);
    }
  }, [key, storedValue]);

  const removeValue = React.useCallback(() => {
    try {
      setStoredValue(defaultValue);
      localStorage.removeItem(key);
    } catch (error) {
      console.warn(`Error al eliminar localStorage["${key}"]:`, error);
    }
  }, [key, defaultValue]);

  return [storedValue, setValue, removeValue];
}

// components/PreferenciasPanel.jsx
function PreferenciasPanel() {
  const [tema, setTema] = useLocalStorage('preferencias-tema', 'claro');
  const [idioma, setIdioma] = useLocalStorage('preferencias-idioma', 'es');
  const [elementosPorPagina, setElementosPorPagina] = useLocalStorage('preferencias-paginacion', 25);

  // Aplicar el tema al documento cuando cambia
  React.useEffect(() => {
    document.documentElement.setAttribute('data-theme', tema);
  }, [tema]);

  return (
    <section aria-labelledby="prefs-titulo">
      <h2 id="prefs-titulo">Preferencias</h2>

      <div>
        <label htmlFor="tema">Tema</label>
        <select id="tema" value={tema} onChange={e => setTema(e.target.value)}>
          <option value="claro">☀️ Claro</option>
          <option value="oscuro">🌙 Oscuro</option>
        </select>
      </div>

      <div>
        <label htmlFor="idioma">Idioma</label>
        <select id="idioma" value={idioma} onChange={e => setIdioma(e.target.value)}>
          <option value="es">🇲🇽 Español</option>
          <option value="en">🇺🇸 English</option>
          <option value="pt">🇧🇷 Português</option>
        </select>
      </div>

      <div>
        <label htmlFor="paginacion">Elementos por página</label>
        <select id="paginacion" value={elementosPorPagina} onChange={e => setElementosPorPagina(Number(e.target.value))}>
          <option value={10}>10</option>
          <option value={25}>25</option>
          <option value={50}>50</option>
        </select>
      </div>
    </section>
  );
}
```

---

## ✅ Ejercicio 3.1: Refactorización ES6+ — Solución

```javascript
// ✅ Código refactorizado con ES6+
const empleados = [
  { nombre: 'Ana', departamento: 'Engineering', salario: 75000, activo: true },
  { nombre: 'Carlos', departamento: 'Sales', salario: 50000, activo: false },
  { nombre: 'María', departamento: 'Engineering', salario: 82000, activo: true },
  { nombre: 'Juan', departamento: 'HR', salario: 60000, activo: true },
];

// 1. Nombres de empleados activos
const nombresActivos = empleados
  .filter(({ activo }) => activo)
  .map(({ nombre }) => nombre);
// ['Ana', 'María', 'Juan']

// 2. Salario promedio de Ingeniería
const empleadosIngenieria = empleados.filter(({ departamento }) => departamento === 'Engineering');
const promedioIngenieria = empleadosIngenieria.reduce((acc, { salario }) => acc + salario, 0) / empleadosIngenieria.length;
// 78500

// Versión alternativa con un solo reduce (más eficiente)
const { total, count } = empleados.reduce(
  (acc, { departamento, salario }) =>
    departamento === 'Engineering'
      ? { total: acc.total + salario, count: acc.count + 1 }
      : acc,
  { total: 0, count: 0 }
);
const promedioIngenieria2 = total / count;

// 3. Empleados activos con bono
const conBono = empleados
  .filter(({ activo }) => activo)
  .map(empleado => ({
    ...empleado,                        // Spread: copia todas las propiedades
    bono: empleado.salario * 0.1,       // Añade la propiedad nueva
  }));
```

---

## ✅ Ejercicio 3.2: Peticiones Paralelas — Solución

```javascript
// utils/dashboard.js

class APIError extends Error {
  constructor(status, message) {
    super(message);
    this.status = status;
    this.name = 'APIError';
  }
}

async function fetchConFallback(url, fallback) {
  try {
    const response = await fetch(url);
    if (response.status === 404) return fallback;
    if (!response.ok) throw new APIError(response.status, `Error ${response.status} en ${url}`);
    return response.json();
  } catch (error) {
    if (error instanceof APIError) throw error;
    throw new APIError(0, `Error de red: ${error.message}`);
  }
}

async function cargarDatosDashboard(usuarioId) {
  const resultados = await Promise.allSettled([
    fetchConFallback(`/api/usuarios/${usuarioId}`, null),
    fetchConFallback(`/api/notificaciones?usuarioId=${usuarioId}`, []),
    fetchConFallback(`/api/metricas?usuarioId=${usuarioId}`, {}),
  ]);

  // Verificar si todas fallaron
  const todasFallaron = resultados.every(r => r.status === 'rejected');
  if (todasFallaron) {
    throw new Error('No se pudo cargar ningún dato del dashboard. Verifica tu conexión.');
  }

  const [usuarioResult, notificacionesResult, metricasResult] = resultados;

  return {
    usuario: usuarioResult.status === 'fulfilled' ? usuarioResult.value : null,
    notificaciones: notificacionesResult.status === 'fulfilled' ? notificacionesResult.value : [],
    metricas: metricasResult.status === 'fulfilled' ? metricasResult.value : {},
  };
}

// hooks/useDashboard.js
function useDashboard(usuarioId) {
  const [estado, setEstado] = React.useState({
    datos: null,
    cargando: true,
    error: null,
  });

  React.useEffect(() => {
    if (!usuarioId) return;

    let cancelado = false; // Evita actualizar estado si el componente se desmontó

    async function cargar() {
      setEstado({ datos: null, cargando: true, error: null });
      try {
        const datos = await cargarDatosDashboard(usuarioId);
        if (!cancelado) {
          setEstado({ datos, cargando: false, error: null });
        }
      } catch (error) {
        if (!cancelado) {
          setEstado({ datos: null, cargando: false, error: error.message });
        }
      }
    }

    cargar();

    return () => { cancelado = true; }; // Cleanup
  }, [usuarioId]);

  return estado;
}
```

---

## ✅ Ejercicio 3.3: Pipeline de Datos — Solución

```javascript
// 1. Total de ingresos por categoría
function totalPorCategoria() {
  return ventas.reduce((grupos, { categoria, precio, cantidad }) => ({
    ...grupos,
    [categoria]: (grupos[categoria] ?? 0) + (precio * cantidad),
  }), {});
}
// { 'Electrónica': 10200, 'Muebles': 7300 }

// 2. Vendedor con más ingresos
function topVendedor() {
  const ingresosPorVendedor = ventas.reduce((acc, { vendedor, precio, cantidad }) => ({
    ...acc,
    [vendedor]: (acc[vendedor] ?? 0) + (precio * cantidad),
  }), {});

  return Object.entries(ingresosPorVendedor)
    .sort(([, a], [, b]) => b - a)[0][0];
}
// 'Ana' (total: 9600 vs Carlos: 6300, María: 3000)

// 3. Productos en rango de precio
function productosEnRango(min, max) {
  return ventas
    .filter(({ precio }) => precio >= min && precio <= max)
    .sort((a, b) => a.precio - b.precio)
    .map(({ producto, precio }) => ({ producto, precio }));
}

// 4. Resumen del reporte
function resumenReporte() {
  const totalVentas = ventas.reduce((sum, { precio, cantidad }) => sum + (precio * cantidad), 0);
  const promedioVenta = totalVentas / ventas.length;
  
  const productoMasVendido = ventas
    .slice()
    .sort((a, b) => (b.precio * b.cantidad) - (a.precio * a.cantidad))[0].producto;

  const categorias = totalPorCategoria();
  const mejorCategoria = Object.entries(categorias)
    .sort(([, a], [, b]) => b - a)[0][0];

  return {
    totalVentas,
    promedioVenta: Math.round(promedioVenta),
    productoMasVendido,
    mejorCategoria,
  };
}
```

---

## ✅ Ejercicio 4.1: Decisiones de Arquitectura — Solución

### 1. HRMS (Gestión de Recursos Humanos)
- ✅ **Estrategia:** CSR (SPA pura)
- 📋 **Justificación:** Solo acceden usuarios autenticados, el SEO es irrelevante. La interfaz es compleja (tablas, formularios multi-step, filtros) y se beneficia del estado rico del cliente. Los datos son personalizados por usuario.
- 🛠️ **Stack:** Vite + React + React Router + TanStack Query + Zustand
- ⚠️ **Riesgo:** Tiempo de carga inicial. Mitigar con code splitting agresivo.

### 2. E-commerce B2B
- ✅ **Estrategia:** SSR + SSG híbrido (Next.js)
- 📋 **Justificación:** El catálogo público necesita SEO. Las páginas de producto pueden ser SSG+ISR (se regeneran cuando cambian precios). El carrito y checkout son interacciones de cliente (CSR). Los precios personalizados por cliente requieren SSR.
- 🛠️ **Stack:** Next.js + TypeScript + Prisma/PostgreSQL + Stripe
- ⚠️ **Riesgo:** Complejidad del manejo de estado entre servidor y cliente. Requiere arquitectura BFF.

### 3. Blog Técnico
- ✅ **Estrategia:** SSG (Generación estática completa)
- 📋 **Justificación:** El contenido cambia pocas veces por semana. SEO es crítico para visibilidad. SSG ofrece máximo rendimiento y mínimo costo (CDN). Un rebuild al publicar cada artículo es aceptable.
- 🛠️ **Stack:** Next.js + MDX + Vercel (deploy automático en merge a main)
- ⚠️ **Riesgo:** Si el número de artículos crece mucho (miles), el tiempo de build puede aumentar. Usar ISR para artículos más antiguos.

### 4. Dashboard de Análisis en Tiempo Real
- ✅ **Estrategia:** CSR con WebSockets o SSE
- 📋 **Justificación:** Datos en tiempo real no se pueden prerenderizar. Solo acceden usuarios autenticados (SEO irrelevante). Requiere conexión persistente al servidor para actualizaciones push.
- 🛠️ **Stack:** Vite + React + TanStack Query + Socket.io o SSE + Chart.js/Recharts
- ⚠️ **Riesgo:** Gestión de reconexiones WebSocket, consumo de memoria con muchos suscriptores activos.
