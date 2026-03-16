# Ejercicios de Práctica — Módulo I: Fundamentos Web Modernos

Estos ejercicios están diseñados para practicar **durante las sesiones**. Trabaja de forma independiente o en pares. No hay respuestas "perfectas"; el objetivo es experimentar y aprender.

---

## 🔴 Sección 1: HTML5 y Accesibilidad

### Ejercicio 1.1: Auditando HTML Semántico

Tienes el siguiente HTML. Identifica y **lista todos los problemas semánticos y de accesibilidad** que encuentres:

```html
<div class="page">
  <div class="top">
    <div class="logo">MiEmpresa</div>
    <div class="menu">
      <a href="/inicio">Inicio</a>
      <a href="/productos">Productos</a>
      <a href="/contacto">Contacto</a>
    </div>
  </div>

  <div class="content">
    <div class="post">
      <div class="title">Lanzamos React 19</div>
      <div class="body">React 19 es la versión más nueva...</div>
      <div class="tags">
        <div>React</div>
        <div>JavaScript</div>
      </div>
    </div>
  </div>

  <div class="bottom">
    <div>© 2025 MiEmpresa</div>
    <img src="twitter.png">
  </div>
</div>
```

**Tu tarea:**
1. Lista los problemas (mínimo 8)
2. Reescribe el HTML usando etiquetas semánticas correctas
3. Añade los atributos de accesibilidad necesarios

---

### Ejercicio 1.2: Formulario de Registro Empresarial

Crea un formulario HTML5 completo para el **registro de un nuevo empleado** con los siguientes campos:

| Campo | Tipo | Validaciones |
|-------|------|-------------|
| Nombre completo | Texto | Requerido, mínimo 3 caracteres |
| Correo corporativo | Email | Requerido, debe terminar en `@empresa.com` |
| Teléfono | Tel | Requerido, exactamente 10 dígitos |
| Departamento | Select | Opciones: Ingeniería, Ventas, RH, Finanzas |
| Fecha de inicio | Date | Requerido, no puede ser fecha pasada |
| Nivel | Radio | Junior, Mid, Senior |
| Acepta política de datos | Checkbox | Requerido |
| Notas adicionales | Textarea | Opcional, máximo 500 caracteres |

**Requisitos:**
- Todos los campos deben tener `<label>` asociado correctamente
- Usar `<fieldset>` para agrupar los campos relacionados
- Añadir atributos `aria-describedby` con mensajes de ayuda
- En React, convertir los atributos `for` a `htmlFor`

---

### Ejercicio 1.3: Implementar localStorage con Custom Hook

Implementa el custom hook `useLocalStorage(key, defaultValue)` que:
1. Inicializa el estado desde `localStorage` al montar el componente
2. Guarda automáticamente en `localStorage` cada vez que el estado cambia
3. Maneja errores si `localStorage` no está disponible (modo incógnito)
4. Soporta cualquier tipo de valor serializable (strings, números, objetos, arrays)

**Luego** crea un componente `PreferenciasPanel` que use el hook para guardar:
- Tema (claro/oscuro)
- Idioma (es/en/pt)
- Elementos por página (10/25/50)

---

## 🟡 Sección 2: CSS Moderno

### Ejercicio 2.1: Tarjeta de Empleado con Flexbox

Crea una tarjeta de empleado usando **solo Flexbox** con el siguiente diseño:

```
┌──────────────────────────────────────┐
│  [Avatar]  │  Ana García             │
│  (80x80)   │  Ingeniera Senior       │
│            │  ⭐ 4.8 / 5.0           │
│────────────────────────────────────── │
│  📧 ana@empresa.com                   │
│  📱 +52 55 1234-5678                  │
│────────────────────────────────────── │
│  [Ver Perfil]          [Asignar]      │
└──────────────────────────────────────┘
```

**Requisitos:**
- El avatar y el nombre/rol deben estar en fila (Flexbox horizontal)
- Los datos de contacto, apilados (Flexbox vertical)
- Los botones siempre en la parte inferior de la tarjeta (hint: `flex-grow` o `margin-top: auto`)
- La tarjeta debe tener altura mínima de 200px

---

### Ejercicio 2.2: Layout de Dashboard con CSS Grid

Usando **CSS Grid**, crea el layout de un panel de administración:

```
Desktop (>1024px):                Mobile (<768px):
┌────────────────────┐            ┌──────────────┐
│      HEADER        │            │    HEADER    │
├──────┬─────────────┤            ├──────────────┤
│      │   MAIN      │            │    MAIN      │
│ SIDE │  CONTENT    │            │   CONTENT    │
│ BAR  │             │            ├──────────────┤
│      │             │            │   SIDEBAR    │
├──────┴─────────────┤            ├──────────────┤
│      FOOTER        │            │    FOOTER    │
└────────────────────┘            └──────────────┘
```

**Requisitos:**
- Usar `grid-template-areas`
- Header: altura 64px, fondo azul corporativo
- Sidebar: ancho 240px en desktop, oculto en móvil (debajo del main)
- Footer: altura 48px, fondo gris oscuro
- Añadir la media query para el cambio responsive

---

### Ejercicio 2.3: Sistema de Diseño con Variables CSS

Crea un mini sistema de diseño usando Variables CSS:

1. **Define las variables** en `:root`:
   - 5 colores (primario, secundario, éxito, advertencia, error)
   - 4 niveles de gris (para texto y fondos)
   - 5 tamaños de espaciado (xs, sm, md, lg, xl)
   - 1 radio de borde estándar
   - 2 sombras (card y modal)

2. **Crea el tema oscuro** que cambia solo las variables de color

3. **Implementa en React** el toggle de tema con `document.documentElement.setAttribute('data-theme', tema)`

4. **Aplica las variables** a 3 componentes: Botón, Tarjeta y Badge

---

### Ejercicio 2.4: Animación de Skeleton Loading

Implementa el efecto de **Skeleton Loading** (loader de marcador de posición) que aparece mientras se cargan los datos de la API.

```
Estado de carga:                   Estado cargado:
┌───────────────────────────┐      ┌───────────────────────────┐
│ ████████████████          │      │ Ana García                │
│ ████████████              │      │ Ingeniera Senior          │
│ ████                      │      │ Ingeniería                │
└───────────────────────────┘      └───────────────────────────┘
```

**Requisitos:**
- Usar `@keyframes` con un gradiente animado (efecto shimmer)
- Crear el componente `SkeletonCard` con las mismas dimensiones que `TarjetaEmpleado`
- El componente `TarjetaEmpleado` debe mostrar el skeleton mientras `cargando === true`
- La animación debe respetar `prefers-reduced-motion`

---

## 🟠 Sección 3: JavaScript Moderno

### Ejercicio 3.1: Refactorizar a Sintaxis Moderna

Refactoriza el siguiente código usando ES6+:

```javascript
// Código antiguo a refactorizar
var empleados = [
  { nombre: "Ana", departamento: "Engineering", salario: 75000, activo: true },
  { nombre: "Carlos", departamento: "Sales", salario: 50000, activo: false },
  { nombre: "María", departamento: "Engineering", salario: 82000, activo: true },
  { nombre: "Juan", departamento: "HR", salario: 60000, activo: true },
];

// 1. Obtener solo los nombres de empleados activos
var nombresActivos = [];
for (var i = 0; i < empleados.length; i++) {
  if (empleados[i].activo === true) {
    nombresActivos.push(empleados[i].nombre);
  }
}

// 2. Calcular el salario promedio de Ingeniería
var totalSalario = 0;
var countIngenieria = 0;
for (var j = 0; j < empleados.length; j++) {
  if (empleados[j].departamento === "Engineering") {
    totalSalario = totalSalario + empleados[j].salario;
    countIngenieria = countIngenieria + 1;
  }
}
var promedioIngenieria = totalSalario / countIngenieria;

// 3. Dar un bono del 10% a todos los empleados activos
var conBono = [];
for (var k = 0; k < empleados.length; k++) {
  if (empleados[k].activo) {
    var empleadoConBono = {
      nombre: empleados[k].nombre,
      departamento: empleados[k].departamento,
      salario: empleados[k].salario,
      activo: empleados[k].activo,
      bono: empleados[k].salario * 0.1
    };
    conBono.push(empleadoConBono);
  }
}
```

**Reescríbelo usando:** `const`/`let`, arrow functions, `filter`, `map`, `reduce`, destructuring y spread operator.

---

### Ejercicio 3.2: Manejo de Errores en Peticiones Asíncronas

Implementa la función `cargarDatosDashboard(usuarioId)` que:

1. Realiza **3 peticiones en paralelo** a:
   - `GET /api/usuarios/{id}` — Perfil del usuario
   - `GET /api/notificaciones?usuarioId={id}` — Notificaciones
   - `GET /api/metricas?usuarioId={id}` — Métricas del período

2. Si **todas tienen éxito**: devuelve un objeto con `{ usuario, notificaciones, metricas }`

3. Si **alguna falla** con 404: devuelve el dato por defecto (`[]` para arrays, `null` para objetos)

4. Si **todas fallan**: lanza un error con mensaje descriptivo

**Bonus:** Convierte la función en un custom hook `useDashboard(usuarioId)` que también maneje los estados `cargando` y `error`.

---

### Ejercicio 3.3: Pipeline de Transformación de Datos

Dado el siguiente dataset de ventas, implementa las funciones usando **encadenamiento de métodos de array**:

```javascript
const ventas = [
  { id: 1, producto: 'Laptop Pro', categoria: 'Electrónica', precio: 1200, cantidad: 5, fecha: '2025-01-15', vendedor: 'Ana' },
  { id: 2, producto: 'Silla Ergonómica', categoria: 'Muebles', precio: 450, cantidad: 10, fecha: '2025-01-16', vendedor: 'Carlos' },
  { id: 3, producto: 'Monitor 4K', categoria: 'Electrónica', precio: 600, cantidad: 3, fecha: '2025-01-17', vendedor: 'Ana' },
  { id: 4, producto: 'Teclado Mecánico', categoria: 'Electrónica', precio: 150, cantidad: 20, fecha: '2025-01-18', vendedor: 'María' },
  { id: 5, producto: 'Escritorio', categoria: 'Muebles', precio: 350, cantidad: 8, fecha: '2025-01-19', vendedor: 'Carlos' },
];
```

Implementa **sin usar bucles `for`/`while`**:

1. `totalPorCategoria()` — Objeto con el total de ingresos por categoría
2. `topVendedor()` — El nombre del vendedor con más ingresos totales
3. `productosEnRango(min, max)` — Productos con precio entre min y max, ordenados por precio ascendente
4. `resumenReporte()` — Objeto con: totalVentas, promedioVenta, productoMasVendido, mejorCategoria

---

## 🔵 Sección 4: Arquitectura Web

### Ejercicio 4.1: Diagrama de Arquitectura

Para cada uno de los siguientes proyectos, **decide y justifica** qué estrategia de rendering usarías (CSR, SSR, SSG o un híbrido):

1. **Sistema de gestión de recursos humanos (HRMS)** — Solo acceden empleados autenticados. Incluye: directorio de empleados, gestión de vacaciones, nómina.

2. **Portal de e-commerce corporativo B2B** — Catálogo de productos, precios por cliente, carrito de compras, checkout con integración a ERP.

3. **Blog técnico del equipo de ingeniería** — Artículos técnicos, documentación de APIs, actualizaciones de productos. Publicaciones ocasionales (1-3 por semana).

4. **Dashboard de análisis en tiempo real** — Métricas de ventas, inventario y operaciones actualizándose cada 30 segundos. Solo para gerentes.

Para cada uno indica:
- ✅ Estrategia elegida
- 📋 Justificación técnica
- 🛠️ Stack tecnológico recomendado
- ⚠️ Riesgos o consideraciones

---

### Ejercicio 4.2: Consumir una API Real

Usa la API pública `https://jsonplaceholder.typicode.com` para implementar:

1. Un componente `ListaUsuarios` que:
   - Carga la lista de usuarios desde `GET /users`
   - Muestra estado de carga con Skeleton
   - Muestra mensaje de error si la petición falla
   - Permite buscar usuarios por nombre en tiempo real

2. Un componente `DetalleUsuario` que:
   - Recibe un `usuarioId` como prop
   - Carga los datos del usuario desde `GET /users/{id}`
   - Carga sus posts desde `GET /posts?userId={id}` **en paralelo** con los datos del usuario
   - Muestra nombre, email, empresa y lista de títulos de posts

**Sin usar React Query ni Axios** — Solo `fetch` nativo y los hooks `useState` / `useEffect`.

---

## 💡 Reto Extra (Para los más aventureros)

### Ejercicio Extra: Mini Kanban Board

Implementa un **tablero Kanban** básico usando la **Drag & Drop API** nativa (sin librerías):

```
┌──────────────┬──────────────┬──────────────┐
│  Por Hacer   │  En Progreso │   Terminado  │
├──────────────┼──────────────┼──────────────┤
│ [Tarea A]    │ [Tarea C]    │ [Tarea E]    │
│ [Tarea B]    │              │              │
└──────────────┴──────────────┴──────────────┘
```

**Requisitos:**
- 3 columnas con estado inicial de tareas
- Las tareas pueden arrastrarse entre columnas
- El estado se guarda en `localStorage` automáticamente
- Feedback visual al arrastrar (columna se ilumina al pasar sobre ella)
- Al soltar en una columna inválida, la tarea regresa a su posición original
