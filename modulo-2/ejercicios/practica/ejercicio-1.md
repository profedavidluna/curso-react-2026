# Ejercicio de Práctica 1: Sistema de Cards de Empleados

**Nivel:** Intermedio  
**Tiempo estimado:** 60-80 minutos  
**Conceptos:** JSX, props, diseño atómico, renderizado condicional, listas y keys

---

## Contexto empresarial

La empresa **DevCorp Solutions** necesita una sección de "Nuestro Equipo" para su sitio web corporativo. El equipo de Recursos Humanos quiere mostrar el directorio de empleados con sus roles, departamentos y disponibilidad (disponible para proyectos / en proyecto actual).

---

## Objetivo

Construir un directorio de empleados aplicando los principios del Diseño Atómico, con los siguientes requisitos:

---

## Requisitos Funcionales

### RF-01: Visualización de empleados
- [ ] Mostrar una cuadrícula de tarjetas de empleados.
- [ ] Cada tarjeta debe mostrar: foto de perfil (o iniciales si no hay foto), nombre completo, cargo, departamento y estado de disponibilidad.

### RF-02: Indicador de disponibilidad
- [ ] Si el empleado está **disponible**, mostrar un badge verde con texto "Disponible".
- [ ] Si el empleado está **en proyecto**, mostrar un badge naranja con el nombre del proyecto.
- [ ] Si el empleado está **de vacaciones**, mostrar un badge gris con "Vacaciones".

### RF-03: Filtrado por departamento
- [ ] Botones para filtrar empleados por departamento: Todos, Frontend, Backend, Diseño, DevOps.
- [ ] El botón del departamento activo debe tener un estilo diferente.
- [ ] Al cambiar el filtro, la lista se actualiza sin recargar la página.

### RF-04: Estadísticas del equipo
- [ ] Mostrar un resumen encima de la cuadrícula: total de empleados, disponibles, en proyecto.

---

## Datos de muestra

Crea un archivo `src/data/empleados.js` con los siguientes datos:

```js
export const EMPLEADOS = [
  {
    id: 1,
    nombre: 'Ana García',
    cargo: 'Senior Frontend Developer',
    departamento: 'Frontend',
    foto: null,   // Sin foto — mostrar iniciales
    estado: 'disponible',
    proyecto: null,
  },
  {
    id: 2,
    nombre: 'Carlos Mendez',
    cargo: 'Backend Architect',
    departamento: 'Backend',
    foto: 'https://i.pravatar.cc/150?img=3',
    estado: 'en-proyecto',
    proyecto: 'Sistema de Pagos v2',
  },
  {
    id: 3,
    nombre: 'María López',
    cargo: 'UX/UI Designer',
    departamento: 'Diseño',
    foto: 'https://i.pravatar.cc/150?img=5',
    estado: 'disponible',
    proyecto: null,
  },
  {
    id: 4,
    nombre: 'Javier Torres',
    cargo: 'DevOps Engineer',
    departamento: 'DevOps',
    foto: null,
    estado: 'vacaciones',
    proyecto: null,
  },
  {
    id: 5,
    nombre: 'Laura Sánchez',
    cargo: 'React Developer',
    departamento: 'Frontend',
    foto: 'https://i.pravatar.cc/150?img=9',
    estado: 'en-proyecto',
    proyecto: 'App Móvil Cliente',
  },
  {
    id: 6,
    nombre: 'Roberto Jiménez',
    cargo: 'Node.js Developer',
    departamento: 'Backend',
    foto: 'https://i.pravatar.cc/150?img=12',
    estado: 'disponible',
    proyecto: null,
  },
  {
    id: 7,
    nombre: 'Sofía Herrera',
    cargo: 'Frontend Developer',
    departamento: 'Frontend',
    foto: 'https://i.pravatar.cc/150?img=16',
    estado: 'disponible',
    proyecto: null,
  },
  {
    id: 8,
    nombre: 'Miguel Ángel Castro',
    cargo: 'Cloud Architect',
    departamento: 'DevOps',
    foto: 'https://i.pravatar.cc/150?img=20',
    estado: 'en-proyecto',
    proyecto: 'Migración a AWS',
  },
];
```

---

## Arquitectura esperada

Aplica el Diseño Atómico para organizar tus componentes:

```
src/
├── data/
│   └── empleados.js
├── components/
│   ├── atoms/
│   │   ├── Avatar/        ← Foto o iniciales del empleado
│   │   └── StatusBadge/   ← Badge de disponibilidad
│   ├── molecules/
│   │   └── EmployeeCard/  ← Tarjeta completa del empleado
│   └── organisms/
│       ├── DepartmentFilter/ ← Botones de filtro
│       ├── StatsBar/         ← Barra de estadísticas
│       └── EmployeeGrid/     ← Cuadrícula de tarjetas
└── App.jsx
```

---

## Puntos de evaluación

| Criterio | Descripción | Puntos |
|----------|-------------|--------|
| **Estructura atómica** | Componentes bien divididos en átomos, moléculas, organismos | 25 pts |
| **JSX correcto** | Sin errores de sintaxis, keys en listas, cierre correcto de tags | 20 pts |
| **Renderizado condicional** | Badges correctos según estado del empleado | 20 pts |
| **Filtrado funcional** | El filtro por departamento funciona correctamente | 20 pts |
| **Estadísticas** | La barra de resumen muestra datos correctos | 15 pts |

**Total: 100 puntos**

---

## Pistas

<details>
<summary>💡 Pista 1: Calcular iniciales del Avatar</summary>

```jsx
// Para obtener las iniciales de "Ana García":
const obtenerIniciales = (nombre) => {
  return nombre
    .split(' ')
    .slice(0, 2)
    .map(palabra => palabra[0].toUpperCase())
    .join('');
};
// Resultado: "AG"
```
</details>

<details>
<summary>💡 Pista 2: Filtrar empleados con useState</summary>

```jsx
const [departamentoActivo, setDepartamentoActivo] = useState('Todos');

const empleadosFiltrados = departamentoActivo === 'Todos'
  ? EMPLEADOS
  : EMPLEADOS.filter(e => e.departamento === departamentoActivo);
```
</details>

<details>
<summary>💡 Pista 3: Calcular estadísticas</summary>

```jsx
const stats = {
  total: empleados.length,
  disponibles: empleados.filter(e => e.estado === 'disponible').length,
  enProyecto: empleados.filter(e => e.estado === 'en-proyecto').length,
};
```
</details>

---

## Entregables

1. Código fuente del proyecto Vite + React.
2. Captura de pantalla de la aplicación en funcionamiento.
3. Breve explicación (2-3 párrafos) de las decisiones de diseño tomadas: ¿Por qué separaste los componentes de esa manera? ¿Qué cambiarías si el proyecto creciera?

---

*← [Volver al índice del Módulo 2](../../README.md) | Siguiente ejercicio: [Práctica 2 →](./ejercicio-2.md)*
