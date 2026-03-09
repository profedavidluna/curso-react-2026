# Ejercicio Resuelto 2: Lista de Tareas con React 19

**Nivel:** Intermedio  
**Tiempo estimado:** 75-100 minutos  
**Conceptos:** React 19 Actions, `useActionState`, `useOptimistic`, JSX avanzado, renderizado condicional, listas y keys

---

## Enunciado

Construir una aplicación de gestión de tareas (To-Do List) que demuestre las características nuevas de React 19. La aplicación debe simular comunicación con un servidor y usar las nuevas APIs de React 19 para manejar el estado del formulario y las actualizaciones optimistas.

### Requisitos funcionales

1. **Listar tareas** obtenidas de una API simulada (con delay de red).
2. **Añadir nueva tarea** usando un formulario con `useActionState`.
3. **Completar/descompletar tarea** usando actualizaciones optimistas con `useOptimistic`.
4. **Eliminar tarea** con confirmación optimista.
5. **Gestión de errores**: Si la operación falla (error simulado), revertir el cambio optimista.
6. **Estado de carga**: El botón de submit debe deshabilitarse mientras se procesa la acción.

---

## Solución paso a paso

### Paso 1: Configurar servicios simulados de API

```js
// src/services/tareas.js

// Base de datos en memoria para simular el servidor
let tareasDB = [
  { id: 1, texto: 'Revisar documentación de React 19', completada: false, creadaEn: new Date('2026-01-01') },
  { id: 2, texto: 'Configurar el React Compiler en el proyecto', completada: false, creadaEn: new Date('2026-01-02') },
  { id: 3, texto: 'Migrar formularios a useActionState', completada: true, creadaEn: new Date('2026-01-03') },
  { id: 4, texto: 'Implementar Server Components con Next.js', completada: false, creadaEn: new Date('2026-01-04') },
];

let nextId = 5;

// Simula latencia de red
const delay = (ms) => new Promise(resolve => setTimeout(resolve, ms));

// Simula errores aleatorios (10% de probabilidad)
const simularError = () => Math.random() < 0.1;

export async function obtenerTareas() {
  await delay(500); // Simula 500ms de latencia
  return [...tareasDB];
}

export async function agregarTarea(texto) {
  await delay(600);
  if (simularError()) {
    throw new Error('Error de red: No se pudo guardar la tarea');
  }
  const nuevaTarea = {
    id: nextId++,
    texto: texto.trim(),
    completada: false,
    creadaEn: new Date(),
  };
  tareasDB = [...tareasDB, nuevaTarea];
  return nuevaTarea;
}

export async function toggleTarea(id) {
  await delay(400);
  if (simularError()) {
    throw new Error('Error al actualizar la tarea');
  }
  tareasDB = tareasDB.map(t =>
    t.id === id ? { ...t, completada: !t.completada } : t
  );
  return tareasDB.find(t => t.id === id);
}

export async function eliminarTarea(id) {
  await delay(400);
  if (simularError()) {
    throw new Error('Error al eliminar la tarea');
  }
  tareasDB = tareasDB.filter(t => t.id !== id);
}
```

### Paso 2: Componentes atómicos necesarios

```jsx
// src/components/atoms/Checkbox/Checkbox.jsx
function Checkbox({ id, checked, onChange, label }) {
  return (
    <label
      htmlFor={id}
      style={{
        display: 'flex',
        alignItems: 'center',
        gap: '10px',
        cursor: 'pointer',
        userSelect: 'none',
      }}
    >
      <input
        type="checkbox"
        id={id}
        checked={checked}
        onChange={onChange}
        style={{
          width: '18px',
          height: '18px',
          cursor: 'pointer',
          accentColor: '#0070f3',
        }}
      />
      <span
        style={{
          textDecoration: checked ? 'line-through' : 'none',
          color: checked ? '#9ca3af' : '#111827',
          transition: 'all 0.2s',
        }}
      >
        {label}
      </span>
    </label>
  );
}

export default Checkbox;
```

### Paso 3: Componente de item de tarea con actualizaciones optimistas

```jsx
// src/components/molecules/TareaItem/TareaItem.jsx
import Checkbox from '../../atoms/Checkbox/Checkbox';

function TareaItem({ tarea, onToggle, onEliminar }) {
  const fechaFormateada = tarea.creadaEn.toLocaleDateString('es-ES', {
    day: 'numeric',
    month: 'short',
  });

  return (
    <li
      style={{
        display: 'flex',
        alignItems: 'center',
        justifyContent: 'space-between',
        padding: '14px 16px',
        borderRadius: '8px',
        backgroundColor: tarea.completada ? '#f9fafb' : 'white',
        border: '1px solid #e5e7eb',
        transition: 'all 0.2s',
        opacity: tarea._optimistic ? 0.6 : 1, // Visual feedback de operación pendiente
      }}
    >
      <Checkbox
        id={`tarea-${tarea.id}`}
        checked={tarea.completada}
        onChange={() => onToggle(tarea.id)}
        label={tarea.texto}
      />

      <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
        <span style={{ fontSize: '12px', color: '#9ca3af' }}>
          {fechaFormateada}
        </span>
        <button
          onClick={() => onEliminar(tarea.id)}
          aria-label={`Eliminar tarea: ${tarea.texto}`}
          style={{
            background: 'none',
            border: 'none',
            cursor: 'pointer',
            color: '#ef4444',
            fontSize: '16px',
            padding: '4px',
            borderRadius: '4px',
            lineHeight: 1,
          }}
        >
          ✕
        </button>
      </div>
    </li>
  );
}

export default TareaItem;
```

### Paso 4: Formulario con useActionState (React 19)

```jsx
// src/components/molecules/FormNuevaTarea/FormNuevaTarea.jsx
import { useActionState } from 'react';
import { agregarTarea } from '../../../services/tareas';

// La acción recibe el estado anterior y los datos del formulario
async function accionAgregarTarea(estadoAnterior, formData) {
  const texto = formData.get('texto');

  // Validación
  if (!texto || texto.trim().length === 0) {
    return { error: 'El texto de la tarea no puede estar vacío.', exito: false };
  }
  if (texto.trim().length > 200) {
    return { error: 'La tarea no puede superar 200 caracteres.', exito: false };
  }

  try {
    const nuevaTarea = await agregarTarea(texto);
    return { error: null, exito: true, tarea: nuevaTarea };
  } catch (err) {
    return { error: err.message, exito: false };
  }
}

function FormNuevaTarea({ onTareaAgregada }) {
  const [estado, dispatch, isPending] = useActionState(
    async (estadoAnterior, formData) => {
      const resultado = await accionAgregarTarea(estadoAnterior, formData);
      if (resultado.exito && resultado.tarea) {
        onTareaAgregada(resultado.tarea); // Notificar al padre
      }
      return resultado;
    },
    { error: null, exito: false }
  );

  return (
    <form
      action={dispatch}
      style={{ display: 'flex', flexDirection: 'column', gap: '12px' }}
    >
      <div style={{ display: 'flex', gap: '8px' }}>
        <input
          name="texto"
          type="text"
          placeholder="¿Qué necesitas hacer?"
          maxLength={200}
          required
          disabled={isPending}
          style={{
            flex: 1,
            padding: '10px 14px',
            borderRadius: '8px',
            border: estado.error ? '1px solid #ef4444' : '1px solid #d1d5db',
            fontSize: '14px',
            outline: 'none',
          }}
        />
        <button
          type="submit"
          disabled={isPending}
          style={{
            padding: '10px 20px',
            borderRadius: '8px',
            border: 'none',
            backgroundColor: isPending ? '#93c5fd' : '#0070f3',
            color: 'white',
            fontWeight: 600,
            cursor: isPending ? 'not-allowed' : 'pointer',
            whiteSpace: 'nowrap',
            fontSize: '14px',
          }}
        >
          {isPending ? '⏳ Guardando...' : '+ Añadir'}
        </button>
      </div>

      {/* Mensajes de error/éxito */}
      {estado.error && (
        <p role="alert" style={{ color: '#ef4444', fontSize: '13px', margin: 0 }}>
          ⚠️ {estado.error}
        </p>
      )}
      {estado.exito && (
        <p style={{ color: '#059669', fontSize: '13px', margin: 0 }}>
          ✅ Tarea añadida correctamente
        </p>
      )}
    </form>
  );
}

export default FormNuevaTarea;
```

**¿Por qué `useActionState` aquí?**
- Elimina los `useState` manuales para `cargando`, `error` y `exito`.
- El botón de submit se deshabilita automáticamente mientras `isPending` es `true`.
- La acción recibe directamente el `FormData`, sin necesidad de `e.preventDefault()`.

### Paso 5: Lista de tareas con useOptimistic

```jsx
// src/components/organisms/ListaTareas/ListaTareas.jsx
import { useOptimistic } from 'react';
import TareaItem from '../../molecules/TareaItem/TareaItem';
import { toggleTarea, eliminarTarea } from '../../../services/tareas';

function ListaTareas({ tareas, onTareasActualizadas }) {
  // useOptimistic: muestra el estado "esperado" inmediatamente
  // mientras la operación real (async) se completa en segundo plano
  const [tareasOptimistas, actualizarOptimista] = useOptimistic(
    tareas,
    (tareasActuales, accion) => {
      switch (accion.tipo) {
        case 'TOGGLE':
          return tareasActuales.map(t =>
            t.id === accion.id
              ? { ...t, completada: !t.completada, _optimistic: true }
              : t
          );
        case 'ELIMINAR':
          return tareasActuales.filter(t => t.id !== accion.id);
        default:
          return tareasActuales;
      }
    }
  );

  const handleToggle = async (id) => {
    // 1. Actualización optimista: la UI cambia INMEDIATAMENTE
    actualizarOptimista({ tipo: 'TOGGLE', id });

    try {
      // 2. Operación real en el servidor
      await toggleTarea(id);
      // 3. Sincronizar el estado real del padre
      onTareasActualizadas(prev =>
        prev.map(t => t.id === id ? { ...t, completada: !t.completada } : t)
      );
    } catch (err) {
      // 4. Si falla, React revierte automáticamente el estado optimista
      console.error('Error al actualizar:', err.message);
      // Opcional: mostrar un toast de error
    }
  };

  const handleEliminar = async (id) => {
    actualizarOptimista({ tipo: 'ELIMINAR', id });

    try {
      await eliminarTarea(id);
      onTareasActualizadas(prev => prev.filter(t => t.id !== id));
    } catch (err) {
      console.error('Error al eliminar:', err.message);
    }
  };

  if (tareasOptimistas.length === 0) {
    return (
      <div
        style={{
          textAlign: 'center',
          padding: '48px 24px',
          color: '#9ca3af',
        }}
      >
        <p style={{ fontSize: '48px', margin: '0 0 16px' }}>✅</p>
        <p>¡No tienes tareas pendientes!</p>
      </div>
    );
  }

  const pendientes = tareasOptimistas.filter(t => !t.completada);
  const completadas = tareasOptimistas.filter(t => t.completada);

  return (
    <div style={{ display: 'flex', flexDirection: 'column', gap: '24px' }}>
      {/* Tareas pendientes */}
      {pendientes.length > 0 && (
        <section>
          <h3 style={{ margin: '0 0 12px', color: '#374151', fontSize: '14px', fontWeight: 600 }}>
            PENDIENTES ({pendientes.length})
          </h3>
          <ul style={{ listStyle: 'none', padding: 0, margin: 0, display: 'flex', flexDirection: 'column', gap: '8px' }}>
            {pendientes.map(tarea => (
              <TareaItem
                key={tarea.id}
                tarea={tarea}
                onToggle={handleToggle}
                onEliminar={handleEliminar}
              />
            ))}
          </ul>
        </section>
      )}

      {/* Tareas completadas */}
      {completadas.length > 0 && (
        <section>
          <h3 style={{ margin: '0 0 12px', color: '#9ca3af', fontSize: '14px', fontWeight: 600 }}>
            COMPLETADAS ({completadas.length})
          </h3>
          <ul style={{ listStyle: 'none', padding: 0, margin: 0, display: 'flex', flexDirection: 'column', gap: '8px' }}>
            {completadas.map(tarea => (
              <TareaItem
                key={tarea.id}
                tarea={tarea}
                onToggle={handleToggle}
                onEliminar={handleEliminar}
              />
            ))}
          </ul>
        </section>
      )}
    </div>
  );
}

export default ListaTareas;
```

### Paso 6: Componente principal con carga inicial

```jsx
// src/App.jsx
import { useState, useEffect } from 'react';
import FormNuevaTarea from './components/molecules/FormNuevaTarea/FormNuevaTarea';
import ListaTareas from './components/organisms/ListaTareas/ListaTareas';
import { obtenerTareas } from './services/tareas';

function App() {
  const [tareas, setTareas] = useState([]);
  const [cargandoInicial, setCargandoInicial] = useState(true);

  // Carga inicial de tareas
  useEffect(() => {
    obtenerTareas()
      .then(datos => setTareas(datos))
      .finally(() => setCargandoInicial(false));
  }, []);

  const handleTareaAgregada = (nuevaTarea) => {
    setTareas(prev => [...prev, nuevaTarea]);
  };

  const totalPendientes = tareas.filter(t => !t.completada).length;

  return (
    <div
      style={{
        minHeight: '100vh',
        backgroundColor: '#f9fafb',
        display: 'flex',
        flexDirection: 'column',
        alignItems: 'center',
        padding: '40px 16px',
      }}
    >
      <div style={{ width: '100%', maxWidth: '600px' }}>
        {/* Cabecera */}
        <div style={{ marginBottom: '32px', textAlign: 'center' }}>
          <h1 style={{ margin: '0 0 8px', fontSize: '28px', fontWeight: 800, color: '#111827' }}>
            📋 Mis Tareas
          </h1>
          <p style={{ margin: 0, color: '#6b7280' }}>
            {cargandoInicial
              ? 'Cargando...'
              : `${totalPendientes} ${totalPendientes === 1 ? 'tarea pendiente' : 'tareas pendientes'}`
            }
          </p>
        </div>

        {/* Formulario para nueva tarea */}
        <div
          style={{
            backgroundColor: 'white',
            borderRadius: '12px',
            padding: '20px',
            border: '1px solid #e5e7eb',
            marginBottom: '24px',
            boxShadow: '0 1px 3px rgba(0,0,0,0.1)',
          }}
        >
          <h2 style={{ margin: '0 0 16px', fontSize: '16px', color: '#374151' }}>
            Nueva tarea
          </h2>
          <FormNuevaTarea onTareaAgregada={handleTareaAgregada} />
        </div>

        {/* Lista de tareas */}
        <div
          style={{
            backgroundColor: 'white',
            borderRadius: '12px',
            padding: '20px',
            border: '1px solid #e5e7eb',
            boxShadow: '0 1px 3px rgba(0,0,0,0.1)',
          }}
        >
          {cargandoInicial ? (
            <div style={{ textAlign: 'center', padding: '48px', color: '#9ca3af' }}>
              <p style={{ fontSize: '32px' }}>⏳</p>
              <p>Cargando tareas...</p>
            </div>
          ) : (
            <ListaTareas
              tareas={tareas}
              onTareasActualizadas={setTareas}
            />
          )}
        </div>

        {/* Nota técnica */}
        <p style={{ marginTop: '24px', fontSize: '12px', color: '#9ca3af', textAlign: 'center' }}>
          💡 Usando React 19: <code>useActionState</code> + <code>useOptimistic</code>
        </p>
      </div>
    </div>
  );
}

export default App;
```

---

## Análisis de las APIs de React 19 utilizadas

### useActionState

```
accionAgregarTarea(estadoAnterior, formData) → nuevoEstado
         ↓
  [estado, dispatch, isPending] = useActionState(accion, estadoInicial)
         ↓
  <form action={dispatch}>  ←  React 19 acepta funciones async aquí
```

**Ventajas sobre el patrón anterior:**
- No necesitas `e.preventDefault()`.
- `isPending` es automático (no hay `useState` para `loading`).
- El estado del formulario es colocado explícitamente.

### useOptimistic

```
[estadoOptimista, actualizarOptimista] = useOptimistic(
  estadoReal,
  (estadoActual, accion) => estadoTransformado  ← función pura
)
```

**Flujo de ejecución:**
1. Usuario hace clic → `actualizarOptimista(accion)` → UI actualiza INMEDIATAMENTE.
2. La operación async corre en segundo plano.
3. Si éxito → `estadoReal` se actualiza → `estadoOptimista` converge.
4. Si error → `estadoOptimista` **revierte automáticamente** al `estadoReal`.

---

## Resultado esperado

- Lista de 4 tareas cargadas al inicio (con spinner de carga).
- Formulario con validación: no permite tareas vacías.
- Al añadir una tarea: el botón muestra "⏳ Guardando..." y se deshabilita.
- Al marcar/desmarcar: el cambio es inmediato visualmente.
- Al eliminar: la tarea desaparece inmediatamente.
- En ~10% de las operaciones verás en consola errores simulados y el estado revertirá.

---

## Extensiones propuestas

1. **Filtros**: Botones para mostrar todas / pendientes / completadas.
2. **Reordenamiento**: Drag & drop para priorizar tareas.
3. **Toast de error**: Mostrar una notificación cuando una operación falla.
4. **Fechas límite**: Añadir una fecha de vencimiento a las tareas.
5. **Categorías**: Agrupar tareas por proyecto o etiqueta.

---

## Tips Empresariales del Ejercicio

> **🏢 Tip 1 — Actualizaciones optimistas en producción:** Las apps como Notion, Linear y Jira usan este patrón extensivamente. La percepción de velocidad es fundamental para la UX empresarial: los usuarios toleran mejor los errores ocasionales que la latencia constante.

> **🏢 Tip 2 — Manejo de errores con rollback:** Siempre muestra feedback cuando una operación optimista falla. Un toast de error + el rollback visual mantiene la confianza del usuario en la aplicación.

> **🏢 Tip 3 — Servidor como fuente de verdad:** `useOptimistic` no reemplaza la sincronización con el servidor. Siempre actualiza el estado real (`setTareas`) después de una operación exitosa para evitar discrepancias.

---

*← [Ejercicio 1: Tarjeta de Producto](./ejercicio-1-componente-tarjeta-producto.md) | [Volver al índice](../../README.md)*
