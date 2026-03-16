# CSS Moderno

## 1. Flexbox: Layouts Unidimensionales

### ¿Qué es Flexbox?

El **Flexible Box Layout Module** (Flexbox) es un modelo de diseño **unidimensional** de CSS diseñado para distribuir el espacio a lo largo de **una sola fila o columna**. Es la herramienta perfecta para alinear elementos dentro de una barra de navegación, centrar un componente o distribuir botones de acción.

> 🏗️ **Regla arquitectónica clave:** Usa **Flexbox** para layouts unidimensionales (una fila O una columna) y **CSS Grid** para layouts bidimensionales (filas Y columnas simultáneamente).

---

### 🧠 Conceptos Fundamentales

La arquitectura Flexbox divide el mundo en dos entidades:

- **Contenedor Flex (Padre):** El elemento con `display: flex`. Controla cómo se distribuyen sus hijos.
- **Ítems Flex (Hijos):** Los elementos directos del contenedor. Responden a las reglas del padre y tienen sus propias propiedades.

Existen dos ejes invisibles:
- **Main Axis (Eje Principal):** Dirección en la que fluyen los ítems (por defecto: horizontal → ).
- **Cross Axis (Eje Cruzado):** Perpendicular al eje principal (por defecto: vertical ↓).

---

### A. Propiedades del Contenedor (Padre)

```css
.contenedor {
  display: flex; /* Activa el contexto Flexbox */
}
```

| Propiedad | Valores | Para qué sirve |
|-----------|---------|---------------|
| `flex-direction` | `row` \| `row-reverse` \| `column` \| `column-reverse` | Define la dirección del Eje Principal |
| `justify-content` | `flex-start` \| `center` \| `flex-end` \| `space-between` \| `space-around` \| `space-evenly` | Alinea ítems en el **Eje Principal** |
| `align-items` | `stretch` \| `center` \| `flex-start` \| `flex-end` \| `baseline` | Alinea ítems en el **Eje Cruzado** |
| `flex-wrap` | `nowrap` \| `wrap` \| `wrap-reverse` | Permite que los ítems salten a nueva línea |
| `gap` | `16px` \| `1rem 2rem` | Espacio uniforme entre ítems (filas y columnas) |
| `align-content` | `flex-start` \| `center` \| `space-between`... | Alinea múltiples filas/columnas (solo con `flex-wrap: wrap`) |

---

### B. Propiedades de los Ítems (Hijos)

| Propiedad | Valores | Para qué sirve |
|-----------|---------|---------------|
| `flex-grow` | `0` \| `1` \| `2`... | Capacidad de **crecer** para ocupar espacio sobrante |
| `flex-shrink` | `1` \| `0`... | Capacidad de **encogerse** si no hay espacio |
| `flex-basis` | `auto` \| `200px` \| `30%` | Tamaño base antes de distribuir el espacio sobrante |
| `flex` | `1` \| `1 1 auto` | **Shorthand** de `flex-grow flex-shrink flex-basis` |
| `align-self` | `auto` \| `center` \| `flex-start`... | Sobrescribe `align-items` del padre para este ítem |
| `order` | `0` \| `-1` \| `1`... | Cambia el orden visual sin cambiar el HTML |

---

### 🚀 Casos de Uso y Ejemplos Reales

#### Ejemplo 1: El Centrado Perfecto

```css
/* Antigüedad: hackeos con position, margin: auto, etc.  */
/* Hoy: 3 líneas. */
.modal-container {
  display: flex;
  justify-content: center; /* Centrado horizontal */
  align-items: center;     /* Centrado vertical */
  min-height: 100vh;
}
```

#### Ejemplo 2: Barra de Navegación (Navbar)

```css
/* Logo a la izquierda, links a la derecha */
.navbar {
  display: flex;
  justify-content: space-between; /* Empuja logo y links a los extremos */
  align-items: center;            /* Centra verticalmente */
  padding: 1rem 2rem;
}

.nav-links {
  display: flex;
  gap: 1.5rem; /* Espacio uniforme entre enlaces — mejor que margin */
  list-style: none;
}
```

#### Ejemplo 3: Footer Pegajoso (Sticky Footer)

```css
/* El footer siempre al fondo, aunque haya poco contenido */
body {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

main {
  flex-grow: 1; /* El main se expande, empujando el footer hacia abajo */
}
```

#### Ejemplo 4: Tarjeta de Producto (Card)

```css
/* Los botones siempre al fondo, sin importar el largo del texto */
.product-card {
  display: flex;
  flex-direction: column;
  height: 100%;
}

.product-card__content {
  flex-grow: 1; /* El contenido absorbe el espacio disponible */
}

.product-card__actions {
  /* Los botones siempre quedan al final de la tarjeta */
  display: flex;
  gap: 0.5rem;
  margin-top: auto; /* Alternativa: empuja al final sin flex-grow */
}
```

---

### 💡 Mejores Prácticas de Flexbox

```css
/* ✅ USA gap en lugar de márgenes con :last-child */
.nav-links {
  display: flex;
  gap: 16px; /* Limpio, simple, funciona */
}

/* ❌ EVITA este patrón antiguo */
.nav-links li { margin-right: 16px; }
.nav-links li:last-child { margin-right: 0; }

/* ✅ USA el shorthand flex */
.item { flex: 1; }          /* Equivale a: flex: 1 1 0% */
.item-doble { flex: 2; }    /* El doble de ancho que flex: 1 */

/* ❌ EVITA declarar las 3 propiedades por separado */
.item { flex-grow: 1; flex-shrink: 1; flex-basis: 0%; }

/* ✅ EVITA anchos fijos en hijos flex — usa flex-basis */
.sidebar { flex: 0 0 280px; }  /* No crece ni se encoge, base 280px */
.content { flex: 1; }          /* Ocupa el resto del espacio */
```

---

## 2. CSS Grid Layout: Estructuras Bidimensionales

### ¿Qué es CSS Grid Layout?

**CSS Grid Layout** es el sistema de diseño más potente de CSS. A diferencia de Flexbox, está diseñado para layouts **bidimensionales**, permitiendo manejar **filas y columnas simultáneamente**. Es el estándar para diseñar la estructura completa de una página o un dashboard.

---

### 🧠 Nuevos Conceptos de Grid

| Concepto | Descripción |
|----------|-------------|
| **Grid Lines** | Las líneas que dividen la cuadrícula (numeradas desde 1) |
| **Grid Tracks** | El espacio entre dos líneas adyacentes (una fila o columna completa) |
| **Grid Cell** | La intersección de una fila y una columna (la unidad mínima) |
| **Grid Area** | Un espacio rectangular formado por múltiples celdas |
| **Unidad `fr`** | Fracción del espacio disponible (`1fr 2fr` = la segunda es el doble de ancha) |

---

### A. Propiedades del Contenedor (Padre)

```css
.contenedor {
  display: grid; /* Activa el contexto Grid */
}
```

| Propiedad | Ejemplo | Para qué sirve |
|-----------|---------|---------------|
| `grid-template-columns` | `1fr 2fr 1fr` | Define el número y ancho de columnas |
| `grid-template-rows` | `80px 1fr 60px` | Define el número y alto de filas |
| `grid-template-areas` | Ver ejemplo abajo | Nombra áreas para organizar visualmente |
| `gap` | `16px` | Espacio entre filas y columnas |
| `grid-auto-rows` | `200px` | Alto por defecto para filas generadas automáticamente |
| `grid-auto-columns` | `1fr` | Ancho por defecto para columnas generadas automáticamente |
| `justify-items` | `center` | Alinea ítems horizontalmente dentro de su celda |
| `align-items` | `center` | Alinea ítems verticalmente dentro de su celda |

---

### B. Propiedades de los Ítems (Hijos)

| Propiedad | Ejemplo | Para qué sirve |
|-----------|---------|---------------|
| `grid-column` | `1 / 3` | Columnas de inicio y fin (o `span 2` para abarcar 2 columnas) |
| `grid-row` | `1 / 2` | Filas de inicio y fin |
| `grid-area` | `header` | Asigna el ítem a un área nombrada en `grid-template-areas` |
| `justify-self` | `center` | Alinea el ítem horizontalmente dentro de su celda |
| `align-self` | `end` | Alinea el ítem verticalmente dentro de su celda |

---

### 🚀 Casos de Uso y Ejemplos Reales

#### Ejemplo 1: El Layout Santo Grial (Header + Sidebar + Main + Footer)

```css
.layout-container {
  display: grid;
  grid-template-columns: 250px 1fr;        /* Sidebar fijo, main flexible */
  grid-template-rows: 80px 1fr 60px;       /* Header, contenido, Footer */
  grid-template-areas:
    "header  header"                        /* Header ocupa toda la fila */
    "sidebar main"                          /* Sidebar + Main */
    "footer  footer";                       /* Footer ocupa toda la fila */
  min-height: 100vh;
  gap: 0; /* Sin espacio entre áreas de layout */
}

.header  { grid-area: header; }
.sidebar { grid-area: sidebar; }
.main    { grid-area: main; }
.footer  { grid-area: footer; }
```

```css
/* Responsive: En móvil, el sidebar se apila sobre el main */
@media (max-width: 768px) {
  .layout-container {
    grid-template-columns: 1fr;             /* Una sola columna */
    grid-template-rows: 80px auto 1fr 60px;
    grid-template-areas:
      "header"
      "sidebar"
      "main"
      "footer";
  }
}
```

#### Ejemplo 2: Galería Automática y Responsiva (sin Media Queries)

```css
/* La "fórmula mágica" del CSS Grid moderno */
.galeria-productos {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  /* 
    auto-fit: Crea tantas columnas como quepan
    minmax(250px, 1fr): Cada columna mínimo 250px, máximo 1fr
    Resultado: Responsive automático sin una sola media query
  */
  gap: 16px;
}
```

#### Ejemplo 3: Dashboard Bento Box

```css
/* Layouts asimétricos modernos, tipo dashboard */
.bento-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-auto-rows: 200px;
  gap: 20px;
}

/* Elemento destacado que ocupa más espacio */
.kpi-principal {
  grid-column: span 2; /* Ocupa 2 columnas */
  grid-row: span 2;    /* Ocupa 2 filas */
}

/* Gráfica ancha */
.grafica-ventas {
  grid-column: span 3;
}
```

#### Ejemplo 4: Superposición de Elementos (Unique to Grid)

```css
/* A diferencia de Flexbox, Grid puede superponer elementos sin position: absolute */
.hero-section {
  display: grid;
  grid-template: 1fr / 1fr; /* 1 fila, 1 columna */
}

/* Ambos elementos ocupan la misma celda: se superponen */
.hero-section > * {
  grid-area: 1 / 1 / 2 / 2;
}

.hero-section__imagen {
  width: 100%;
  height: 400px;
  object-fit: cover;
}

.hero-section__texto {
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  background: rgba(0, 0, 0, 0.4); /* Overlay semitransparente */
  z-index: 1;
}
```

---

### 💡 Mejores Prácticas de CSS Grid

```css
/* ✅ USA la unidad fr en lugar de porcentajes */
.layout { grid-template-columns: 1fr 2fr 1fr; }    /* ✅ Se adapta al espacio */
.layout { grid-template-columns: 25% 50% 25%; }    /* ❌ No cuenta el gap */

/* ✅ USA repeat() para no repetir código */
.grid-4-cols { grid-template-columns: repeat(4, 1fr); }   /* ✅ */
.grid-4-cols { grid-template-columns: 1fr 1fr 1fr 1fr; }  /* ❌ Repetitivo */

/* ✅ USA span para extender elementos */
.featured { grid-column: span 2; }    /* ✅ Legible */
.featured { grid-column: 1 / 3; }     /* ✅ También válido si necesitas posición exacta */

/* ✅ LA FÓRMULA RESPONSIVA sin Media Queries */
.auto-grid {
  grid-template-columns: repeat(auto-fit, minmax(min(100%, 300px), 1fr));
  /* min(100%, 300px) evita que en móvil el minmax cause overflow */
}
```

---

## 3. Variables CSS (Custom Properties)

Las **CSS Custom Properties** (Variables CSS) permiten definir valores reutilizables que se pueden actualizar desde JavaScript. Son la base de los **Design Tokens** modernos.

```css
/* Definición en :root — disponibles globalmente */
:root {
  /* Design Tokens de Color */
  --color-primario: #003087;
  --color-primario-hover: #004cc7;
  --color-texto: #1a1a2e;
  --color-fondo: #ffffff;
  --color-error: #d32f2f;
  --color-exito: #388e3c;

  /* Tipografía */
  --fuente-principal: 'Inter', system-ui, sans-serif;
  --tamanio-base: 16px;
  --escala-tipografica: 1.25;

  /* Espaciado (sistema de 8px) */
  --espacio-xs:  4px;
  --espacio-sm:  8px;
  --espacio-md:  16px;
  --espacio-lg:  24px;
  --espacio-xl:  32px;
  --espacio-2xl: 48px;

  /* Bordes y sombras */
  --radio-borde: 8px;
  --sombra-card: 0 2px 8px rgba(0, 0, 0, 0.12);
  --sombra-modal: 0 8px 32px rgba(0, 0, 0, 0.24);

  /* Transiciones */
  --transicion-rapida: 0.15s ease-in-out;
  --transicion-normal: 0.3s ease-in-out;
}

/* Tema Oscuro — cambia solo las variables, no el CSS */
[data-theme="dark"] {
  --color-texto: #e8eaf6;
  --color-fondo: #0d1117;
  --sombra-card: 0 2px 8px rgba(0, 0, 0, 0.4);
}
```

```css
/* Uso en componentes */
.boton-primario {
  background-color: var(--color-primario);
  color: var(--color-fondo);
  padding: var(--espacio-sm) var(--espacio-md);
  border-radius: var(--radio-borde);
  font-family: var(--fuente-principal);
  transition: background-color var(--transicion-rapida);
}

.boton-primario:hover {
  background-color: var(--color-primario-hover);
}
```

```javascript
// Cambiar tema desde JavaScript/React — el CSS reacciona automáticamente
function ToggleTema({ temaActual, onCambiar }) {
  const handleCambio = () => {
    const nuevoTema = temaActual === 'light' ? 'dark' : 'light';
    document.documentElement.setAttribute('data-theme', nuevoTema);
    onCambiar(nuevoTema);
  };

  return (
    <button onClick={handleCambio} aria-label={`Cambiar a tema ${temaActual === 'light' ? 'oscuro' : 'claro'}`}>
      {temaActual === 'light' ? '🌙' : '☀️'}
    </button>
  );
}
```

---

## 4. Media Queries y Diseño Responsive

### Enfoque Mobile-First

El enfoque **Mobile-First** es el estándar de la industria: diseñar primero para pantallas pequeñas y escalar hacia arriba con `min-width`.

```css
/* ✅ Enfoque Mobile-First (recomendado) */
.grid-productos {
  display: grid;
  grid-template-columns: 1fr;       /* Por defecto: 1 columna en móvil */
  gap: var(--espacio-md);
}

@media (min-width: 640px) {          /* sm: Tablets pequeñas */
  .grid-productos { grid-template-columns: repeat(2, 1fr); }
}

@media (min-width: 1024px) {         /* lg: Desktop */
  .grid-productos { grid-template-columns: repeat(3, 1fr); }
}

@media (min-width: 1280px) {         /* xl: Desktop grande */
  .grid-productos { grid-template-columns: repeat(4, 1fr); }
}
```

### Breakpoints Estándar (Sistema Tailwind-compatible)

| Nombre | Breakpoint | Dispositivo típico |
|--------|-----------|-------------------|
| `sm` | `640px` | Teléfonos horizontales / tablets pequeñas |
| `md` | `768px` | Tablets |
| `lg` | `1024px` | Laptops / Desktop pequeño |
| `xl` | `1280px` | Desktop estándar |
| `2xl` | `1536px` | Desktop grande / monitores 4K |

### Media Queries Avanzadas

```css
/* Preferencias del sistema operativo */
@media (prefers-color-scheme: dark) {
  :root { /* Variables del tema oscuro */ }
}

@media (prefers-reduced-motion: reduce) {
  /* Deshabilita animaciones para usuarios con epilepsia o mareos */
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}

/* Dispositivos táctiles vs puntero fino */
@media (hover: hover) and (pointer: fine) {
  .boton:hover { /* Solo aplica en dispositivos con cursor real */ }
}

/* Orientación */
@media (orientation: landscape) {
  .video-container { max-height: 60vh; }
}
```

---

## 5. Animaciones y Transiciones

### Transiciones CSS (`transition`)

Las transiciones son para cambios de estado simples (`:hover`, `:focus`, cambio de clase).

```css
/* Sintaxis: property duration timing-function delay */
.boton {
  background-color: var(--color-primario);
  transform: translateY(0);
  box-shadow: var(--sombra-card);
  
  transition:
    background-color 0.2s ease,    /* Transición de color */
    transform 0.15s ease-out,       /* Transición de posición */
    box-shadow 0.2s ease;           /* Transición de sombra */
}

.boton:hover {
  background-color: var(--color-primario-hover);
  transform: translateY(-2px);       /* Efecto "levitar" */
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.2);
}

.boton:active {
  transform: translateY(0px);        /* Efecto "presionar" */
}
```

### Animaciones CSS (`@keyframes`)

Las animaciones son para secuencias más complejas o movimientos continuos.

```css
/* Skeleton Loading — estándar en apps empresariales */
@keyframes skeleton-shimmer {
  0%   { background-position: -468px 0; }
  100% { background-position: 468px 0; }
}

.skeleton {
  background: linear-gradient(
    90deg,
    #f0f0f0 25%,
    #e0e0e0 50%,
    #f0f0f0 75%
  );
  background-size: 936px 100%;
  animation: skeleton-shimmer 1.2s ease-in-out infinite;
  border-radius: var(--radio-borde);
}

/* Entrada de elementos (fade + slide) */
@keyframes entrar-desde-abajo {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.card-animada {
  animation: entrar-desde-abajo 0.4s ease-out both;
}

/* Animaciones escalonadas con animation-delay */
.lista-items > :nth-child(1) { animation-delay: 0ms; }
.lista-items > :nth-child(2) { animation-delay: 50ms; }
.lista-items > :nth-child(3) { animation-delay: 100ms; }
```

---

## 6. Estrategias de Estilos: Preprocesadores vs CSS Moderno vs CSS-in-JS

### Comparativa de Estrategias

| Estrategia | Ejemplos | Ventajas | Desventajas | Cuándo usar |
|------------|---------|----------|-------------|-------------|
| **CSS Nativo Moderno** | Variables CSS, Grid, Flexbox | Sin dependencias, rendimiento máximo, soporte nativo | Menos funcionalidades que Sass | Siempre como base |
| **Preprocesadores CSS** | Sass (`.scss`), Less | Mixins, funciones, anidado, herencia | Requiere compilación, menos necesario hoy | Proyectos grandes con sistemas de diseño complejos |
| **CSS Modules** | `styles.module.css` | Scoping local automático (sin conflictos), cero runtime | Dificulta estilos dinámicos | Componentes React que necesitan aislamiento |
| **CSS-in-JS** | Styled Components, Emotion | Estilos dinámicos con props, colocación junto al componente | Runtime overhead, curva de aprendizaje | Apps con muchos temas dinámicos o Design Systems |
| **Utilidades Atómicas** | Tailwind CSS | Desarrollo ultrarrápido, bundle pequeño con purge | HTML verboso, curva inicial | Prototipado rápido, equipos que priorizan velocidad |

---

### Sass / SCSS en la Práctica

```scss
// Variables SCSS (hoy reemplazadas por CSS Custom Properties)
$color-primario: #003087;
$espacio-base: 8px;

// Mixins — funciones reutilizables
@mixin flex-centrado {
  display: flex;
  justify-content: center;
  align-items: center;
}

@mixin responsive($breakpoint) {
  @if $breakpoint == 'sm'  { @media (min-width: 640px)  { @content; } }
  @if $breakpoint == 'md'  { @media (min-width: 768px)  { @content; } }
  @if $breakpoint == 'lg'  { @media (min-width: 1024px) { @content; } }
}

// Anidado — más cercano a la estructura del HTML
.navbar {
  background: $color-primario;
  padding: $espacio-base * 2;

  &__logo {
    @include flex-centrado;
    font-size: 1.5rem;
  }

  &__links {
    display: flex;
    gap: $espacio-base * 2;

    a {
      color: white;
      text-decoration: none;

      &:hover { opacity: 0.8; }
    }
  }

  @include responsive('md') {
    padding: $espacio-base * 3;
  }
}
```

---

### CSS Modules en React

```jsx
/* Archivo: Boton.module.css */
/*
.boton { background: #003087; color: white; padding: 8px 16px; }
.boton--primario { background: #003087; }
.boton--secundario { background: transparent; border: 2px solid #003087; color: #003087; }
*/

// Archivo: Boton.jsx
import styles from './Boton.module.css';
import clsx from 'clsx'; // Librería para combinar clases condicionalmente

function Boton({ variante = 'primario', children, ...props }) {
  return (
    <button
      className={clsx(
        styles.boton,
        variante === 'primario' && styles['boton--primario'],
        variante === 'secundario' && styles['boton--secundario']
      )}
      {...props}
    >
      {children}
    </button>
  );
}
```

---

### Styled Components (CSS-in-JS)

```jsx
import styled, { css } from 'styled-components';

// Estilos dinámicos basados en props
const Boton = styled.button`
  padding: 8px 16px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-weight: 600;
  transition: all 0.2s ease;

  /* Estilos dinámicos según props */
  ${props => props.$variante === 'primario' && css`
    background: #003087;
    color: white;
    &:hover { background: #004cc7; }
  `}

  ${props => props.$variante === 'secundario' && css`
    background: transparent;
    border: 2px solid #003087;
    color: #003087;
    &:hover { background: #f0f4ff; }
  `}

  ${props => props.$tamanio === 'grande' && css`
    padding: 12px 24px;
    font-size: 1.1rem;
  `}
`;

// Uso
function PaginaLogin() {
  return (
    <>
      <Boton $variante="primario" $tamanio="grande">Iniciar Sesión</Boton>
      <Boton $variante="secundario">Cancelar</Boton>
    </>
  );
}
```

---

## 📚 Recursos Adicionales

| Recurso | Descripción | Nivel |
|---------|-------------|-------|
| [CSS Flexbox Froggy](https://flexboxfroggy.com/) | Juego interactivo para aprender Flexbox | Básico |
| [CSS Grid Garden](https://cssgridgarden.com/) | Juego interactivo para aprender Grid | Básico |
| [CSS Tricks: Flexbox Guide](https://css-tricks.com/snippets/css/a-guide-to-flexbox/) | La guía visual más completa de Flexbox | Intermedio |
| [CSS Tricks: Grid Guide](https://css-tricks.com/snippets/css/complete-guide-grid/) | La guía visual más completa de Grid | Intermedio |
| [MDN: CSS Custom Properties](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties) | Documentación oficial de Variables CSS | Básico |
| [Sass Documentation](https://sass-lang.com/documentation/) | Documentación oficial de Sass/SCSS | Intermedio |
| [Styled Components Docs](https://styled-components.com/docs) | Documentación de la librería más popular de CSS-in-JS | Intermedio |
| [Tailwind CSS Docs](https://tailwindcss.com/docs) | Documentación de Tailwind CSS | Básico |
| [An Interactive Guide to CSS Transitions](https://www.joshwcomeau.com/animation/css-transitions/) | Guía profunda sobre transiciones CSS | Avanzado |
| [web.dev: Responsive Design](https://web.dev/learn/design/) | Curso de Google sobre diseño responsive | Intermedio |
