# Ejercicio de Práctica 2: Formulario de Registro con React 19 Actions

**Nivel:** Intermedio-Avanzado  
**Tiempo estimado:** 70-90 minutos  
**Conceptos:** React 19 `useActionState`, `useFormStatus`, validación de formularios, manejo de errores, feedback al usuario

---

## Contexto empresarial

**FinTech Startup** necesita implementar un formulario de registro de nuevos usuarios para su plataforma de gestión financiera. El formulario debe sentirse rápido y profesional, con validación robusta y feedback claro al usuario.

---

## Objetivo

Construir un formulario de registro multi-campo usando las nuevas APIs de React 19 para manejar el estado del formulario, la carga y los errores, sin necesidad de `useState` para cada campo de estado.

---

## Requisitos Funcionales

### RF-01: Campos del formulario

El formulario debe incluir los siguientes campos:

| Campo | Tipo | Validaciones |
|-------|------|--------------|
| Nombre completo | texto | Requerido, mínimo 3 caracteres |
| Email | email | Requerido, formato válido |
| Contraseña | password | Requerido, mínimo 8 caracteres, al menos 1 número y 1 mayúscula |
| Confirmar contraseña | password | Debe coincidir con contraseña |
| País | select | Requerido (opciones: Costa Rica, México, España, Colombia, Argentina) |
| Acepto términos | checkbox | Requerido |

### RF-02: Validación

- [ ] Validar el formulario **antes** de enviarlo al servidor.
- [ ] Mostrar mensajes de error específicos por campo.
- [ ] Si hay múltiples errores, mostrar todos de una vez (no uno por uno).

### RF-03: Estado del botón de submit

- [ ] El botón debe mostrar "Registrarse" en estado normal.
- [ ] El botón debe mostrar "Procesando..." y estar deshabilitado mientras se envía.
- [ ] Usar `useFormStatus` para implementar el botón de submit como componente separado.

### RF-04: Respuesta del servidor (simulada)

Simula una llamada al servidor con un delay de 1 segundo. El servidor debe:
- Rechazar emails que terminen en `@test.com` (email ya en uso).
- Aprobar todos los demás registros.

### RF-05: Confirmación de éxito

- [ ] Si el registro es exitoso, mostrar un mensaje de confirmación con el nombre del usuario.
- [ ] Ocultar el formulario y mostrar la pantalla de bienvenida.

---

## Servicio de API simulado

Crea el archivo `src/services/auth.js`:

```js
// src/services/auth.js

const delay = (ms) => new Promise(resolve => setTimeout(resolve, ms));

export async function registrarUsuario(datos) {
  await delay(1000); // Simula latencia del servidor

  // Email ya registrado
  if (datos.email.endsWith('@test.com')) {
    const error = new Error('Este email ya está registrado.');
    error.campo = 'email'; // El servidor indica qué campo falló
    throw error;
  }

  // Registro exitoso
  return {
    id: Math.random().toString(36).substring(2, 9),
    nombre: datos.nombre,
    email: datos.email,
    creadoEn: new Date().toISOString(),
  };
}
```

---

## Función de validación

Crea el archivo `src/utils/validaciones.js`:

```js
// src/utils/validaciones.js

export function validarFormularioRegistro(datos) {
  const errores = {};

  // Nombre
  if (!datos.nombre || datos.nombre.trim().length < 3) {
    errores.nombre = 'El nombre debe tener al menos 3 caracteres.';
  }

  // Email
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  if (!datos.email || !emailRegex.test(datos.email)) {
    errores.email = 'Introduce un email válido.';
  }

  // Contraseña
  const passwordRegex = /^(?=.*[A-Z])(?=.*\d).{8,}$/;
  if (!datos.password || !passwordRegex.test(datos.password)) {
    errores.password = 'La contraseña debe tener 8+ caracteres, una mayúscula y un número.';
  }

  // Confirmar contraseña
  if (datos.password !== datos.confirmPassword) {
    errores.confirmPassword = 'Las contraseñas no coinciden.';
  }

  // País
  if (!datos.pais) {
    errores.pais = 'Selecciona tu país.';
  }

  // Términos
  if (!datos.terminos) {
    errores.terminos = 'Debes aceptar los términos y condiciones.';
  }

  return errores; // Objeto vacío = sin errores
}
```

---

## Arquitectura esperada

```
src/
├── services/
│   └── auth.js
├── utils/
│   └── validaciones.js
├── components/
│   ├── atoms/
│   │   ├── FormField/      ← Label + Input + Mensaje de error
│   │   └── SubmitButton/   ← Botón que usa useFormStatus
│   └── molecules/
│       └── RegistroForm/   ← Formulario completo con useActionState
└── App.jsx
```

---

## Comportamiento del componente SubmitButton

El componente `SubmitButton` debe ser hijo del `<form>` y usar `useFormStatus` para saber si está en modo pendiente:

```jsx
// Pista de implementación
import { useFormStatus } from 'react-dom';

function SubmitButton() {
  const { pending } = useFormStatus();

  return (
    <button type="submit" disabled={pending}>
      {pending ? 'Procesando...' : 'Registrarse'}
    </button>
  );
}
```

---

## Pantalla de éxito esperada

Cuando el registro sea exitoso, muestra algo similar a:

```
✅ ¡Bienvenido, [Nombre del usuario]!

Tu cuenta ha sido creada exitosamente.
ID de usuario: [id generado]

[Ir al Dashboard →]
```

---

## Puntos de evaluación

| Criterio | Descripción | Puntos |
|----------|-------------|--------|
| **useActionState correcto** | La acción maneja submit, validación y llamada al servidor | 30 pts |
| **useFormStatus en SubmitButton** | El botón se deshabilita durante el pending | 15 pts |
| **Validación completa** | Todos los campos validados con mensajes correctos | 25 pts |
| **Manejo del error del servidor** | Error de email duplicado mostrado correctamente | 15 pts |
| **Pantalla de éxito** | Se muestra la confirmación tras registro exitoso | 15 pts |

**Total: 100 puntos**

---

## Pistas

<details>
<summary>💡 Pista 1: Estructura de la acción con useActionState</summary>

```jsx
const [estado, dispatch, isPending] = useActionState(
  async (estadoAnterior, formData) => {
    // 1. Extraer datos del formData
    const datos = {
      nombre: formData.get('nombre'),
      email: formData.get('email'),
      // ...
    };

    // 2. Validar
    const errores = validarFormularioRegistro(datos);
    if (Object.keys(errores).length > 0) {
      return { tipo: 'ERRORES_VALIDACION', errores };
    }

    // 3. Llamar al servidor
    try {
      const usuario = await registrarUsuario(datos);
      return { tipo: 'EXITO', usuario };
    } catch (err) {
      return { tipo: 'ERROR_SERVIDOR', mensaje: err.message, campo: err.campo };
    }
  },
  { tipo: null }
);
```
</details>

<details>
<summary>💡 Pista 2: Renderizar errores por campo</summary>

```jsx
// En el JSX, muestra el error del campo si existe en estado.errores
{estado.tipo === 'ERRORES_VALIDACION' && estado.errores?.email && (
  <p role="alert" style={{ color: 'red' }}>{estado.errores.email}</p>
)}
```
</details>

<details>
<summary>💡 Pista 3: Mostrar/ocultar el formulario según el estado</summary>

```jsx
if (estado.tipo === 'EXITO') {
  return <PantallaExito usuario={estado.usuario} />;
}
return <form action={dispatch}>...</form>;
```
</details>

---

## Desafío adicional (opcional)

Implementa un **indicador de fortaleza de contraseña** que se actualice en tiempo real mientras el usuario escribe:

- **Débil**: menos de 6 caracteres (rojo)
- **Media**: 6-8 caracteres o falta número o mayúscula (naranja)
- **Fuerte**: 8+ caracteres, con número y mayúscula (verde)

```jsx
// Pista: usa un useState separado solo para este campo, ya que es UI local
const [password, setPassword] = useState('');
const fortaleza = calcularFortalezaPassword(password); // 'débil' | 'media' | 'fuerte'
```

---

*← [Práctica 1](./ejercicio-1.md) | [Volver al índice](../../README.md) | Siguiente: [Práctica 3 →](./ejercicio-3.md)*
