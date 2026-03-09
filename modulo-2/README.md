# Módulo 2: Introducción a React 19 y JSX

## Descripción del Módulo

Este módulo marca el inicio del núcleo del curso: **React**. Partimos de los fundamentos conceptuales de la biblioteca, comprendemos la arquitectura basada en componentes y nos adentramos en las novedades de **React 19**, la versión más avanzada hasta la fecha. Aprenderemos a escribir JSX con confianza y terminaremos aplicando el **Diseño Atómico** para estructurar proyectos React de escala empresarial.

---

## Objetivos de Aprendizaje

Al finalizar este módulo, el estudiante será capaz de:

1. Explicar qué es React, su propósito y sus ventajas frente a otras soluciones.
2. Describir el funcionamiento del Virtual DOM y el proceso de reconciliación.
3. Identificar y aplicar las principales novedades de React 19.
4. Escribir JSX correcto y comprensible, siguiendo las mejores prácticas.
5. Transformar JSX en JavaScript puro de forma manual (conceptual) y automática con el compilador.
6. Diseñar la arquitectura de un proyecto React aplicando los principios del Diseño Atómico.

---

## Estructura del Módulo

```
modulo-2/
├── README.md                          ← Este archivo
├── react-fundamentos/
│   └── README.md                      ← Qué es React, Virtual DOM, componentes, comparativa
├── react-19/
│   └── README.md                      ← Novedades de React 19
├── jsx/
│   └── README.md                      ← JSX en profundidad
├── diseno-atomico/
│   └── README.md                      ← Diseño atómico y organización de proyectos
└── ejercicios/
    ├── resueltos/
    │   ├── ejercicio-1-componente-tarjeta-producto.md
    │   └── ejercicio-2-lista-tareas-react19.md
    └── practica/
        ├── ejercicio-1.md
        ├── ejercicio-2.md
        └── ejercicio-3.md
```

---

## Sesión 3: Introducción a React 19 y JSX

### Temario

| Bloque | Tema | Recurso |
|--------|------|---------|
| 1 | Fundamentos de React | [`react-fundamentos/README.md`](./react-fundamentos/README.md) |
| 2 | Novedades en React 19 | [`react-19/README.md`](./react-19/README.md) |
| 3 | JSX en profundidad | [`jsx/README.md`](./jsx/README.md) |
| 4 | Diseño Atómico y organización de componentes | [`diseno-atomico/README.md`](./diseno-atomico/README.md) |
| 5 | Ejercicios resueltos | [`ejercicios/resueltos/`](./ejercicios/resueltos/) |
| 6 | Ejercicios de práctica | [`ejercicios/practica/`](./ejercicios/practica/) |

---

## Prerrequisitos

Para aprovechar al máximo este módulo, el estudiante debe haber completado el **Módulo 1: Fundamentos Web Modernos** y tener un dominio sólido de:

- **JavaScript moderno (ES6+)**: arrow functions, destructuring, módulos, spread/rest operator, optional chaining.
- **HTML5 y CSS3**: estructura semántica, Flexbox o Grid básico.
- **Node.js y npm/pnpm**: instalación de dependencias y ejecución de scripts.
- **Línea de comandos**: navegación básica y ejecución de comandos.

---

## Configuración del Entorno

### Crear un proyecto React 19 con Vite

```bash
# Usando npm
npm create vite@latest mi-app-react -- --template react

# Usando pnpm (recomendado en entornos empresariales)
pnpm create vite mi-app-react --template react

# Entrar al directorio e instalar dependencias
cd mi-app-react
npm install   # o pnpm install

# Iniciar el servidor de desarrollo
npm run dev
```

### Versiones recomendadas

```json
{
  "dependencies": {
    "react": "^19.0.0",
    "react-dom": "^19.0.0"
  },
  "devDependencies": {
    "@vitejs/plugin-react": "^4.3.0",
    "vite": "^6.0.0"
  }
}
```

> **💡 Tip empresarial:** En proyectos de producción, se recomienda fijar versiones exactas (`"react": "19.0.0"`) para evitar sorpresas en builds reproducibles. Usa herramientas como **Renovate** o **Dependabot** para automatizar las actualizaciones de dependencias de forma controlada.

---

## Materiales Complementarios y Recursos

### Documentación oficial

- [React 19 — Documentación oficial](https://react.dev)
- [What's New in React 19](https://react.dev/blog/2024/12/05/react-19)
- [React 19 Upgrade Guide](https://react.dev/blog/2024/04/25/react-19-upgrade-guide)

### Herramientas

- [Vite — Frontend Tooling](https://vitejs.dev)
- [React DevTools (extensión de navegador)](https://react.dev/learn/react-developer-tools)
- [Babel REPL — Transpilación JSX online](https://babeljs.io/repl)

### Cursos y referencias adicionales

- [React Learn (curso oficial interactivo)](https://react.dev/learn)
- [Kent C. Dodds — EpicReact.dev](https://epicreact.dev)
- [Josh W. Comeau — The Joy of React](https://www.joyofreact.com)

### Videos recomendados

- [React 19 — What's New? (Fireship)](https://www.youtube.com/watch?v=AJOGzVygGcY)
- [Understanding the React Compiler](https://www.youtube.com/watch?v=kjOacmVsLqE)

---

## Tips Empresariales Generales del Módulo

> **🏢 Tip 1 — React como estándar de la industria:** React es la biblioteca de UI más demandada en el mercado laboral (>40% de los proyectos front-end según Stack Overflow 2024). Aprenderlo bien es una inversión de carrera sólida.

> **🏢 Tip 2 — Consistencia sobre creatividad:** En equipos grandes, la arquitectura y convenciones importan más que las preferencias individuales. Define un estilo guía (`ESLint + Prettier + arquitectura acordada`) desde el primer día.

> **🏢 Tip 3 — Actualización gradual:** Actualizar React en un proyecto grande requiere planificación. Usa las [codemods oficiales](https://react.dev/blog/2024/04/25/react-19-upgrade-guide#codemods) para automatizar cambios de breaking changes.

> **🏢 Tip 4 — Testing desde el inicio:** Los proyectos que integran testing desde el día cero (React Testing Library + Vitest) tienen hasta un 60% menos de regresiones en producción según estudios de Google Engineering.

---

## Evaluación del Módulo

| Actividad | Tipo | Peso |
|-----------|------|------|
| Ejercicio 1: Tarjeta de Producto | Resuelto (estudio) | — |
| Ejercicio 2: Lista de Tareas con React 19 | Resuelto (estudio) | — |
| Práctica 1: Sistema de Cards de Empleados | Individual | 33% |
| Práctica 2: Formulario con Actions | Individual | 33% |
| Práctica 3: Refactor con Diseño Atómico | Individual | 34% |

---

*Módulo 2 — Curso React 2026 · Prof. David Luna*
