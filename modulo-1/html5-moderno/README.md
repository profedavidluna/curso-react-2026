# HTML5 Moderno

## 1. Semántica y Accesibilidad (a11y)

Escribir HTML semántico significa usar las etiquetas HTML correctas para su propósito previsto. Esto no solo mejora el **SEO**, sino que otorga **accesibilidad gratuita** para lectores de pantalla y tecnologías de asistencia.

> 💡 **Principio clave:** El HTML semántico es la base de una aplicación accesible. Un buen árbol de accesibilidad reduce al mínimo la necesidad de atributos ARIA adicionales.

---

### 🏗️ Etiquetas de Layout (Landmarks)

| Etiqueta | Para qué sirve | Atributos clave | Ejemplo |
|----------|---------------|-----------------|---------|
| `<html>` | Raíz del documento HTML | `lang` — idioma del documento (vital para lectores de pantalla y SEO) | `<html lang="es">` |
| `<head>` | Metadatos, CSS, fuentes, scripts (no visible) | — | `<head><title>App</title></head>` |
| `<meta>` | Define metadatos: descripción, codificación, viewport | `charset`, `name`, `content` | `<meta charset="UTF-8">` |
| `<title>` | Título de la pestaña del navegador | — | `<title>Dashboard Corporativo</title>` |
| `<body>` | Todo el contenido visible de la página | `class`, `id` | `<body>...</body>` |

---

### 🏛️ Semántica y Estructura de la Página

| Etiqueta | Para qué sirve | Atributos clave | Ejemplo |
|----------|---------------|-----------------|---------|
| `<header>` | Cabecera de la página o sección (logos, menús) | `class`, `id` | `<header><h1>Mi Logo</h1></header>` |
| `<main>` | Contenido principal y único del documento (solo uno por página) | `class`, `id` | `<main><article>...</article></main>` |
| `<footer>` | Pie de página (copyright, links legales) | `class`, `id` | `<footer><p>© 2025 Empresa</p></footer>` |
| `<nav>` | Bloque de navegación principal o secundaria | `aria-label` | `<nav aria-label="Menú principal">...</nav>` |
| `<section>` | Agrupa contenido temático relacionado, idealmente con encabezado | `class`, `id` | `<section id="precios"><h2>Precios</h2></section>` |
| `<article>` | Contenido autónomo que tiene sentido por sí mismo (un post, tarjeta de producto) | `class`, `id` | `<article><h3>Nuevo React 19</h3></article>` |
| `<aside>` | Contenido secundario o complementario (sidebar, publicidad) | `class`, `id` | `<aside><p>Artículos relacionados</p></aside>` |
| `<div>` | Contenedor genérico **sin valor semántico** (para CSS/JS) | `class`, `id` | `<div class="flex-container">...</div>` |

---

### 📝 Texto y Formato

| Etiqueta | Para qué sirve | Atributos clave | Ejemplo |
|----------|---------------|-----------------|---------|
| `<h1>` a `<h6>` | Encabezados jerárquicos. `<h1>` es el título principal (uno por página) | atributos globales | `<h1>Perfil</h1><h2>Datos</h2>` |
| `<p>` | Párrafo de texto | atributos globales | `<p>Bienvenido al sistema.</p>` |
| `<strong>` | Texto de **gran importancia** semántica (negrita) | atributos globales | `<p><strong>Error:</strong> Campo vacío</p>` |
| `<em>` | Énfasis semántico (cursiva) | atributos globales | `<p>Debes leer <em>todos</em> los términos.</p>` |
| `<span>` | Contenedor inline sin semántica (para CSS/JS) | `class`, `id` | `<span class="badge">Nuevo</span>` |
| `<br>` | Salto de línea (sin etiqueta de cierre) | — | `<p>Av. Principal 123<br>Madrid, España</p>` |

---

### 🖼️ Multimedia y Enlaces

| Etiqueta | Para qué sirve | Atributos clave | Ejemplo |
|----------|---------------|-----------------|---------|
| `<a>` | Hipervínculo hacia otras páginas, secciones o emails | `href` (URL destino), `target` (`_blank` para nueva pestaña), `rel` | `<a href="/contacto" target="_blank" rel="noopener noreferrer">Contacto</a>` |
| `<img>` | Inserta una imagen | `src` (ruta), `alt` (**OBLIGATORIO** para SEO y accesibilidad), `loading="lazy"` | `<img src="logo.png" alt="Logotipo corporativo" loading="lazy">` |
| `<video>` | Incrusta video nativo | `src`, `controls`, `autoplay`, `muted`, `poster` | `<video src="demo.mp4" controls poster="thumbnail.jpg"></video>` |
| `<audio>` | Incrusta audio nativo | `src`, `controls`, `autoplay` | `<audio src="podcast.mp3" controls></audio>` |
| `<picture>` | Contenedor para imágenes responsivas con múltiples fuentes | — | `<picture><source media="(min-width:800px)" srcset="large.jpg"><img src="small.jpg" alt="..."></picture>` |

> ⚠️ **Buena práctica empresarial:** Siempre añadir `rel="noopener noreferrer"` en enlaces con `target="_blank"` para prevenir ataques de tipo **tabnabbing**.

---

### 📋 Listas y Tablas

| Etiqueta | Para qué sirve | Atributos clave | Ejemplo |
|----------|---------------|-----------------|---------|
| `<ul>` | Lista desordenada (viñetas) | atributos globales | `<ul><li>React</li><li>Vue</li></ul>` |
| `<ol>` | Lista ordenada (números) | `start` (número inicial) | `<ol start="3"><li>Tres</li></ol>` |
| `<li>` | Elemento de lista | `value` (en `<ol>`) | `<li>Elemento</li>` |
| `<table>` | Tabla de datos tabulares | atributos globales | `<table>...</table>` |
| `<thead>`, `<tbody>`, `<tfoot>` | Secciones semánticas de la tabla | — | `<thead><tr><th>Nombre</th></tr></thead>` |
| `<tr>` | Fila de tabla | atributos globales | `<tr><td>Ana</td></tr>` |
| `<th>` | Celda de cabecera | `scope` (`col`/`row`), `colspan`, `rowspan` | `<th scope="col">Nombre</th>` |
| `<td>` | Celda de datos | `colspan`, `rowspan` | `<td>Ana García</td>` |

---

## 2. Formularios Avanzados y Validación

Los formularios son la principal vía de interacción del usuario con las reglas de negocio de la empresa.

### 🏷️ Etiquetas y Atributos de Formulario HTML5

| Etiqueta | Para qué sirve | Atributos clave | Ejemplo |
|----------|---------------|-----------------|---------|
| `<form>` | Envoltorio del formulario | `action` (URL destino), `method` (`get`/`post`), `novalidate` | `<form action="/api/login" method="post">` |
| `<fieldset>` | Agrupa campos relacionados dentro de un formulario | atributos globales | `<fieldset><legend>Datos de Contacto</legend>...</fieldset>` |
| `<legend>` | Título descriptivo de un `<fieldset>` | atributos globales | `<legend>Información Personal</legend>` |
| `<label>` | Etiqueta asociada a un campo (**fundamental para accesibilidad**) | `for` (o `htmlFor` en JSX) | `<label for="email">Correo:</label>` |
| `<input>` | Campo de entrada genérico, adaptable por `type` | Ver tabla de tipos abajo | `<input type="email" required>` |
| `<textarea>` | Campo de texto de múltiples líneas | `rows`, `cols`, `placeholder` | `<textarea rows="5" placeholder="Comentarios"></textarea>` |
| `<button>` | Botón presionable | `type` (`submit`/`button`/`reset`), `disabled` | `<button type="submit">Guardar</button>` |
| `<select>` / `<option>` | Menú desplegable y sus opciones | `multiple`, `value` | `<select><option value="admin">Admin</option></select>` |

---

### 📋 Tipos de Input Avanzados en HTML5

Usar el `type` correcto activa el **teclado apropiado en móviles** y añade **validación nativa**:

| Tipo | Caso de uso | Validación nativa |
|------|------------|-------------------|
| `type="text"` | Nombre, apellido, ciudad | Ninguna por defecto |
| `type="email"` | Correo electrónico corporativo | Valida formato de email |
| `type="tel"` | Número de teléfono | Activa teclado numérico en móvil |
| `type="url"` | Sitio web | Valida formato de URL |
| `type="number"` | Cantidades, edades | Permite `min`, `max`, `step` |
| `type="date"` | Fecha de nacimiento, fecha de evento | Selector nativo de fecha |
| `type="datetime-local"` | Fecha y hora combinadas | Selector nativo |
| `type="password"` | Contraseñas | Oculta el texto |
| `type="checkbox"` | Selección múltiple (acepto términos) | — |
| `type="radio"` | Selección única en un grupo | — |
| `type="file"` | Subida de archivos | `accept` filtra tipos de archivo |
| `type="range"` | Slider (volumen, porcentaje) | `min`, `max`, `step` |
| `type="search"` | Campo de búsqueda | Puede mostrar botón de borrar |
| `type="color"` | Selector de color | Abre selector nativo |
| `type="hidden"` | Datos ocultos (CSRF tokens) | — |

---

### ✅ Atributos de Validación Nativa HTML5

```html
<!-- Validación nativa poderosa -->
<input 
  type="email"
  required                          <!-- Campo obligatorio -->
  pattern=".*@miempresa\.com"       <!-- Expresión regular personalizada -->
  minlength="10"                    <!-- Longitud mínima del texto -->
  maxlength="100"                   <!-- Longitud máxima -->
  autocomplete="email"              <!-- Ayuda al autocompletado del navegador -->
  aria-describedby="emailHelp"      <!-- Asocia un mensaje de ayuda -->
>
<small id="emailHelp">Solo se permiten correos corporativos @miempresa.com</small>
```

---

### ♿ Accesibilidad en Formularios

```jsx
// ✅ CORRECTO: Formulario accesible en React
function FormularioContacto() {
  const handleSubmit = (e) => {
    e.preventDefault();
    // Lógica de envío
  };

  return (
    <form onSubmit={handleSubmit} noValidate>
      <fieldset>
        <legend>Datos de Contacto</legend>

        {/* ✅ label asociado via htmlFor + id */}
        <label htmlFor="userEmail">Correo Corporativo</label>
        <input
          type="email"
          id="userEmail"
          name="email"
          required
          pattern=".*@miempresa\.com"
          aria-label="Ingrese su correo corporativo"
          aria-describedby="emailError"
          autoComplete="email"
        />
        <span id="emailError" role="alert" aria-live="polite">
          {/* Mensaje de error dinámico aquí */}
        </span>
      </fieldset>

      <button type="submit">Enviar</button>
    </form>
  );
}
```

> 💡 **Tip Profesional:** Aunque en React usamos librerías como **React Hook Form** o **Formik** para validación compleja, la **validación nativa de HTML5** debe ser tu primera línea de defensa. Es gratuita, sin dependencias y accesible por defecto.

---

### 🔴 El Problema del "Div Soup" y los Fragmentos de React

Los desarrolladores React suelen envolver elementos en `<div>` para satisfacer la regla del único nodo raíz. Esto rompe la semántica HTML, especialmente dentro de `<ul>`, `<ol>`, `<table>` o `<select>`.

```jsx
// ❌ MAL: El <div> extra rompe la semántica de <ul>
function ListaItems({ items }) {
  return items.map(item => (
    <div key={item.id}>       {/* <div> dentro de <ul> es inválido */}
      <li>{item.nombre}</li>
    </div>
  ));
}

// ✅ BIEN: Usar Fragment no inyecta nodos al DOM
function ListaItems({ items }) {
  return items.map(item => (
    <React.Fragment key={item.id}>
      <li>{item.nombre}</li>
    </React.Fragment>
  ));
}
```

---

## 3. APIs Web Nativas: Storage, Drag & Drop, Geolocation

El navegador moderno es un sistema operativo en sí mismo. Usar sus APIs integradas **reduce la necesidad de librerías de terceros**, disminuyendo el tamaño del bundle y las dependencias de seguridad.

---

### 💾 Web Storage API

Mecanismos para persistir datos en el cliente sin necesidad de un servidor.

| API | Duración | Alcance | Tamaño máx. | Caso de uso |
|-----|----------|---------|-------------|-------------|
| `sessionStorage` | Hasta que se cierra la pestaña | Solo esa pestaña | ~5 MB | Estado de formulario multi-paso (Wizard) |
| `localStorage` | Sin caducidad (persistente) | Todo el origen | ~5 MB | Preferencias de usuario (tema oscuro/claro), carrito de compras invitado |
| `cookies` | Configurable (expires/max-age) | Configurable (domain/path) | ~4 KB | Tokens de sesión (con `HttpOnly` + `Secure`) |
| `IndexedDB` | Sin caducidad | Todo el origen | Sin límite fijo | Datos grandes estructurados, apps offline-first |

```javascript
// ✅ Uso correcto de localStorage para preferencias de usuario
const ThemeManager = {
  save: (theme) => {
    try {
      localStorage.setItem('user-theme', theme);
    } catch (error) {
      // localStorage puede fallar en modo incógnito o si está lleno
      console.warn('No se pudo guardar el tema:', error);
    }
  },
  load: () => {
    return localStorage.getItem('user-theme') ?? 'light';
  },
  clear: () => {
    localStorage.removeItem('user-theme');
  }
};

// ✅ Hook de React para preferencias persistentes
function useLocalStorage(key, defaultValue) {
  const [value, setValue] = React.useState(() => {
    try {
      const stored = localStorage.getItem(key);
      return stored ? JSON.parse(stored) : defaultValue;
    } catch {
      return defaultValue;
    }
  });

  const setStoredValue = (newValue) => {
    try {
      setValue(newValue);
      localStorage.setItem(key, JSON.stringify(newValue));
    } catch (error) {
      console.warn(`Error al guardar "${key}" en localStorage:`, error);
    }
  };

  return [value, setStoredValue];
}
```

> 🚨 **Seguridad Empresarial CRÍTICA:** **NUNCA** guardes tokens de sesión (JWT) ni información PII (Datos de Identificación Personal) en `localStorage`. Es vulnerable a ataques **XSS** (Cross-Site Scripting). Los tokens de autenticación deben guardarse en **cookies `HttpOnly` + `Secure`**, que son inaccesibles desde JavaScript.

---

### 🖱️ Drag and Drop API

Permite crear interfaces arrastrables nativas sin librerías adicionales.

```jsx
// Ejemplo: Área de carga de archivos por arrastre (empresa)
function AreaCargaDocumentos({ onArchivosCargados }) {
  const [isDragOver, setIsDragOver] = React.useState(false);

  const handleDragOver = (e) => {
    e.preventDefault(); // OBLIGATORIO para habilitar el drop
    setIsDragOver(true);
  };

  const handleDragLeave = () => setIsDragOver(false);

  const handleDrop = (e) => {
    e.preventDefault();
    setIsDragOver(false);
    
    const archivos = Array.from(e.dataTransfer.files).filter(
      file => file.type === 'application/pdf'
    );
    
    if (archivos.length === 0) {
      alert('Solo se aceptan archivos PDF');
      return;
    }
    
    onArchivosCargados(archivos);
  };

  return (
    <div
      onDragOver={handleDragOver}
      onDragLeave={handleDragLeave}
      onDrop={handleDrop}
      role="region"
      aria-label="Área de carga de documentos"
      style={{
        border: `2px dashed ${isDragOver ? '#007bff' : '#ccc'}`,
        borderRadius: '8px',
        padding: '40px',
        textAlign: 'center',
        backgroundColor: isDragOver ? '#f0f8ff' : '#fafafa',
        transition: 'all 0.2s ease'
      }}
    >
      <p>Arrastra tus documentos PDF aquí</p>
      <p>o <button type="button">haz clic para seleccionar</button></p>
    </div>
  );
}
```

> 💼 **Casos de uso empresariales:**
> - Subida de documentos (contratos, facturas en PDF)
> - Tableros Kanban (mover tareas entre columnas, como en Jira/Trello)
> - Reordenamiento de elementos en una lista de configuración

---

### 📍 Geolocation API

Permite solicitar la ubicación geográfica del usuario. Requiere **permiso explícito del usuario** y **HTTPS**.

```javascript
// Función utilitaria con promesas para usar en React
function obtenerUbicacion() {
  return new Promise((resolve, reject) => {
    if (!navigator.geolocation) {
      reject(new Error('Geolocation no está disponible en este navegador'));
      return;
    }

    navigator.geolocation.getCurrentPosition(
      (position) => {
        resolve({
          latitud: position.coords.latitude,
          longitud: position.coords.longitude,
          precision: position.coords.accuracy, // En metros
        });
      },
      (error) => {
        // Tipos de error: PERMISSION_DENIED, POSITION_UNAVAILABLE, TIMEOUT
        reject(new Error(`Error de geolocalización: ${error.message}`));
      },
      {
        enableHighAccuracy: true,  // Usa GPS si está disponible
        timeout: 10000,            // Máximo 10 segundos
        maximumAge: 300000,        // Acepta posición en caché de hasta 5 minutos
      }
    );
  });
}

// Hook de React para geolocalización
function useGeolocalizacion() {
  const [estado, setEstado] = React.useState({
    ubicacion: null,
    cargando: false,
    error: null,
  });

  const solicitar = async () => {
    setEstado(prev => ({ ...prev, cargando: true, error: null }));
    try {
      const ubicacion = await obtenerUbicacion();
      setEstado({ ubicacion, cargando: false, error: null });
    } catch (error) {
      setEstado({ ubicacion: null, cargando: false, error: error.message });
    }
  };

  return { ...estado, solicitar };
}
```

> 💼 **Casos de uso empresariales:**
> - Autocompletar dirección de envío en checkout de e-commerce
> - Mostrar la sucursal o tienda física más cercana al usuario
> - Registrar la ubicación de actividades de campo (reportes de vendedores)
> - Verificar que el empleado está en la ubicación correcta (control de asistencia)

---

## 4. Web Components y su Relación con React 19

Los **Web Components** son un conjunto de APIs nativas de la plataforma web que permiten crear etiquetas HTML personalizadas, reutilizables y encapsuladas.

### 🧩 Los Tres Pilares de los Web Components

| Tecnología | Descripción | Caso de uso |
|-----------|-------------|-------------|
| **Custom Elements** | APIs para definir nuevas etiquetas HTML (`<mi-boton>`) | Crear componentes reutilizables entre frameworks |
| **Shadow DOM** | Encapsulación del árbol DOM y estilos CSS aislados del documento principal | Design Systems corporativos (los estilos no se "filtran") |
| **HTML Templates** (`<template>` y `<slot>`) | Marcado que no se renderiza hasta instanciarse con JS | Plantillas reutilizables para componentes |

```javascript
// Definición de un Web Component nativo
class BotonCorporativo extends HTMLElement {
  connectedCallback() {
    this.innerHTML = `
      <button class="btn-corp" style="
        background: #003087;
        color: white;
        padding: 8px 16px;
        border: none;
        border-radius: 4px;
        cursor: pointer;
      ">
        ${this.getAttribute('label') || 'Aceptar'}
      </button>
    `;
  }
}

customElements.define('boton-corporativo', BotonCorporativo);
// Uso: <boton-corporativo label="Confirmar Pedido"></boton-corporativo>
```

---

### 🔄 React 19 y Web Components: El Gran Cambio

Históricamente, React tenía fricción con los Web Components (pasar props complejas, manejar eventos personalizados). Esto obligaba a escribir código extra ("wrappers").

**¡React 19 introduce soporte completo y nativo para Web Components!**

```jsx
// ✅ React 19: Integración directa sin wrappers
// Si la empresa tiene un Design System con Web Components (Lit, Stencil)
// ahora se pueden usar directamente en JSX

import './design-system/components'; // Registra los Web Components

function FormularioPedido() {
  const handleSubmit = (e) => { /* ... */ };
  
  return (
    <form onSubmit={handleSubmit}>
      {/* Los Web Components se usan como elementos HTML estándar */}
      <ds-input 
        label="Número de pedido" 
        type="text"
        required
        onDsChange={(e) => console.log(e.detail)} // Eventos personalizados
      />
      <ds-select 
        label="Estado"
        options={JSON.stringify(['Pendiente', 'Enviado', 'Entregado'])} // Props complejas
      />
      <ds-button type="submit" variant="primary">
        Confirmar Pedido
      </ds-button>
    </form>
  );
}
```

> 💼 **Caso de uso empresarial real:** Una corporación tiene un **Design System** compartido construido con **Lit** o **Stencil** para ser usado en aplicaciones Angular, Vue y React por igual. Con React 19, las aplicaciones React pueden consumir ese Design System directamente en JSX, sin adaptadores, manteniendo la consistencia visual en toda la empresa.

---

## ♿ Resumen de Buenas Prácticas de Accesibilidad (a11y)

```jsx
// Guía rápida de atributos ARIA más usados
const ejemplos = {
  // aria-label: Texto alternativo cuando no hay texto visible
  '<button aria-label="Cerrar modal">✕</button>': 'Sin aria-label, el lector de pantalla diría "botón"',
  
  // aria-hidden: Oculta decoración visual de los lectores de pantalla
  '<span aria-hidden="true">★★★★☆</span>': 'Los iconos decorativos no deben ser leídos',
  
  // aria-live: Anuncia cambios dinámicos en el DOM
  '<div aria-live="polite" aria-atomic="true">{mensaje}</div>': 'Para notificaciones y alertas dinámicas',
  
  // role: Define el rol semántico cuando no se puede usar el elemento correcto
  '<div role="alert">{error}</div>': 'Para mensajes de error que deben ser leídos inmediatamente',
  
  // aria-expanded: Estado de elementos expandibles
  '<button aria-expanded={isOpen}>Menú</button>': 'Para acordeones, menús desplegables',
  
  // aria-required: Campos obligatorios (además del atributo required)
  '<input aria-required="true" required>': 'Comunica el estado a tecnologías de asistencia',
};
```

---

## 📚 Recursos Adicionales

| Recurso | Descripción | Nivel |
|---------|-------------|-------|
| [MDN: HTML elements reference](https://developer.mozilla.org/en-US/docs/Web/HTML/Element) | Referencia completa de todas las etiquetas HTML | Básico |
| [WCAG 2.1 Guidelines](https://www.w3.org/WAI/WCAG21/quickref/) | Estándar internacional de accesibilidad web | Avanzado |
| [The A11Y Project](https://www.a11yproject.com/) | Guías prácticas de accesibilidad | Intermedio |
| [MDN: Web Storage API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API) | Documentación de localStorage y sessionStorage | Básico |
| [MDN: Drag and Drop API](https://developer.mozilla.org/en-US/docs/Web/API/HTML_Drag_and_Drop_API) | Guía completa de Drag & Drop | Intermedio |
| [MDN: Geolocation API](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation_API) | Documentación de la API de geolocalización | Básico |
| [Web Components MDN](https://developer.mozilla.org/en-US/docs/Web/API/Web_components) | Guía de Web Components | Avanzado |
| [React 19: Web Components Support](https://react.dev/blog/2024/12/05/react-19#support-for-custom-elements) | Anuncio oficial del soporte en React 19 | Avanzado |
| [Lit Framework](https://lit.dev/) | Framework para crear Web Components modernos | Avanzado |
