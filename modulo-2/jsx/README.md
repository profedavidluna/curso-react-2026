# JSX en Profundidad

**JSX** (JavaScript XML) es una extensión de sintaxis de JavaScript que permite escribir estructuras similares a HTML dentro de archivos `.js` o `.jsx`. No es HTML ni una cadena de texto: es azúcar sintáctico que se transforma en llamadas a funciones de React.

---

## 1. Sintaxis y Reglas Básicas

### JSX no es HTML

Aunque JSX se parece a HTML, existen diferencias importantes:

| HTML | JSX |
|------|-----|
| `class="..."` | `className="..."` |
| `for="..."` | `htmlFor="..."` |
| `<img>` (sin cerrar) | `<img />` (debe cerrarse) |
| `onclick="fn()"` | `onClick={fn}` |
| `style="color: red"` | `style={{ color: 'red' }}` |
| Comentarios HTML: `<!-- -->` | Comentarios JSX: `{/* */}` |
| Atributos en minúsculas | Atributos en camelCase |

### Regla 1: Un solo elemento raíz

Un componente solo puede retornar **un elemento raíz**. Si necesitas retornar múltiples elementos, envuélvelos en un contenedor o usa Fragmentos.

```jsx
// ❌ Error: dos elementos raíz
function Tarjeta() {
  return (
    <h1>Título</h1>
    <p>Descripción</p>
  );
}

// ✅ Correcto: un solo elemento raíz
function Tarjeta() {
  return (
    <div>
      <h1>Título</h1>
      <p>Descripción</p>
    </div>
  );
}

// ✅ Correcto: usando Fragment
function Tarjeta() {
  return (
    <>
      <h1>Título</h1>
      <p>Descripción</p>
    </>
  );
}
```

### Regla 2: Cerrar todas las etiquetas

En JSX, **todas** las etiquetas deben cerrarse, incluyendo las que en HTML son auto-contenidas.

```jsx
// ❌ Error en JSX (aunque válido en HTML)
<img src="foto.jpg">
<input type="text">
<br>

// ✅ Correcto en JSX
<img src="foto.jpg" />
<input type="text" />
<br />
```

### Regla 3: camelCase para atributos

```jsx
// ❌ Estilo HTML
<div class="contenedor" onclick="handleClick()">
  <label for="nombre">Nombre:</label>
  <input tabindex="1" maxlength="50" />
</div>

// ✅ Estilo JSX
<div className="contenedor" onClick={handleClick}>
  <label htmlFor="nombre">Nombre:</label>
  <input tabIndex={1} maxLength={50} />
</div>
```

### Regla 4: Los atributos de estilo usan objetos

```jsx
// ❌ Estilo HTML: string
<div style="color: red; font-size: 16px;">

// ✅ Estilo JSX: objeto JavaScript
<div style={{ color: 'red', fontSize: '16px' }}>
// Las llaves externas son para "modo JS" y las internas son el objeto

// Para estilos dinámicos
const estilosDinamicos = {
  backgroundColor: estaActivo ? '#0070f3' : '#eee',
  color: estaActivo ? 'white' : 'black',
};
<button style={estilosDinamicos}>Botón</button>
```

---

## 2. Expresiones en JSX

### Modo JavaScript con `{}`

Las llaves `{}` activan el "modo JavaScript" dentro de JSX, permitiendo insertar cualquier expresión válida de JavaScript.

```jsx
const nombre = 'Carlos';
const precio = 29.99;
const esVip = true;

function Producto() {
  return (
    <div>
      {/* Variables */}
      <h1>{nombre}</h1>

      {/* Operaciones */}
      <p>Precio: ${(precio * 1.13).toFixed(2)} (con IVA)</p>

      {/* Expresiones ternarias */}
      <span>{esVip ? '⭐ Cliente VIP' : 'Cliente estándar'}</span>

      {/* Llamadas a funciones */}
      <p>{new Date().toLocaleDateString('es-ES')}</p>

      {/* Métodos de string/array */}
      <p>{nombre.toUpperCase()}</p>
    </div>
  );
}
```

### Renderizado condicional

Hay varias formas de renderizar contenido condicionalmente:

```jsx
function EstadoPedido({ estado, usuario }) {
  // Método 1: if/else fuera del JSX
  let mensajeEstado;
  if (estado === 'enviado') {
    mensajeEstado = <p className="verde">Pedido en camino ✈️</p>;
  } else if (estado === 'entregado') {
    mensajeEstado = <p className="azul">Pedido entregado ✅</p>;
  } else {
    mensajeEstado = <p className="naranja">Procesando pedido ⏳</p>;
  }

  return (
    <div>
      {/* Método 2: Operador ternario (para casos simples) */}
      {usuario.esPremium ? <BannerPremium /> : null}

      {/* Método 3: Cortocircuito && (muestra solo si es truthy) */}
      {estado === 'error' && <AlertaError mensaje="Hubo un problema con tu pedido" />}

      {/* Método 4: Nullish coalescing para valores por defecto */}
      <p>{usuario.apodo ?? usuario.nombre}</p>

      {mensajeEstado}
    </div>
  );
}
```

> **⚠️ Trampa común:** El valor `0` en JavaScript es falsy, así que `{conteo && <Componente />}` renderizará `0` si `conteo` es 0, no nada. Usa siempre `{conteo > 0 && <Componente />}` o `{!!conteo && <Componente />}`.

```jsx
// ❌ Puede renderizar "0" no deseado
{items.length && <Lista items={items} />}

// ✅ Correcto
{items.length > 0 && <Lista items={items} />}
```

### Renderizado de arrays

Puedes insertar arrays de elementos JSX directamente:

```jsx
const colores = ['rojo', 'verde', 'azul'];

function PaletaColores() {
  return (
    <ul>
      {colores.map(color => (
        <li key={color} style={{ color }}>{color}</li>
      ))}
    </ul>
  );
}
```

---

## 3. Fragmentos

Los **Fragmentos** permiten agrupar elementos sin añadir nodos extra al DOM.

### Sintaxis corta (`<>...</>`)

```jsx
function FilaTabla({ dato }) {
  // ✅ Fragment no añade un <div> extra que rompería el <table>
  return (
    <>
      <td>{dato.nombre}</td>
      <td>{dato.precio}</td>
      <td>{dato.stock}</td>
    </>
  );
}

// Si usaras <div> aquí, el HTML resultante sería inválido:
// <tr><div><td>...</td></div></tr>  ← inválido en HTML
```

### Sintaxis explícita con `key` (`<Fragment key>`)

Cuando necesitas pasar una `key` al Fragment (en listas), debes usar la sintaxis explícita:

```jsx
import { Fragment } from 'react';

function ListaDefiniciones({ terminos }) {
  return (
    <dl>
      {terminos.map(termino => (
        // ✅ Fragment con key: necesario cuando el Fragment está en una lista
        <Fragment key={termino.id}>
          <dt>{termino.palabra}</dt>
          <dd>{termino.definicion}</dd>
        </Fragment>
      ))}
    </dl>
  );
}
```

---

## 4. Listas y Keys

### La importancia de las keys

Las `key` son identificadores únicos que React usa durante el proceso de **reconciliación** para determinar qué elementos han cambiado, se han añadido o se han eliminado.

```jsx
// ❌ Sin keys: React no puede optimizar el diff
{productos.map(p => <TarjetaProducto {...p} />)}

// ⚠️ Con índice como key: funciona, pero no es ideal
{productos.map((p, i) => <TarjetaProducto key={i} {...p} />)}

// ✅ Con ID estable como key: lo correcto
{productos.map(p => <TarjetaProducto key={p.id} {...p} />)}
```

### ¿Por qué el índice como key es problemático?

```jsx
// Supón que tienes esta lista:
// key=0: "Manzana"
// key=1: "Banana"
// key=2: "Cereza"

// Si eliminas "Manzana", los índices cambian:
// key=0: "Banana"  ← React cree que cambió el key=0, no que se eliminó
// key=1: "Cereza"  ← React cree que cambió el key=1

// Resultado: React re-renderiza TODOS los elementos
// Con IDs estables, React sabría exactamente cuál se eliminó
```

### Buenas prácticas con keys

```jsx
// ✅ IDs de base de datos
{usuarios.map(u => <FilaUsuario key={u.id} usuario={u} />)}

// ✅ Slugs o valores únicos estables
{categorias.map(c => <Categoria key={c.slug} categoria={c} />)}

// ✅ Combinación de campos si no hay ID único
{pedidos.map(p => (
  <FilaPedido key={`${p.fecha}-${p.numero}`} pedido={p} />
))}

// ⚠️ Índice: aceptable SOLO si la lista es estática (nunca se reordena/filtra)
{pasosTutorial.map((paso, i) => <Paso key={i} texto={paso} />)}
```

### Las keys deben ser únicas entre hermanos

```jsx
// ❌ Keys duplicadas en el mismo nivel
<ul>
  <li key="1">Elemento 1</li>
  <li key="1">Elemento 2</li>  {/* ← Conflicto */}
</ul>

// ✅ Las keys deben ser únicas solo entre hermanos, no globalmente
<ul>
  {frutas.map(f => <li key={f.id}>{f.nombre}</li>)}  {/* key="1", "2", "3"... */}
</ul>
<ul>
  {verduras.map(v => <li key={v.id}>{v.nombre}</li>)}  {/* key="1", "2"... OK, son listas distintas */}
</ul>
```

---

## 5. Mejoras de JSX en React 19

### Ref como prop (sin forwardRef)

En React 19, puedes pasar `ref` como una prop normal, sin necesidad de `forwardRef`:

```jsx
// Antes de React 19: necesitabas forwardRef
import { forwardRef } from 'react';

const Input = forwardRef(function Input({ placeholder }, ref) {
  return <input ref={ref} placeholder={placeholder} />;
});
```

```jsx
// React 19: ref como prop directa
function Input({ placeholder, ref }) {
  return <input ref={ref} placeholder={placeholder} />;
}

// Uso
function Formulario() {
  const inputRef = useRef(null);

  return (
    <div>
      <Input ref={inputRef} placeholder="Escribe aquí" />
      <button onClick={() => inputRef.current.focus()}>
        Enfocar input
      </button>
    </div>
  );
}
```

### Cleanup de refs

React 19 soporta funciones de cleanup en el callback de `ref`, similar al cleanup de `useEffect`:

```jsx
function ComponenteConRef() {
  return (
    <div
      ref={(nodo) => {
        // Función de setup: se ejecuta cuando el elemento se monta
        if (nodo) {
          const controlador = inicializarPlugin(nodo);

          // Función de cleanup: se ejecuta cuando el elemento se desmonta
          return () => {
            controlador.destruir();
          };
        }
      }}
    />
  );
}
```

### Context como componente (sin `.Provider`)

```jsx
// Antes: necesitabas <MiContexto.Provider>
const TemaContexto = createContext('claro');

function App() {
  return (
    <TemaContexto.Provider value="oscuro">
      <Pagina />
    </TemaContexto.Provider>
  );
}

// React 19: el contexto mismo actúa como provider
function App() {
  return (
    <TemaContexto value="oscuro">
      <Pagina />
    </TemaContexto>
  );
}
```

---

## 6. Cómo se Transpila JSX a JavaScript

JSX no es entendido directamente por los navegadores. Herramientas como **Babel** o **el compilador de Vite** lo transforman en llamadas a funciones de JavaScript.

### JSX → JavaScript (transformación manual)

```jsx
// JSX que escribes tú:
const elemento = (
  <div className="tarjeta">
    <h1>Producto</h1>
    <p>Precio: $29.99</p>
  </div>
);
```

```js
// JavaScript que genera el compilador (React 17+ transform):
import { jsx as _jsx, jsxs as _jsxs } from 'react/jsx-runtime';

const elemento = _jsxs('div', {
  className: 'tarjeta',
  children: [
    _jsx('h1', { children: 'Producto' }),
    _jsx('p', { children: 'Precio: $29.99' })
  ]
});
```

> Con la **antigua transformación** (React <17), se usaba `React.createElement` y era necesario importar React en todos los archivos JSX. La nueva transformación (desde React 17) elimina esa necesidad.

### Ejemplo más complejo con props

```jsx
// JSX
function Boton({ texto, onClick, deshabilitado = false }) {
  return (
    <button
      onClick={onClick}
      disabled={deshabilitado}
      className={`btn ${deshabilitado ? 'btn-disabled' : 'btn-primary'}`}
    >
      {texto}
    </button>
  );
}
```

```js
// JavaScript compilado
function Boton({ texto, onClick, deshabilitado = false }) {
  return _jsx('button', {
    onClick: onClick,
    disabled: deshabilitado,
    className: `btn ${deshabilitado ? 'btn-disabled' : 'btn-primary'}`,
    children: texto
  });
}
```

### Explorar la transpilación con Babel REPL

Puedes ver en tiempo real cómo se transpila tu JSX en: [babeljs.io/repl](https://babeljs.io/repl#?presets=react)

---

## Patrones Avanzados de JSX

### Spread de props

```jsx
// Pasar todas las props de un objeto a un componente
const propsBton = {
  type: 'submit',
  disabled: false,
  'aria-label': 'Enviar formulario',
};

// ✅ Útil para componentes que envuelven elementos HTML
<button {...propsBton}>Enviar</button>

// También funciona para pasar props de un padre a un hijo
function Wrapper({ children, ...props }) {
  return <div className="wrapper" {...props}>{children}</div>;
}
```

### Children como función (Render Props)

```jsx
// El patrón de "render props" pasa una función como children
function RatónTracker({ children }) {
  const [posicion, setPosicion] = useState({ x: 0, y: 0 });

  return (
    <div
      onMouseMove={e => setPosicion({ x: e.clientX, y: e.clientY })}
      style={{ height: '200px', border: '1px solid' }}
    >
      {children(posicion)}
    </div>
  );
}

// Uso
<RatónTracker>
  {({ x, y }) => <p>Ratón en: ({x}, {y})</p>}
</RatónTracker>
```

### Children polimórficos con `as` prop

```jsx
// Componente que puede renderizarse como cualquier tag HTML
function Texto({ as: Tag = 'p', children, className }) {
  return <Tag className={className}>{children}</Tag>;
}

// Uso flexible
<Texto>Párrafo normal</Texto>
<Texto as="h1" className="titulo">Título principal</Texto>
<Texto as="span" className="etiqueta">Etiqueta</Texto>
```

---

## Checklist de Buenas Prácticas en JSX

- [ ] Usar `key` con IDs estables en todas las listas (nunca el índice si la lista puede cambiar).
- [ ] Usar `Fragment` (`<>`) en lugar de `<div>` envoltorio innecesarios.
- [ ] Extraer lógica JSX compleja a variables antes del `return`.
- [ ] Usar `className` y `htmlFor` en lugar de `class` y `for`.
- [ ] Cerrar todos los tags, incluidos los auto-contenidos (`<img />`, `<input />`).
- [ ] No usar `0` como condición para renderizado condicional (usar `> 0`).
- [ ] Mantener los componentes pequeños: si el JSX supera ~50 líneas, considera dividirlo.
- [ ] No anidar llamadas de renderizado inline más de 2 niveles.

---

## Recursos Adicionales

- [JSX en profundidad — Documentación oficial](https://react.dev/learn/writing-markup-with-jsx)
- [Babel REPL — Ver transpilación JSX en tiempo real](https://babeljs.io/repl#?presets=react)
- [JSX sin React — Preact, Solid.js y otros](https://preactjs.com/guide/v10/differences-to-react/)
- [React — Conditional Rendering](https://react.dev/learn/conditional-rendering)
- [React — Rendering Lists](https://react.dev/learn/rendering-lists)

---

*← [Novedades en React 19](../react-19/README.md) | [Volver al índice](../README.md) | Siguiente: [Diseño Atómico →](../diseno-atomico/README.md)*
