# JavaScript Moderno (ES6+)

JavaScript ha evolucionado enormemente desde ES6 (2015). Conocer la sintaxis moderna no es opcional en un entorno empresarial: mejora la legibilidad, reduce errores y es la base para entender React a fondo.

---

## 1. Sintaxis Moderna: Arrow Functions y Template Literals

### Arrow Functions

Las **Arrow Functions** son una sintaxis concisa para declarar funciones. En React, son fundamentales para manejadores de eventos y callbacks.

```javascript
// Función tradicional
function saludar(nombre) {
  return `Hola, ${nombre}!`;
}

// Arrow function equivalente
const saludar = (nombre) => `Hola, ${nombre}!`;

// Con un solo parámetro, los paréntesis son opcionales
const duplicar = n => n * 2;

// Con cuerpo de bloque (necesita return explícito)
const calcularTotal = (precio, cantidad) => {
  const subtotal = precio * cantidad;
  const iva = subtotal * 0.16;
  return subtotal + iva;
};

// ⚠️ DIFERENCIA CLAVE: Arrow functions NO tienen su propio 'this'
// Esto es MUY importante en React para los manejadores de eventos

// ❌ Problema con función tradicional en clases
class ContadorAntiguo extends React.Component {
  constructor() {
    super();
    this.state = { count: 0 };
    this.incrementar = this.incrementar.bind(this); // Binding manual necesario
  }
  incrementar() {
    this.setState({ count: this.state.count + 1 }); // 'this' puede perderse
  }
}

// ✅ Arrow function — 'this' del contexto léxico
class ContadorModerno extends React.Component {
  state = { count: 0 };
  incrementar = () => {                              // 'this' siempre correcto
    this.setState({ count: this.state.count + 1 });
  };
}
```

---

### Template Literals (Template Strings)

Los **Template Literals** permiten interpolación de variables y strings multilinea.

```javascript
const usuario = { nombre: 'Ana García', rol: 'Admin', empresa: 'TechCorp' };
const fechaAcceso = new Date().toLocaleDateString('es-MX');

// ❌ Concatenación tradicional — ilegible
const mensaje = 'Bienvenida, ' + usuario.nombre + '. Tu rol es ' + usuario.rol + ' en ' + usuario.empresa;

// ✅ Template Literal — limpio y legible
const mensaje = `Bienvenida, ${usuario.nombre}. Tu rol es ${usuario.rol} en ${usuario.empresa}`;

// Expresiones dentro de ${}
const resumen = `
  Usuario: ${usuario.nombre}
  Rol: ${usuario.rol.toUpperCase()}
  Último acceso: ${fechaAcceso}
  Permisos: ${usuario.rol === 'Admin' ? 'Completos' : 'Limitados'}
`;

// Caso de uso en React: Clases CSS dinámicas
const claseBoton = `btn btn--${variante} ${deshabilitado ? 'btn--disabled' : ''}`.trim();

// Tagged Templates (avanzado) — base de styled-components
const consultaSQL = sql`
  SELECT * FROM usuarios
  WHERE empresa_id = ${empresaId}
  AND activo = true
  LIMIT ${limite}
`;
```

---

## 2. Destructuring y Spread Operator

### Destructuring de Objetos

Permite extraer propiedades de un objeto en variables independientes.

```javascript
const empleado = {
  id: 'EMP-001',
  nombre: 'Carlos Mendoza',
  departamento: 'Ingeniería',
  salario: 85000,
  direccion: {
    ciudad: 'Ciudad de México',
    pais: 'México'
  }
};

// ❌ Forma antigua
const nombre = empleado.nombre;
const departamento = empleado.departamento;

// ✅ Destructuring
const { nombre, departamento, salario } = empleado;

// Con alias (renombrar variables)
const { nombre: nombreEmpleado, salario: sueldo } = empleado;

// Con valores por defecto
const { nombre, bonus = 0 } = empleado;

// Destructuring anidado
const { nombre, direccion: { ciudad, pais } } = empleado;

// En parámetros de función — muy común en React
function TarjetaEmpleado({ nombre, departamento, salario = 0 }) {
  return <div>{nombre} - {departamento}</div>;
}

// Omitir propiedades con rest
const { salario, ...datosPublicos } = empleado;
// datosPublicos: { id, nombre, departamento, direccion }
```

---

### Destructuring de Arrays

```javascript
const coordenadas = [19.4326, -99.1332, 'Ciudad de México'];
const semana = ['Lunes', 'Martes', 'Miércoles', 'Jueves', 'Viernes'];

// Extracción por posición
const [latitud, longitud, ciudad] = coordenadas;

// Omitir elementos con comas
const [primero, , tercero] = coordenadas; // Omite el segundo

// Con valores por defecto
const [x = 0, y = 0, z = 0] = coordenadas;

// Rest en arrays
const [hoy, manana, ...restoDeSemana] = semana;
// restoDeSemana: ['Miércoles', 'Jueves', 'Viernes']

// 🌟 Caso de uso CRÍTICO en React: useState
const [contador, setContador] = React.useState(0);
const [usuario, setUsuario] = React.useState(null);
const [cargando, setCargando] = React.useState(false);
```

---

### Spread Operator (`...`)

El operador spread "expande" los elementos de un array u objeto.

```javascript
// Arrays
const frutas = ['manzana', 'pera'];
const verduras = ['zanahoria', 'espinaca'];
const alimentos = [...frutas, ...verduras, 'arroz'];
// ['manzana', 'pera', 'zanahoria', 'espinaca', 'arroz']

// Copia inmutable de un array (sin referencia compartida)
const original = [1, 2, 3];
const copia = [...original];

// Objetos — FUNDAMENTAL en React para actualizar el estado
const configuracion = { tema: 'claro', idioma: 'es', notificaciones: true };

// Actualizar una propiedad sin mutar el original
const nuevaConfig = { ...configuracion, tema: 'oscuro' };
// { tema: 'oscuro', idioma: 'es', notificaciones: true }

// En React — actualizar estado de objeto correctamente
const [config, setConfig] = React.useState({ tema: 'claro', idioma: 'es' });

// ❌ NUNCA mutes el estado directamente
config.tema = 'oscuro'; // ❌ React no detecta el cambio

// ✅ Siempre crea un nuevo objeto
setConfig(prev => ({ ...prev, tema: 'oscuro' })); // ✅

// Fusionar objetos (el último gana en conflictos)
const datosBase = { id: 1, nombre: 'Ana', activo: false };
const datosActualizados = { nombre: 'Ana García', activo: true };
const resultado = { ...datosBase, ...datosActualizados };
// { id: 1, nombre: 'Ana García', activo: true }

// Pasar todas las props a un componente hijo (usar con cuidado)
function Wrapper(props) {
  const { extraProp, ...propsRestantes } = props;
  return <ComponenteHijo {...propsRestantes} />;
}
```

---

## 3. Módulos ES6 (import / export)

Los módulos ES6 son el sistema oficial para organizar y reutilizar código JavaScript.

```javascript
// ============================================
// Tipos de exports
// ============================================

// 1. Named Export — puede haber múltiples por archivo
export const PI = 3.14159;
export function formatearMoneda(valor, moneda = 'MXN') {
  return new Intl.NumberFormat('es-MX', { style: 'currency', currency: moneda })
    .format(valor);
}
export class ServicioEmpleados { /* ... */ }

// 2. Default Export — solo uno por archivo (generalmente el componente principal)
export default function PaginaInicio() {
  return <main>Inicio</main>;
}

// ============================================
// Tipos de imports
// ============================================

// Named imports — nombres exactos entre llaves
import { formatearMoneda, ServicioEmpleados } from './utils/formato';

// Con alias para evitar conflictos de nombres
import { formatearMoneda as formatMoney } from './utils/formato';

// Default import — cualquier nombre
import PaginaInicio from './pages/PaginaInicio';
import React from 'react'; // React es un default export

// Mixto: default + named en una sola línea
import React, { useState, useEffect, useCallback } from 'react';

// Namespace import — todo el módulo como un objeto
import * as Utils from './utils/formato';
Utils.formatearMoneda(1500); // Uso

// Re-export desde un archivo índice (Barrel Export pattern)
// Archivo: src/components/index.js
export { default as Boton } from './Boton';
export { default as Modal } from './Modal';
export { default as Tabla } from './Tabla';
export { TarjetaEmpleado, TarjetaProducto } from './Tarjetas';

// Uso limpio:
import { Boton, Modal, TarjetaEmpleado } from '@/components';
```

---

### Organización de Archivos en un Proyecto React

```
src/
├── components/
│   ├── ui/              # Componentes base reutilizables
│   │   ├── Boton.jsx
│   │   ├── Modal.jsx
│   │   └── index.js     # Barrel file
│   └── features/        # Componentes de dominio
│       ├── empleados/
│       │   ├── TarjetaEmpleado.jsx
│       │   └── ListaEmpleados.jsx
│       └── dashboard/
│           └── GraficaVentas.jsx
├── hooks/               # Custom hooks
│   ├── useAuth.js
│   └── useLocalStorage.js
├── services/            # Lógica de comunicación con APIs
│   ├── api.js
│   └── empleadosService.js
├── utils/               # Funciones utilitarias puras
│   ├── formato.js
│   └── validaciones.js
└── pages/               # Páginas/rutas de la aplicación
    ├── Login.jsx
    └── Dashboard.jsx
```

---

## 4. Promises y Async/Await

El manejo de operaciones asíncronas es fundamental para cualquier aplicación que consuma APIs.

### El Problema del Código Asíncrono

```javascript
// La cadena del Callback Hell — ilegible e inmanejable
obtenerUsuario(id, function(usuario) {
  obtenerPerfil(usuario.perfilId, function(perfil) {
    obtenerPermisos(perfil.rol, function(permisos) {
      renderizarUI(usuario, perfil, permisos, function(resultado) {
        console.log('Listo'); // ¿Cómo manejo los errores de cada paso?
      });
    });
  });
});
```

---

### Promises

```javascript
// Una Promise representa una operación que completará en el futuro
// Estados: pending → fulfilled | rejected

function fetchEmpleado(id) {
  return fetch(`/api/empleados/${id}`)
    .then(response => {
      if (!response.ok) {
        throw new Error(`Error HTTP: ${response.status}`);
      }
      return response.json(); // Devuelve otra Promise
    });
}

// Encadenamiento de Promises (mejor que callbacks)
fetchEmpleado(1)
  .then(empleado => fetchDepartamento(empleado.departamentoId))
  .then(departamento => renderizarInfo(departamento))
  .catch(error => console.error('Error:', error))
  .finally(() => ocultarCargando());

// Promise.all — ejecutar múltiples promesas en PARALELO
Promise.all([
  fetchEmpleado(1),
  fetchDepartamento(5),
  fetchPermisos('admin')
]).then(([empleado, departamento, permisos]) => {
  // Los 3 datos disponibles al mismo tiempo
});

// Promise.allSettled — espera a todas sin que una falle las demás
Promise.allSettled([
  fetchEmpleado(1),
  fetchEmpleado(2),
  fetchEmpleado(999) // Este puede fallar
]).then(resultados => {
  resultados.forEach(({ status, value, reason }) => {
    if (status === 'fulfilled') console.log('Éxito:', value);
    else console.error('Error:', reason);
  });
});
```

---

### Async/Await — La Forma Moderna

```javascript
// async/await hace que el código asíncrono parezca síncrono

// ✅ Función async básica
async function obtenerDashboard(usuarioId) {
  try {
    // await "pausa" la función hasta que la Promise se resuelve
    const usuario = await fetchEmpleado(usuarioId);
    const departamento = await fetchDepartamento(usuario.departamentoId);
    const metricas = await fetchMetricas(departamento.id);

    return { usuario, departamento, metricas };
  } catch (error) {
    // Un solo catch para todos los errores
    console.error('Error al cargar el dashboard:', error);
    throw error; // Re-lanzar para que el llamador pueda manejarlo
  } finally {
    // Siempre se ejecuta (limpiar recursos, ocultar spinners)
    setCargando(false);
  }
}

// ✅ Paralelo con async/await — NO hacer una espera tras otra si no hay dependencia
async function obtenerDatosIniciales(usuarioId) {
  // ❌ Lento: espera uno por uno
  const perfil = await fetchPerfil(usuarioId);
  const notificaciones = await fetchNotificaciones(usuarioId);

  // ✅ Rápido: ambas peticiones en paralelo
  const [perfil, notificaciones] = await Promise.all([
    fetchPerfil(usuarioId),
    fetchNotificaciones(usuarioId)
  ]);

  return { perfil, notificaciones };
}

// ✅ Patrón en React con useEffect
function PerfilEmpleado({ empleadoId }) {
  const [empleado, setEmpleado] = React.useState(null);
  const [cargando, setCargando] = React.useState(true);
  const [error, setError] = React.useState(null);

  React.useEffect(() => {
    // Las funciones de useEffect no pueden ser async directamente
    // Se crea una función async interna
    async function cargarEmpleado() {
      try {
        setCargando(true);
        const datos = await fetchEmpleado(empleadoId);
        setEmpleado(datos);
      } catch (err) {
        setError(err.message);
      } finally {
        setCargando(false);
      }
    }

    cargarEmpleado();
  }, [empleadoId]);

  if (cargando) return <Skeleton />;
  if (error) return <MensajeError mensaje={error} />;
  return <TarjetaEmpleado datos={empleado} />;
}
```

---

## 5. Métodos de Array: map, filter, reduce

Estos tres métodos son la base de la **programación funcional** en JavaScript y se usan constantemente en React para renderizar listas y transformar datos.

### `map` — Transformar cada elemento

```javascript
const empleados = [
  { id: 1, nombre: 'Ana García', salario: 75000, departamento: 'Ingeniería' },
  { id: 2, nombre: 'Carlos López', salario: 65000, departamento: 'Ventas' },
  { id: 3, nombre: 'María Torres', salario: 80000, departamento: 'Ingeniería' },
];

// Transformar datos para la UI
const tarjetasEmpleados = empleados.map(emp => ({
  ...emp,
  salarioFormateado: new Intl.NumberFormat('es-MX', { style: 'currency', currency: 'MXN' }).format(emp.salario),
  iniciales: emp.nombre.split(' ').map(n => n[0]).join(''),
}));

// 🌟 Uso principal en React: Renderizar listas
function ListaEmpleados({ empleados }) {
  return (
    <ul>
      {empleados.map(empleado => (
        // La prop 'key' es OBLIGATORIA y debe ser única y estable
        <li key={empleado.id}>
          <TarjetaEmpleado datos={empleado} />
        </li>
      ))}
    </ul>
  );
}
```

---

### `filter` — Filtrar elementos

```javascript
// Filtrar empleados de Ingeniería
const equipoIngenieria = empleados.filter(emp => emp.departamento === 'Ingeniería');

// Filtrar con múltiples condiciones
const seniorBienPagados = empleados.filter(emp => 
  emp.nivel === 'Senior' && emp.salario > 70000
);

// 🌟 Caso de uso en React: Búsqueda en tiempo real
function BuscadorEmpleados({ empleados }) {
  const [busqueda, setBusqueda] = React.useState('');

  const resultados = empleados.filter(emp =>
    emp.nombre.toLowerCase().includes(busqueda.toLowerCase()) ||
    emp.departamento.toLowerCase().includes(busqueda.toLowerCase())
  );

  return (
    <>
      <input
        type="search"
        value={busqueda}
        onChange={e => setBusqueda(e.target.value)}
        placeholder="Buscar empleados..."
      />
      <p>{resultados.length} resultados</p>
      <ListaEmpleados empleados={resultados} />
    </>
  );
}
```

---

### `reduce` — Acumular/Agregar datos

```javascript
// Suma de salarios
const totalNomina = empleados.reduce((acumulado, emp) => acumulado + emp.salario, 0);

// Agrupar empleados por departamento
const porDepartamento = empleados.reduce((grupos, emp) => {
  const dept = emp.departamento;
  if (!grupos[dept]) grupos[dept] = [];
  grupos[dept].push(emp);
  return grupos;
}, {});
/* Resultado:
{
  'Ingeniería': [{ id:1, nombre: 'Ana...' }, { id:3, nombre: 'María...' }],
  'Ventas': [{ id:2, nombre: 'Carlos...' }]
}
*/

// Calcular métricas del dashboard
const metricas = empleados.reduce((stats, emp) => ({
  total: stats.total + 1,
  nomina: stats.nomina + emp.salario,
  promedioSalario: (stats.nomina + emp.salario) / (stats.total + 1),
}), { total: 0, nomina: 0, promedioSalario: 0 });
```

---

### Otros Métodos Esenciales de Array

```javascript
const numeros = [3, 1, 4, 1, 5, 9, 2, 6, 5, 3];
const productos = [
  { id: 1, nombre: 'Laptop', precio: 1200, stock: 5 },
  { id: 2, nombre: 'Mouse', precio: 25, stock: 0 },
  { id: 3, nombre: 'Monitor', precio: 450, stock: 3 },
];

// find — Primer elemento que cumple la condición (o undefined)
const laptop = productos.find(p => p.id === 1);

// findIndex — Índice del primer elemento que cumple la condición (o -1)
const indiceOutOfStock = productos.findIndex(p => p.stock === 0);

// some — ¿Algún elemento cumple la condición? (boolean)
const hayProductosSinStock = productos.some(p => p.stock === 0); // true

// every — ¿Todos los elementos cumplen la condición? (boolean)
const todosConStock = productos.every(p => p.stock > 0); // false

// includes — ¿El array incluye este valor? (boolean)
const tieneReact = ['React', 'Vue', 'Angular'].includes('React'); // true

// flat / flatMap — Aplanar arrays anidados
const pedidosPorCliente = [[1, 2, 3], [4, 5], [6]];
const todosPedidos = pedidosPorCliente.flat(); // [1, 2, 3, 4, 5, 6]

// sort — Ordenar (CUIDADO: muta el array original, usa spread primero)
const preciosOrdenados = [...productos]
  .sort((a, b) => a.precio - b.precio); // Ascendente por precio

// Encadenamiento de métodos (pipeline de datos)
const resumenDashboard = empleados
  .filter(emp => emp.activo)                         // Solo activos
  .map(emp => ({ ...emp, bonificacion: emp.salario * 0.1 })) // Añadir bonificación
  .sort((a, b) => b.salario - a.salario)             // Ordenar por salario desc
  .slice(0, 5);                                      // Top 5
```

---

## 6. Optional Chaining y Nullish Coalescing

Estas dos características de ES2020 eliminan por completo el clásico error "Cannot read property X of undefined/null".

### Optional Chaining (`?.`)

Accede a propiedades anidadas de forma segura sin lanzar error si el objeto es `null` o `undefined`.

```javascript
const respuestaAPI = {
  data: {
    usuario: {
      perfil: {
        // avatar puede o no existir
        avatar: { url: 'https://...', tamanio: 128 }
      }
    }
  }
};

// ❌ Forma antigua — frágil y verbosa
const urlAvatar = respuestaAPI 
  && respuestaAPI.data 
  && respuestaAPI.data.usuario 
  && respuestaAPI.data.usuario.perfil
  && respuestaAPI.data.usuario.perfil.avatar
  && respuestaAPI.data.usuario.perfil.avatar.url;

// ✅ Optional Chaining — conciso y seguro
const urlAvatar = respuestaAPI?.data?.usuario?.perfil?.avatar?.url;
// Si alguna parte es null/undefined → devuelve undefined (sin error)

// También funciona con métodos
const nombreUpper = usuario?.nombre?.toUpperCase();

// Y con arrays
const primerPermiso = usuario?.permisos?.[0];

// 🌟 Caso de uso en React — datos que llegan de API
function PerfilUsuario({ usuario }) {
  return (
    <div>
      <img src={usuario?.avatar?.url ?? '/default-avatar.png'} alt="Avatar" />
      <h1>{usuario?.nombre ?? 'Usuario Anónimo'}</h1>
      <span>{usuario?.departamento?.nombre ?? 'Sin departamento'}</span>
    </div>
  );
}
```

---

### Nullish Coalescing (`??`)

Devuelve el valor del lado derecho **solo si el lado izquierdo es `null` o `undefined`**. Es diferente del operador `||` que también activa con `0`, `''` o `false`.

```javascript
// Problema con el operador || (OR lógico)
const cantidad = 0;
const cantidadMostrada = cantidad || 'Sin cantidad'; // ❌ Devuelve 'Sin cantidad' (0 es falsy)

// ✅ Nullish Coalescing — solo null o undefined activan el fallback
const cantidadMostrada = cantidad ?? 'Sin cantidad'; // ✅ Devuelve 0

// Más ejemplos prácticos
const configuracion = {
  reintentos: 0,           // valor válido (no debe reemplazarse)
  timeout: null,           // null → debe reemplazarse
  debug: false,            // false válido (no debe reemplazarse)
};

const reintentos = configuracion.reintentos ?? 3;  // 0 (no reemplaza)
const timeout = configuracion.timeout ?? 5000;     // 5000 (reemplaza null)
const debug = configuracion.debug ?? true;         // false (no reemplaza)

// Combinando Optional Chaining + Nullish Coalescing
const valorConfig = objeto?.ruta?.profunda?.valor ?? 'valor por defecto';

// Nullish Assignment (??=) — Asignar solo si es null/undefined
let usuario = { nombre: 'Ana', rol: null };
usuario.rol ??= 'viewer'; // Asigna 'viewer' porque rol es null
usuario.nombre ??= 'Anónimo'; // NO asigna porque nombre ya tiene valor
```

---

## 7. Otras Características Modernas Esenciales

### Object Shorthand y Computed Properties

```javascript
const nombre = 'Ana García';
const departamento = 'Ingeniería';
const activo = true;

// ❌ Repetición innecesaria
const empleado = { nombre: nombre, departamento: departamento, activo: activo };

// ✅ Shorthand — cuando la clave y la variable tienen el mismo nombre
const empleado = { nombre, departamento, activo };

// Computed Properties — claves dinámicas
const campo = 'email';
const valor = 'ana@empresa.com';
const actualizacion = { [campo]: valor }; // { email: 'ana@empresa.com' }
```

---

### Clases y Herencia

```javascript
// Base para servicios de API en proyectos empresariales
class ServicioBase {
  #baseURL; // Campo privado (ES2022)
  #headers;

  constructor(baseURL, token) {
    this.#baseURL = baseURL;
    this.#headers = {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${token}`,
    };
  }

  async get(endpoint) {
    const response = await fetch(`${this.#baseURL}${endpoint}`, {
      headers: this.#headers,
    });
    if (!response.ok) throw new Error(`Error ${response.status}`);
    return response.json();
  }

  async post(endpoint, datos) {
    const response = await fetch(`${this.#baseURL}${endpoint}`, {
      method: 'POST',
      headers: this.#headers,
      body: JSON.stringify(datos),
    });
    if (!response.ok) throw new Error(`Error ${response.status}`);
    return response.json();
  }
}

class ServicioEmpleados extends ServicioBase {
  constructor(token) {
    super('/api/v1', token);
  }

  obtenerTodos() {
    return this.get('/empleados');
  }

  obtenerPorId(id) {
    return this.get(`/empleados/${id}`);
  }

  crear(datos) {
    return this.post('/empleados', datos);
  }
}
```

---

### Iteradores Modernos

```javascript
// for...of — iterar valores de cualquier iterable
const frutas = ['manzana', 'pera', 'uva'];
for (const fruta of frutas) {
  console.log(fruta); // 'manzana', 'pera', 'uva'
}

// entries() — índice y valor simultáneamente
for (const [indice, fruta] of frutas.entries()) {
  console.log(`${indice}: ${fruta}`); // '0: manzana', '1: pera', ...
}

// for...in — iterar CLAVES de un objeto (usar con cuidado)
const config = { tema: 'oscuro', idioma: 'es' };
for (const clave in config) {
  console.log(`${clave}: ${config[clave]}`);
}

// Object.entries() — transforma objeto en array de pares [clave, valor]
Object.entries(config).forEach(([clave, valor]) => {
  console.log(`${clave}: ${valor}`);
});

// Object.keys() y Object.values()
const claves = Object.keys(config);   // ['tema', 'idioma']
const valores = Object.values(config); // ['oscuro', 'es']
```

---

## 📚 Recursos Adicionales

| Recurso | Descripción | Nivel |
|---------|-------------|-------|
| [JavaScript.info](https://javascript.info/) | La guía más completa y moderna de JavaScript | Todos |
| [MDN: JavaScript Reference](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference) | Referencia oficial | Todos |
| [ES6+ Features (exploringjs.com)](https://exploringjs.com/es6/) | Libro gratuito online de ES6 en profundidad | Intermedio |
| [JavaScript Visualized (Lydia Hallie)](https://dev.to/lydiahallie/javascript-visualized-event-loop-3dif) | Serie visual sobre el event loop y async | Intermedio |
| [You Don't Know JS](https://github.com/getify/You-Dont-Know-JS) | Serie de libros gratuita sobre JS profundo | Avanzado |
| [Promises A+ Spec](https://promisesaplus.com/) | Especificación técnica de las Promises | Avanzado |
| [Array Methods Cheatsheet](https://www.array-methods.info/) | Cheatsheet interactivo de métodos de array | Básico |
| [Optional Chaining MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining) | Documentación oficial | Básico |
