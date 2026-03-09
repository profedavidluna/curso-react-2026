# Ejercicio de Práctica 3: Refactor con Diseño Atómico

**Nivel:** Intermedio-Avanzado  
**Tiempo estimado:** 80-100 minutos  
**Conceptos:** Diseño atómico, refactorización, organización de proyectos, barrel exports, alias de rutas

---

## Contexto empresarial

**MediApp** es una aplicación de gestión de citas médicas. El equipo de desarrollo original trabajó rápido y creó un componente monolítico `Dashboard.jsx` de 350 líneas que hace demasiadas cosas. Tú eres el nuevo desarrollador Senior y debes refactorizar el componente aplicando el Diseño Atómico.

Este tipo de tarea es muy común en entornos empresariales: heredar código funcionalmente correcto pero difícil de mantener o extender.

---

## Objetivo

Refactorizar el componente monolítico `Dashboard.jsx` en una arquitectura de Diseño Atómico manteniendo exactamente el mismo comportamiento visual y funcional.

---

## Código a refactorizar

Crea el archivo `src/Dashboard.jsx` con el siguiente código (esto es lo que RECIBES, no lo que debes entregar):

```jsx
// src/Dashboard.jsx  ← Código legado a refactorizar
import { useState } from 'react';

const CITAS = [
  { id: 1, paciente: 'Laura Morales', doctor: 'Dr. Pérez', especialidad: 'Cardiología', fecha: '2026-03-15', hora: '09:00', estado: 'confirmada' },
  { id: 2, paciente: 'Juan Rodríguez', doctor: 'Dra. Gómez', especialidad: 'Pediatría', fecha: '2026-03-15', hora: '10:30', estado: 'pendiente' },
  { id: 3, paciente: 'Elena Castro', doctor: 'Dr. Martínez', especialidad: 'Dermatología', fecha: '2026-03-16', hora: '11:00', estado: 'confirmada' },
  { id: 4, paciente: 'Pedro Vega', doctor: 'Dra. Silva', especialidad: 'Neurología', fecha: '2026-03-16', hora: '14:00', estado: 'cancelada' },
  { id: 5, paciente: 'María Santos', doctor: 'Dr. Pérez', especialidad: 'Cardiología', fecha: '2026-03-17', hora: '09:30', estado: 'pendiente' },
];

export default function Dashboard() {
  const [filtroEstado, setFiltroEstado] = useState('todas');
  const [busqueda, setBusqueda] = useState('');

  const citasFiltradas = CITAS.filter(cita => {
    const coincideEstado = filtroEstado === 'todas' || cita.estado === filtroEstado;
    const coincideBusqueda =
      cita.paciente.toLowerCase().includes(busqueda.toLowerCase()) ||
      cita.doctor.toLowerCase().includes(busqueda.toLowerCase());
    return coincideEstado && coincideBusqueda;
  });

  const stats = {
    total: CITAS.length,
    confirmadas: CITAS.filter(c => c.estado === 'confirmada').length,
    pendientes: CITAS.filter(c => c.estado === 'pendiente').length,
    canceladas: CITAS.filter(c => c.estado === 'cancelada').length,
  };

  const colorEstado = {
    confirmada: '#059669',
    pendiente:  '#d97706',
    cancelada:  '#ef4444',
  };

  return (
    <div style={{ padding: '24px', maxWidth: '1100px', margin: '0 auto', fontFamily: 'sans-serif' }}>

      {/* Header */}
      <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginBottom: '32px' }}>
        <div>
          <h1 style={{ margin: 0, fontSize: '24px', fontWeight: 700 }}>🏥 MediApp Dashboard</h1>
          <p style={{ margin: '4px 0 0', color: '#6b7280' }}>Gestión de citas médicas</p>
        </div>
        <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
          <div style={{ width: '36px', height: '36px', borderRadius: '50%', backgroundColor: '#0070f3', color: 'white', display: 'flex', alignItems: 'center', justifyContent: 'center', fontWeight: 700 }}>
            A
          </div>
          <div>
            <div style={{ fontWeight: 600, fontSize: '14px' }}>Admin</div>
            <div style={{ fontSize: '12px', color: '#6b7280' }}>Administrador</div>
          </div>
        </div>
      </div>

      {/* Stats */}
      <div style={{ display: 'grid', gridTemplateColumns: 'repeat(4, 1fr)', gap: '16px', marginBottom: '32px' }}>
        {[
          { label: 'Total', valor: stats.total, color: '#0070f3', icono: '📋' },
          { label: 'Confirmadas', valor: stats.confirmadas, color: '#059669', icono: '✅' },
          { label: 'Pendientes', valor: stats.pendientes, color: '#d97706', icono: '⏳' },
          { label: 'Canceladas', valor: stats.canceladas, color: '#ef4444', icono: '❌' },
        ].map(stat => (
          <div key={stat.label} style={{ backgroundColor: 'white', border: '1px solid #e5e7eb', borderRadius: '12px', padding: '20px' }}>
            <div style={{ fontSize: '24px', marginBottom: '8px' }}>{stat.icono}</div>
            <div style={{ fontSize: '28px', fontWeight: 700, color: stat.color }}>{stat.valor}</div>
            <div style={{ fontSize: '13px', color: '#6b7280' }}>{stat.label}</div>
          </div>
        ))}
      </div>

      {/* Filtros y búsqueda */}
      <div style={{ display: 'flex', gap: '16px', marginBottom: '20px', flexWrap: 'wrap' }}>
        <input
          type="search"
          placeholder="Buscar por paciente o doctor..."
          value={busqueda}
          onChange={e => setBusqueda(e.target.value)}
          style={{ flex: 1, minWidth: '200px', padding: '10px 14px', borderRadius: '8px', border: '1px solid #d1d5db', fontSize: '14px' }}
        />
        <div style={{ display: 'flex', gap: '8px' }}>
          {['todas', 'confirmada', 'pendiente', 'cancelada'].map(estado => (
            <button
              key={estado}
              onClick={() => setFiltroEstado(estado)}
              style={{
                padding: '10px 16px',
                borderRadius: '8px',
                border: '1px solid',
                borderColor: filtroEstado === estado ? '#0070f3' : '#d1d5db',
                backgroundColor: filtroEstado === estado ? '#0070f3' : 'white',
                color: filtroEstado === estado ? 'white' : '#374151',
                cursor: 'pointer',
                fontSize: '13px',
                fontWeight: 500,
                textTransform: 'capitalize',
              }}
            >
              {estado === 'todas' ? 'Todas' : estado.charAt(0).toUpperCase() + estado.slice(1)}
            </button>
          ))}
        </div>
      </div>

      {/* Tabla de citas */}
      <div style={{ backgroundColor: 'white', borderRadius: '12px', border: '1px solid #e5e7eb', overflow: 'hidden' }}>
        <table style={{ width: '100%', borderCollapse: 'collapse' }}>
          <thead>
            <tr style={{ backgroundColor: '#f9fafb' }}>
              {['Paciente', 'Doctor', 'Especialidad', 'Fecha', 'Hora', 'Estado'].map(col => (
                <th key={col} style={{ padding: '12px 16px', textAlign: 'left', fontSize: '12px', fontWeight: 600, color: '#6b7280', textTransform: 'uppercase', letterSpacing: '0.05em' }}>
                  {col}
                </th>
              ))}
            </tr>
          </thead>
          <tbody>
            {citasFiltradas.length === 0 ? (
              <tr>
                <td colSpan={6} style={{ padding: '32px', textAlign: 'center', color: '#9ca3af' }}>
                  No se encontraron citas con los filtros aplicados.
                </td>
              </tr>
            ) : (
              citasFiltradas.map((cita, idx) => (
                <tr key={cita.id} style={{ borderTop: '1px solid #f3f4f6', backgroundColor: idx % 2 === 0 ? 'white' : '#fafafa' }}>
                  <td style={{ padding: '14px 16px', fontSize: '14px', fontWeight: 500 }}>{cita.paciente}</td>
                  <td style={{ padding: '14px 16px', fontSize: '14px', color: '#4b5563' }}>{cita.doctor}</td>
                  <td style={{ padding: '14px 16px', fontSize: '14px', color: '#4b5563' }}>{cita.especialidad}</td>
                  <td style={{ padding: '14px 16px', fontSize: '14px', color: '#4b5563' }}>
                    {new Date(cita.fecha + 'T00:00:00').toLocaleDateString('es-ES', { day: 'numeric', month: 'short', year: 'numeric' })}
                  </td>
                  <td style={{ padding: '14px 16px', fontSize: '14px', color: '#4b5563' }}>{cita.hora}</td>
                  <td style={{ padding: '14px 16px' }}>
                    <span style={{
                      display: 'inline-block',
                      padding: '3px 10px',
                      borderRadius: '9999px',
                      fontSize: '12px',
                      fontWeight: 600,
                      backgroundColor: `${colorEstado[cita.estado]}20`,
                      color: colorEstado[cita.estado],
                    }}>
                      {cita.estado.charAt(0).toUpperCase() + cita.estado.slice(1)}
                    </span>
                  </td>
                </tr>
              ))
            )}
          </tbody>
        </table>
      </div>

      <p style={{ marginTop: '16px', fontSize: '12px', color: '#9ca3af', textAlign: 'right' }}>
        Mostrando {citasFiltradas.length} de {CITAS.length} citas
      </p>
    </div>
  );
}
```

---

## Requisitos de la refactorización

### RQ-01: Aplicar Diseño Atómico

Divide el componente monolítico en la siguiente estructura:

```
src/
├── data/
│   └── citas.js                    ← Extraer CITAS y colorEstado aquí
├── components/
│   ├── atoms/
│   │   ├── StatusBadge/            ← Badge de estado de la cita
│   │   └── StatCard/               ← Tarjeta de estadística individual
│   ├── molecules/
│   │   ├── SearchBar/              ← Input de búsqueda
│   │   ├── FilterButtons/          ← Botones de filtro de estado
│   │   └── AppointmentRow/         ← Fila de la tabla
│   └── organisms/
│       ├── DashboardHeader/        ← Cabecera con título y usuario
│       ├── StatsGrid/              ← Cuadrícula de 4 StatCards
│       ├── FilterBar/              ← SearchBar + FilterButtons combinados
│       └── AppointmentsTable/      ← Tabla completa con filas
├── Dashboard.jsx                   ← Componente refactorizado (mínimo, solo orquesta)
└── App.jsx
```

### RQ-02: Comportamiento idéntico

El resultado visual y funcional debe ser **exactamente igual** al componente original. No cambies funcionalidades.

### RQ-03: Barrel exports

Crea archivos `index.js` en cada carpeta de componentes para exportar todos los componentes del nivel:

```js
// src/components/atoms/index.js
export { default as StatusBadge } from './StatusBadge/StatusBadge';
export { default as StatCard } from './StatCard/StatCard';
```

### RQ-04: Alias de rutas

Configura `vite.config.js` con alias para evitar rutas relativas largas:

```js
// El objetivo es poder importar así:
import { StatusBadge, StatCard } from '@atoms';
import { SearchBar, FilterButtons } from '@molecules';
```

### RQ-05: Dashboard refactorizado

El `Dashboard.jsx` final debe ser conciso y de alto nivel, delegando toda la presentación a los componentes hijos:

```jsx
// Dashboard.jsx DESPUÉS de refactorizar: debe ser similar a esto
import { useState } from 'react';
import { CITAS } from './data/citas';
import DashboardHeader from '@organisms/DashboardHeader/DashboardHeader';
import StatsGrid from '@organisms/StatsGrid/StatsGrid';
import FilterBar from '@organisms/FilterBar/FilterBar';
import AppointmentsTable from '@organisms/AppointmentsTable/AppointmentsTable';

export default function Dashboard() {
  const [filtroEstado, setFiltroEstado] = useState('todas');
  const [busqueda, setBusqueda] = useState('');

  const citasFiltradas = /* ... lógica de filtrado ... */;
  const stats = /* ... cálculo de estadísticas ... */;

  return (
    <div style={{ padding: '24px', maxWidth: '1100px', margin: '0 auto', fontFamily: 'sans-serif' }}>
      <DashboardHeader />
      <StatsGrid stats={stats} />
      <FilterBar
        busqueda={busqueda}
        onBusqueda={setBusqueda}
        filtroActivo={filtroEstado}
        onFiltro={setFiltroEstado}
      />
      <AppointmentsTable
        citas={citasFiltradas}
        totalCitas={CITAS.length}
      />
    </div>
  );
}
```

---

## Puntos de evaluación

| Criterio | Descripción | Puntos |
|----------|-------------|--------|
| **Estructura correcta** | Átomos, moléculas y organismos bien identificados y separados | 30 pts |
| **Comportamiento idéntico** | La app funciona igual que el original | 25 pts |
| **Barrel exports** | Cada nivel tiene su `index.js` correcto | 15 pts |
| **Alias de rutas** | `vite.config.js` configurado y usado en las importaciones | 15 pts |
| **Dashboard limpio** | `Dashboard.jsx` refactorizado es conciso y de alto nivel | 15 pts |

**Total: 100 puntos**

---

## Preguntas de reflexión (para la defensa oral o informe)

1. ¿Qué criterio usaste para decidir si un elemento era un átomo o una molécula?
2. ¿Tiene sentido extraer `AppointmentRow` como una molécula separada, o podría vivir dentro de `AppointmentsTable`? ¿Por qué?
3. Si el equipo de Diseño cambia el estilo del `StatusBadge`, ¿cuántos archivos necesitas modificar en tu nueva arquitectura? ¿Cuántos habrías necesitado en el código original?
4. ¿Qué componentes de este Dashboard podrían reutilizarse en otras pantallas de MediApp (ej. pantalla de doctores, historial del paciente)?

---

## Desafío adicional (opcional, +10 puntos)

Agrega **tests unitarios** para al menos dos componentes usando **Vitest** + **React Testing Library**:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

```jsx
// Ejemplo de test para StatusBadge
import { render, screen } from '@testing-library/react';
import StatusBadge from '@atoms/StatusBadge/StatusBadge';

describe('StatusBadge', () => {
  it('muestra el texto del estado', () => {
    render(<StatusBadge estado="confirmada" />);
    expect(screen.getByText('Confirmada')).toBeInTheDocument();
  });

  it('aplica el color correcto para estado cancelada', () => {
    render(<StatusBadge estado="cancelada" />);
    const badge = screen.getByText('Cancelada');
    expect(badge).toHaveStyle({ color: '#ef4444' });
  });
});
```

---

*← [Práctica 2](./ejercicio-2.md) | [Volver al índice del Módulo 2](../../README.md)*
