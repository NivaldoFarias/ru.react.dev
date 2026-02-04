<Intro>

`useState` — это React Hook, который позволяет добавить [переменную состояния](/learn/state-a-components-memory) в ваш компонент.

```js
const [state, setState] = useState(initialState)
```

</Intro>

<InlineToc />

---

## Справочник {/*reference*/}

### `useState(initialState)` {/*usestate*/}

Вызовите `useState` на верхнем уровне вашего компонента, чтобы объявить [переменную состояния.](/learn/state-a-components-memory)

```js
import { useState } from 'react';

function MyComponent() {
  const [age, setAge] = useState(28);
  const [name, setName] = useState('Taylor');
  const [todos, setTodos] = useState(() => createTodos());
  // ...
```

По соглашению, переменные состояния именуются как `[something, setSomething]` с использованием [деструктуризации массивов.](https://javascript.info/destructuring-assignment)

[См. больше примеров ниже.](#usage)

#### Параметры {/*parameters*/}

* `initialState`: Значение, которое должно быть у состояния изначально. Это может быть значение любого типа, но для функций есть особое поведение. Этот аргумент игнорируется после первого рендера.
  * Если вы передаёте функцию в качестве `initialState`, она будет трактоваться как _инициализирующая функция_. Она должна быть чистой, не принимать аргументов и возвращать значение любого типа. React вызовет вашу инициализирующую функцию при инициализации компонента и сохранит её возвращаемое значение как начальное состояние. [См. пример ниже.](#avoiding-recreating-the-initial-state)

#### Возвращаемое значение {/*returns*/}

`useState` возвращает массив из ровно двух значений:

1. Текущее состояние. Во время первого рендера оно будет соответствовать переданному вами `initialState`.
2. [`set` функция](#setstate), которая позволяет обновить состояние другим значением и вызвать повторный рендер.

#### Ограничения {/*caveats*/}

* `useState` — это Hook, поэтому вы можете вызывать его **только на верхнем уровне вашего компонента** или ваших собственных Hooks. Вы не можете вызывать его внутри циклов или условий. Если вам это нужно, вынесите новый компонент и переместите состояние в него.
* В Strict Mode React **дважды вызовет вашу инициализирующую функцию**, чтобы [помочь вам найти случайные примеси.](#my-initializer-or-updater-function-runs-twice) Это поведение только для разработки и не влияет на продакшен. Если ваша инициализирующая функция чистая (как и должно быть), это не должно повлиять на поведение. Результат одного из вызовов будет проигнорирован.

---

### `set` функции, такие как `setSomething(nextState)` {/*setstate*/}

`set` функция, возвращаемая `useState`, позволяет обновить состояние другим значением и вызвать повторный рендер. Вы можете передать следующее состояние напрямую или функцию, которая его вычисляет из предыдущего состояния:

```js
const [name, setName] = useState('Edward');

function handleClick() {
  setName('Taylor');
  setAge(a => a + 1);
  // ...
```

#### Параметры {/*setstate-parameters*/}

* `nextState`: Значение, которое вы хотите установить для состояния. Это может быть значение любого типа, но для функций есть особое поведение.
  * Если вы передаёте функцию в качестве `nextState`, она будет трактоваться как _обновляющая функция_. Она должна быть чистой, принимать ожидающее состояние как единственный аргумент и возвращать следующее состояние. React поместит вашу обновляющую функцию в очередь и повторно отрендерит ваш компонент. Во время следующего рендера React вычислит следующее состояние, применив все поставленные в очередь обновляющие функции к предыдущему состоянию. [См. пример ниже.](#updating-state-based-on-the-previous-state)

#### Возвращаемое значение {/*setstate-returns*/}

`set` функции не имеют возвращаемого значения.

#### Ограничения {/*setstate-caveats*/}

* `set` функция **обновляет переменную состояния только для *следующего* рендера**. Если вы прочитаете переменную состояния после вызова `set` функции, [вы всё равно получите старое значение](#ive-updated-the-state-but-logging-gives-me-the-old-value), которое было на экране до вашего вызова.

* Если новое значение, которое вы предоставляете, идентично текущему `state`, как определено сравнением [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is), React **пропустит повторный рендер компонента и его дочерних элементов.** Это оптимизация. Хотя в некоторых случаях React может всё же вызвать ваш компонент перед пропуском дочерних элементов, это не должно повлиять на ваш код.

* React [группирует обновления состояния.](/learn/queueing-a-series-of-state-updates) Он обновляет экран **после того, как все обработчики событий отработали** и вызвали свои `set` функции. Это предотвращает множественные повторные рендеры во время одного события. В редких случаях, когда вам нужно принудительно обновить экран React раньше, например, для доступа к DOM, вы можете использовать [`flushSync`.](/reference/react-dom/flushSync)

* `set` функция имеет стабильную идентичность, поэтому вы часто увидите, что она опущена из зависимостей Effect, но её включение не вызовет срабатывание Effect. Если линтер позволяет вам опустить зависимость без ошибок, это безопасно. [Узнайте больше об удалении зависимостей Effect.](/learn/removing-effect-dependencies#move-dynamic-objects-and-functions-inside-your-effect)

* Вызов `set` функции *во время рендеринга* разрешён только изнутри текущего рендерящегося компонента. React отбросит его вывод и немедленно попытается отрендерить его снова с новым состоянием. Этот шаблон редко нужен, но вы можете использовать его для **хранения информации из предыдущих рендеров**. [См. пример ниже.](#storing-information-from-previous-renders)

* В Strict Mode React **дважды вызовет вашу обновляющую функцию**, чтобы [помочь вам найти случайные примеси.](#my-initializer-or-updater-function-runs-twice) Это поведение только для разработки и не влияет на продакшен. Если ваша обновляющая функция чистая (как и должно быть), это не должно повлиять на поведение. Результат одного из вызовов будет проигнорирован.

---

## Использование {/*usage*/}

### Добавление состояния в компонент {/*adding-state-to-a-component*/}

Вызовите `useState` на верхнем уровне вашего компонента, чтобы объявить одну или несколько [переменных состояния.](/learn/state-a-components-memory)

```js [[1, 4, "age"], [2, 4, "setAge"], [3, 4, "42"], [1, 5, "name"], [2, 5, "setName"], [3, 5, "'Taylor'"]]
import { useState } from 'react';

function MyComponent() {
  const [age, setAge] = useState(42);
  const [name, setName] = useState('Taylor');
  // ...
```

По соглашению, переменные состояния именуются как `[something, setSomething]` с использованием [деструктуризации массива.](https://javascript.info/destructuring-assignment)

`useState` возвращает массив, содержащий ровно два элемента:

1. <CodeStep step={1}>Текущее состояние</CodeStep> этой переменной состояния, изначально установленное в <CodeStep step={3}>начальное состояние</CodeStep>, которое вы предоставили.
2. <CodeStep step={2}>Функция `set`</CodeStep>, которая позволяет вам изменить его на любое другое значение в ответ на взаимодействие.

Чтобы обновить то, что отображается на экране, вызовите функцию `set` с новым состоянием:

```js [[2, 2, "setName"]]
function handleClick() {
  setName('Robin');
}
```

React сохранит новое состояние, снова отрисует ваш компонент с новыми значениями и обновит пользовательский интерфейс.

<Pitfall>

Вызов функции `set` [**не** изменяет текущее состояние в уже выполняющемся коде](#ive-updated-the-state-but-logging-gives-me-the-old-value):

```js {3}
function handleClick() {
  setName('Robin');
  console.log(name); // Всё ещё "Taylor"!
}
```

Он влияет только на то, что `useState` вернёт начиная со *следующего* рендера.

</Pitfall>

<Recipes titleText="Базовые примеры useState" titleId="examples-basic">

#### Счетчик (число) {/*counter-number*/}

В этом примере переменная состояния `count` хранит число. Нажатие на кнопку увеличивает его.

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <button onClick={handleClick}>
      You pressed me {count} times
    </button>
  );
}
```

</Sandpack>

<Solution />

#### Поле ввода текста (строка) {/*text-field-string*/}

В этом примере переменная состояния `text` хранит строку. Когда вы печатаете, `handleChange` считывает последнее введенное значение из DOM-элемента ввода браузера и вызывает `setText` для обновления состояния. Это позволяет отображать текущий `text` ниже.

<Sandpack>

```js
import { useState } from 'react';

export default function MyInput() {
  const [text, setText] = useState('hello');

  function handleChange(e) {
    setText(e.target.value);
  }

  return (
    <>
      <input value={text} onChange={handleChange} />
      <p>You typed: {text}</p>
      <button onClick={() => setText('hello')}>
        Reset
      </button>
    </>
  );
}
```

</Sandpack>

<Solution />

#### Флажок (булево значение) {/*checkbox-boolean*/}

В этом примере переменная состояния `liked` хранит булево значение. Когда вы нажимаете на поле ввода, `setLiked` обновляет переменную состояния `liked` в зависимости от того, установлен ли флажок браузера. Переменная `liked` используется для отображения текста под флажком.

<Sandpack>

```js
import { useState } from 'react';

export default function MyCheckbox() {
  const [liked, setLiked] = useState(true);

  function handleChange(e) {
    setLiked(e.target.checked);
  }

  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={liked}
          onChange={handleChange}
        />
        I liked this
      </label>
      <p>You {liked ? 'liked' : 'did not like'} this.</p>
    </>
  );
}
```

</Sandpack>

<Solution />

#### Форма (две переменные) {/*form-two-variables*/}

Вы можете объявить несколько переменных состояния в одном компоненте. Каждая переменная состояния полностью независима.

<Sandpack>

```js
import { useState } from 'react';

export default function Form() {
  const [name, setName] = useState('Taylor');
  const [age, setAge] = useState(42);

  return (
    <>
      <input
        value={name}
        onChange={e => setName(e.target.value)}
      />
      <button onClick={() => setAge(age + 1)}>
        Increment age
      </button>
      <p>Hello, {name}. You are {age}.</p>
    </>
  );
}
```

```css
button { display: block; margin-top: 10px; }
```

</Sandpack>

<Solution />

</Recipes>

---

### Обновление состояния на основе предыдущего состояния {/*updating-state-based-on-the-previous-state*/}

Предположим, `age` равно `42`. Этот обработчик вызывает `setAge(age + 1)` три раза:

```js
function handleClick() {
  setAge(age + 1); // setAge(42 + 1)
  setAge(age + 1); // setAge(42 + 1)
  setAge(age + 1); // setAge(42 + 1)
}
```

Однако после одного клика `age` будет равен `43`, а не `45`! Это происходит потому, что вызов функции `set` [не обновляет](/learn/state-as-a-snapshot) переменную состояния `age` в уже выполняющемся коде. Таким образом, каждый вызов `setAge(age + 1)` становится `setAge(43)`.

Чтобы решить эту проблему, **вы можете передать *функцию-обработчик* в `setAge` вместо следующего состояния**:

```js [[1, 2, "a", 0], [2, 2, "a + 1"], [1, 3, "a", 0], [2, 3, "a + 1"], [1, 4, "a", 0], [2, 4, "a + 1"]]
function handleClick() {
  setAge(a => a + 1); // setAge(42 => 43)
  setAge(a => a + 1); // setAge(43 => 44)
  setAge(a => a + 1); // setAge(44 => 45)
}
```

Здесь `a => a + 1` — это ваша функция-обработчик. Она принимает <CodeStep step={1}>ожидающее состояние</CodeStep> и вычисляет <CodeStep step={2}>следующее состояние</CodeStep> на его основе.

React помещает ваши функции-обработчики в [очередь.](/learn/queueing-a-series-of-state-updates) Затем, во время следующего рендера, он вызовет их в том же порядке:

1. `a => a + 1` получит `42` как ожидающее состояние и вернёт `43` как следующее состояние.
1. `a => a + 1` получит `43` как ожидающее состояние и вернёт `44` как следующее состояние.
1. `a => a + 1` получит `44` как ожидающее состояние и вернёт `45` как следующее состояние.

Других ожидающих обновлений нет, поэтому React в итоге сохранит `45` как текущее состояние.

По соглашению, принято называть аргумент ожидающего состояния по первой букве имени переменной состояния, например `a` для `age`. Однако вы можете назвать его и `prevAge` или чем-то другим, что покажется вам более понятным.

React может [вызвать ваши обработчики дважды](#my-initializer-or-updater-function-runs-twice) в режиме разработки, чтобы убедиться, что они [чистые.](/learn/keeping-components-pure)

<DeepDive>

#### Предпочтительно ли всегда использовать обработчик? {/*is-using-an-updater-always-preferred*/}

Вы можете услышать рекомендацию всегда писать код вида `setAge(a => a + 1)`, если устанавливаемое состояние вычисляется из предыдущего состояния. В этом нет ничего плохого, но это и не всегда необходимо.

В большинстве случаев разницы между этими двумя подходами нет. React всегда гарантирует, что для преднамеренных действий пользователя, таких как клики, переменная состояния `age` будет обновлена до следующего клика. Это означает, что нет риска, что обработчик клика увидит "устаревшее" значение `age` в начале обработчика события.

Однако, если вы выполняете несколько обновлений в рамках одного события, обработчики могут быть полезны. Они также полезны, если доступ к самой переменной состояния неудобен (вы можете столкнуться с этим при оптимизации повторных рендеров).

Если вы предпочитаете последовательность вместо немного более многословного синтаксиса, разумно всегда писать обработчик, если устанавливаемое состояние вычисляется из предыдущего состояния. Если оно вычисляется из предыдущего состояния какой-либо *другой* переменной состояния, вы можете объединить их в один объект и [использовать редьюсер.](/learn/extracting-state-logic-into-a-reducer)

</DeepDive>

<Recipes titleText="Разница между передачей обработчика и передачей следующего состояния напрямую" titleId="examples-updater">

#### Передача функции-обработчика {/*passing-the-updater-function*/}

Этот пример передает функцию-обработчик, поэтому кнопка "+3" работает.

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [age, setAge] = useState(42);

  function increment() {
    setAge(a => a + 1);
  }

  return (
    <>
      <h1>Your age: {age}</h1>
      <button onClick={() => {
        increment();
        increment();
        increment();
      }}>+3</button>
      <button onClick={() => {
        increment();
      }}>+1</button>
    </>
  );
}
```

```css
button { display: block; margin: 10px; font-size: 20px; }
h1 { display: block; margin: 10px; }
```

</Sandpack>

<Solution />

#### Передача следующего состояния напрямую {/*passing-the-next-state-directly*/}

Этот пример **не** передает функцию-обработчик, поэтому кнопка "+3" **не работает должным образом**.

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [age, setAge] = useState(42);

  function increment() {
    setAge(age + 1);
  }

  return (
    <>
      <h1>Your age: {age}</h1>
      <button onClick={() => {
        increment();
        increment();
        increment();
      }}>+3</button>
      <button onClick={() => {
        increment();
      }}>+1</button>
    </>
  );
}
```

```css
button { display: block; margin: 10px; font-size: 20px; }
h1 { display: block; margin: 10px; }
```

</Sandpack>

<Solution />

</Recipes>

---

### Обновление объектов и массивов в состоянии {/*updating-objects-and-arrays-in-state*/}

Вы можете помещать объекты и массивы в состояние. В React состояние считается неизменяемым, поэтому **вам следует *заменять* его, а не *изменять* существующие объекты**. Например, если у вас есть объект `form` в состоянии, не изменяйте его:

```js
// 🚩 Не изменяйте объект в состоянии так:
form.firstName = 'Taylor';
```

Вместо этого замените весь объект, создав новый:

```js
// ✅ Замените состояние новым объектом
setForm({
  ...form,
  firstName: 'Taylor'
});
```

Прочтите [Обновление объектов в состоянии](/learn/updating-objects-in-state) и [Обновление массивов в состоянии](/learn/updating-arrays-in-state), чтобы узнать больше.

<Recipes titleText="Примеры объектов и массивов в состоянии" titleId="examples-objects">

#### Форма (объект) {/*form-object*/}

В этом примере переменная состояния `form` содержит объект. Каждый ввод имеет обработчик изменения, который вызывает `setForm` с новым состоянием всей формы. Синтаксис spread `{ ...form }` гарантирует, что объект состояния будет заменен, а не изменен.

<Sandpack>

```js
import { useState } from 'react';

export default function Form() {
  const [form, setForm] = useState({
    firstName: 'Barbara',
    lastName: 'Hepworth',
    email: 'bhepworth@sculpture.com',
  });

  return (
    <>
      <label>
        First name:
        <input
          value={form.firstName}
          onChange={e => {
            setForm({
              ...form,
              firstName: e.target.value
            });
          }}
        />
      </label>
      <label>
        Last name:
        <input
          value={form.lastName}
          onChange={e => {
            setForm({
              ...form,
              lastName: e.target.value
            });
          }}
        />
      </label>
      <label>
        Email:
        <input
          value={form.email}
          onChange={e => {
            setForm({
              ...form,
              email: e.target.value
            });
          }}
        />
      </label>
      <p>
        {form.firstName}{' '}
        {form.lastName}{' '}
        ({form.email})
      </p>
    </>
  );
}
```

```css
label { display: block; }
input { margin-left: 5px; }
```

</Sandpack>

<Solution />

#### Форма (вложенный объект) {/*form-nested-object*/}

В этом примере состояние более вложенное. При обновлении вложенного состояния вам нужно создать копию объекта, который вы обновляете, а также всех объектов, которые его "содержат" на пути вверх. Прочтите [Обновление вложенного объекта](/learn/updating-objects-in-state#updating-a-nested-object), чтобы узнать больше.

<Sandpack>

```js
import { useState } from 'react';

export default function Form() {
  const [person, setPerson] = useState({
    name: 'Niki de Saint Phalle',
    artwork: {
      title: 'Blue Nana',
      city: 'Hamburg',
      image: 'https://i.imgur.com/Sd1AgUOm.jpg',
    }
  });

  function handleNameChange(e) {
    setPerson({
      ...person,
      name: e.target.value
    });
  }

  function handleTitleChange(e) {
    setPerson({
      ...person,
      artwork: {
        ...person.artwork,
        title: e.target.value
      }
    });
  }

  function handleCityChange(e) {
    setPerson({
      ...person,
      artwork: {
        ...person.artwork,
        city: e.target.value
      }
    });
  }

  function handleImageChange(e) {
    setPerson({
      ...person,
      artwork: {
        ...person.artwork,
        image: e.target.value
      }
    });
  }

  return (
    <>
      <label>
        Name:
        <input
          value={person.name}
          onChange={handleNameChange}
        />
      </label>
      <label>
        Title:
        <input
          value={person.artwork.title}
          onChange={handleTitleChange}
        />
      </label>
      <label>
        City:
        <input
          value={person.artwork.city}
          onChange={handleCityChange}
        />
      </label>
      <label>
        Image:
        <input
          value={person.artwork.image}
          onChange={handleImageChange}
        />
      </label>
      <p>
        <i>{person.artwork.title}</i>
        {' by '}
        {person.name}
        <br />
        (located in {person.artwork.city})
      </p>
      <img
        src={person.artwork.image}
        alt={person.artwork.title}
      />
    </>
  );
}
```

```css
label { display: block; }
input { margin-left: 5px; margin-bottom: 5px; }
img { width: 200px; height: 200px; }
```

</Sandpack>

<Solution />

#### Список (массив) {/*list-array*/}

В этом примере переменная состояния `todos` содержит массив. Каждый обработчик кнопки вызывает `setTodos` с новой версией этого массива. Синтаксис spread `[...todos]`, `todos.map()` и `todos.filter()` гарантируют, что массив состояния будет заменен, а не изменен.

<Sandpack>

```js src/App.js
import { useState } from 'react';
import AddTodo from './AddTodo.js';
import TaskList from './TaskList.js';

let nextId = 3;
const initialTodos = [
  { id: 0, title: 'Buy milk', done: true },
  { id: 1, title: 'Eat tacos', done: false },
  { id: 2, title: 'Brew tea', done: false },
];

export default function TaskApp() {
  const [todos, setTodos] = useState(initialTodos);

  function handleAddTodo(title) {
    setTodos([
      ...todos,
      {
        id: nextId++,
        title: title,
        done: false
      }
    ]);
  }

  function handleChangeTodo(nextTodo) {
    setTodos(todos.map(t => {
      if (t.id === nextTodo.id) {
        return nextTodo;
      } else {
        return t;
      }
    }));
  }

  function handleDeleteTodo(todoId) {
    setTodos(
      todos.filter(t => t.id !== todoId)
    );
  }

  return (
    <>
      <AddTodo
        onAddTodo={handleAddTodo}
      />
      <TaskList
        todos={todos}
        onChangeTodo={handleChangeTodo}
        onDeleteTodo={handleDeleteTodo}
      />
    </>
  );
}
```

```js src/AddTodo.js
import { useState } from 'react';

export default function AddTodo({ onAddTodo }) {
  const [title, setTitle] = useState('');
  return (
    <>
      <input
        placeholder="Add todo"
        value={title}
        onChange={e => setTitle(e.target.value)}
      />
      <button onClick={() => {
        setTitle('');
        onAddTodo(title);
      }}>Add</button>
    </>
  )
}
```

```js src/TaskList.js
import { useState } from 'react';

export default function TaskList({
  todos,
  onChangeTodo,
  onDeleteTodo
}) {
  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>
          <Task
            todo={todo}
            onChange={onChangeTodo}
            onDelete={onDeleteTodo}
          />
        </li>
      ))}
    </ul>
  );
}

function Task({ todo, onChange, onDelete }) {
  const [isEditing, setIsEditing] = useState(false);
  let todoContent;
  if (isEditing) {
    todoContent = (
      <>
        <input
          value={todo.title}
          onChange={e => {
            onChange({
              ...todo,
              title: e.target.value
            });
          }} />
        <button onClick={() => setIsEditing(false)}>
          Save
        </button>
      </>
    );
  } else {
    todoContent = (
      <>
        {todo.title}
        <button onClick={() => setIsEditing(true)}>
          Edit
        </button>
      </>
    );
  }
  return (
    <label>
      <input
        type="checkbox"
        checked={todo.done}
        onChange={e => {
          onChange({
            ...todo,
            done: e.target.checked
          });
        }}
      />
      {todoContent}
      <button onClick={() => onDelete(todo.id)}>
        Delete
      </button>
    </label>
  );
}
```

```css
button { margin: 5px; }
li { list-style-type: none; }
ul, li { margin: 0; padding: 0; }
```

</Sandpack>

<Solution />

#### Написание лаконичной логики обновления с помощью Immer {/*writing-concise-update-logic-with-immer*/}

Если обновление массивов и объектов без мутаций кажется утомительным, вы можете использовать библиотеку, такую как [Immer](https://github.com/immerjs/use-immer), чтобы уменьшить повторяющийся код. Immer позволяет писать лаконичный код, как если бы вы изменяли объекты, но под капотом выполняет неизменяемые обновления:

<Sandpack>

```js
import { useState } from 'react';
import { useImmer } from 'use-immer';

let nextId = 3;
const initialList = [
  { id: 0, title: 'Big Bellies', seen: false },
  { id: 1, title: 'Lunar Landscape', seen: false },
  { id: 2, title: 'Terracotta Army', seen: true },
];

export default function BucketList() {
  const [list, updateList] = useImmer(initialList);

  function handleToggle(artworkId, nextSeen) {
    updateList(draft => {
      const artwork = draft.find(a =>
        a.id === artworkId
      );
      artwork.seen = nextSeen;
    });
  }

  return (
    <>
      <h1>Art Bucket List</h1>
      <h2>My list of art to see:</h2>
      <ItemList
        artworks={list}
        onToggle={handleToggle} />
    </>
  );
}

function ItemList({ artworks, onToggle }) {
  return (
    <ul>
      {artworks.map(artwork => (
        <li key={artwork.id}>
          <label>
            <input
              type="checkbox"
              checked={artwork.seen}
              onChange={e => {
                onToggle(
                  artwork.id,
                  e.target.checked
                );
              }}
            />
            {artwork.title}
          </label>
        </li>
      ))}
    </ul>
  );
}
```

```json package.json
{
  "dependencies": {
    "immer": "1.7.3",
    "react": "latest",
    "react-dom": "latest",
    "react-scripts": "latest",
    "use-immer": "0.5.1"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

</Sandpack>

<Solution />

</Recipes>

---

### Избегание повторного создания начального состояния {/*avoiding-recreating-the-initial-state*/}

React сохраняет начальное состояние один раз и игнорирует его при последующих рендерах.

```js
function TodoList() {
  const [todos, setTodos] = useState(createInitialTodos());
  // ...
```

Хотя результат `createInitialTodos()` используется только для начального рендера, вы все равно вызываете эту функцию при каждом рендере. Это может быть расточительно, если она создает большие массивы или выполняет дорогостоящие вычисления.

Чтобы решить эту проблему, вы можете передать ее в качестве _инициализатора_ функции в `useState` вместо этого:

```js
function TodoList() {
  const [todos, setTodos] = useState(createInitialTodos);
  // ...
```

Обратите внимание, что вы передаете `createInitialTodos`, то есть _саму функцию_, а не `createInitialTodos()`, то есть результат ее вызова. Если вы передаете функцию в `useState`, React вызовет ее только во время инициализации.

React может [вызывать ваши инициализаторы дважды](#my-initializer-or-updater-function-runs-twice) в режиме разработки, чтобы убедиться, что они являются [чистыми.](/learn/keeping-components-pure)

<Recipes titleText="Разница между передачей инициализатора и передачей начального состояния напрямую" titleId="examples-initializer">

#### Передача функции-инициализатора {/*passing-the-initializer-function*/}

В этом примере передается функция-инициализатор, поэтому функция `createInitialTodos` выполняется только во время инициализации. Она не выполняется при повторных рендерах компонента, например, когда вы печатаете в поле ввода.

<Sandpack>

```js
import { useState } from 'react';

function createInitialTodos() {
  const initialTodos = [];
  for (let i = 0; i < 50; i++) {
    initialTodos.push({
      id: i,
      text: 'Item ' + (i + 1)
    });
  }
  return initialTodos;
}

export default function TodoList() {
  const [todos, setTodos] = useState(createInitialTodos);
  const [text, setText] = useState('');

  return (
    <>
      <input
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button onClick={() => {
        setText('');
        setTodos([{
          id: todos.length,
          text: text
        }, ...todos]);
      }}>Add</button>
      <ul>
        {todos.map(item => (
          <li key={item.id}>
            {item.text}
          </li>
        ))}
      </ul>
    </>
  );
}
```

</Sandpack>

<Solution />

#### Передача начального состояния напрямую {/*passing-the-initial-state-directly*/}

В этом примере функция-инициализатор **не** передается, поэтому функция `createInitialTodos` выполняется при каждом рендере, например, когда вы печатаете в поле ввода. Наблюдаемой разницы в поведении нет, но этот код менее эффективен.

<Sandpack>

```js
import { useState } from 'react';

function createInitialTodos() {
  const initialTodos = [];
  for (let i = 0; i < 50; i++) {
    initialTodos.push({
      id: i,
      text: 'Item ' + (i + 1)
    });
  }
  return initialTodos;
}

export default function TodoList() {
  const [todos, setTodos] = useState(createInitialTodos());
  const [text, setText] = useState('');

  return (
    <>
      <input
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button onClick={() => {
        setText('');
        setTodos([{
          id: todos.length,
          text: text
        }, ...todos]);
      }}>Add</button>
      <ul>
        {todos.map(item => (
          <li key={item.id}>
            {item.text}
          </li>
        ))}
      </ul>
    </>
  );
}
```

</Sandpack>

<Solution />

</Recipes>

---

### Сброс состояния с помощью ключа {/*resetting-state-with-a-key*/}

Атрибут `key` часто встречается при [отрисовке списков](/learn/rendering-lists). Однако он имеет и другое назначение.

Вы можете **сбросить состояние компонента, передав ему другой `key`**. В этом примере кнопка Reset изменяет переменную состояния `version`, которую мы передаём как `key` компоненту `Form`. Когда `key` изменяется, React заново создаёт компонент `Form` (и все его дочерние элементы) с нуля, поэтому его состояние сбрасывается.

Прочтите [Сохранение и сброс состояния](/learn/preserving-and-resetting-state), чтобы узнать больше.

<Sandpack>

```js src/App.js
import { useState } from 'react';

export default function App() {
  const [version, setVersion] = useState(0);

  function handleReset() {
    setVersion(version + 1);
  }

  return (
    <>
      <button onClick={handleReset}>Reset</button>
      <Form key={version} />
    </>
  );
}

function Form() {
  const [name, setName] = useState('Taylor');

  return (
    <>
      <input
        value={name}
        onChange={e => setName(e.target.value)}
      />
      <p>Hello, {name}.</p>
    </>
  );
}
```

```css
button { display: block; margin-bottom: 20px; }
```

</Sandpack>

---

### Хранение информации из предыдущих рендеров {/*storing-information-from-previous-renders*/}

Обычно вы обновляете состояние в обработчиках событий. Однако в редких случаях может потребоваться скорректировать состояние в ответ на рендеринг — например, изменить переменную состояния при изменении пропса.

В большинстве случаев вам это не понадобится:

* **Если значение, которое вам нужно, можно вычислить полностью из текущих пропсов или другого состояния, [удалите это избыточное состояние.](/learn/choosing-the-state-structure#avoid-redundant-state)** Если вы беспокоитесь о слишком частых перерасчётах, вам может помочь [`useMemo` Hook](/reference/react/useMemo).
* Если вы хотите сбросить состояние всего дерева компонентов, [передайте другой `key` вашему компоненту.](#resetting-state-with-a-key)
* Если возможно, обновляйте всё соответствующее состояние в обработчиках событий.

В редких случаях, когда ни один из этих вариантов не подходит, существует шаблон, который можно использовать для обновления состояния на основе значений, отрендеренных до этого момента, путём вызова `set`-функции во время рендеринга компонента.

Вот пример. Компонент `CountLabel` отображает пропс `count`, переданный ему:

```js src/CountLabel.js
export default function CountLabel({ count }) {
  return <h1>{count}</h1>
}
```

Предположим, вы хотите показать, увеличился или уменьшился счётчик с момента последнего изменения. Пропс `count` сам по себе этого не сообщает — вам нужно отслеживать его предыдущее значение. Добавьте переменную состояния `prevCount` для его отслеживания. Добавьте ещё одну переменную состояния `trend`, чтобы хранить информацию об увеличении или уменьшении счётчика. Сравните `prevCount` с `count`, и если они не равны, обновите и `prevCount`, и `trend`. Теперь вы можете отображать как текущий пропс `count`, так и *то, как он изменился с момента последнего рендера*.

<Sandpack>

```js src/App.js
import { useState } from 'react';
import CountLabel from './CountLabel.js';

export default function App() {
  const [count, setCount] = useState(0);
  return (
    <>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
      <button onClick={() => setCount(count - 1)}>
        Decrement
      </button>
      <CountLabel count={count} />
    </>
  );
}
```

```js src/CountLabel.js active
import { useState } from 'react';

export default function CountLabel({ count }) {
  const [prevCount, setPrevCount] = useState(count);
  const [trend, setTrend] = useState(null);
  if (prevCount !== count) {
    setPrevCount(count);
    setTrend(count > prevCount ? 'increasing' : 'decreasing');
  }
  return (
    <>
      <h1>{count}</h1>
      {trend && <p>The count is {trend}</p>}
    </>
  );
}
```

```css
button { margin-bottom: 10px; }
```

</Sandpack>

Обратите внимание, что если вы вызываете `set`-функцию во время рендеринга, это должно происходить внутри условия, такого как `prevCount !== count`, и внутри этого условия должен быть вызов `setPrevCount(count)`. В противном случае ваш компонент будет бесконечно перезагружаться до сбоя. Кроме того, таким образом вы можете обновлять состояние только *текущего рендерящегося* компонента. Вызов `set`-функции *другого* компонента во время рендеринга является ошибкой. Наконец, ваш вызов `set` должен по-прежнему [обновлять состояние без мутации](#updating-objects-and-arrays-in-state) — это не означает, что вы можете нарушать другие правила [чистых функций.](/learn/keeping-components-pure)

Этот шаблон может быть сложным для понимания, и его обычно лучше избегать. Однако он лучше, чем обновление состояния в эффекте. Когда вы вызываете `set`-функцию во время рендеринга, React немедленно перезагрузит этот компонент после того, как ваш компонент завершит работу с оператором `return`, и до рендеринга дочерних элементов. Таким образом, дочерним элементам не нужно рендериться дважды. Остальная часть вашей функции компонента всё равно будет выполнена (и результат будет отброшен). Если ваше условие находится ниже всех вызовов хуков, вы можете добавить ранний `return;`, чтобы перезапустить рендеринг раньше.

---

## Устранение неполадок {/*troubleshooting*/}

### Я обновил состояние, но логирование выдаёт старое значение {/*ive-updated-the-state-but-logging-gives-me-the-old-value*/}

Вызов функции `set` **не изменяет состояние в работающем коде**:

```js {4,5,8}
function handleClick() {
  console.log(count);  // 0

  setCount(count + 1); // Запрос на перерисовку с 1
  console.log(count);  // Всё ещё 0!

  setTimeout(() => {
    console.log(count); // Тоже 0!
  }, 5000);
}
```

Это происходит потому, что [состояние ведёт себя как снимок](/learn/state-as-a-snapshot). Обновление состояния запрашивает другую перерисовку с новым значением состояния, но не влияет на переменную JavaScript `count` в вашем уже запущенном обработчике событий.

Если вам нужно использовать следующее состояние, вы можете сохранить его в переменной перед передачей в функцию `set`:

```js
const nextCount = count + 1;
setCount(nextCount);

console.log(count);     // 0
console.log(nextCount); // 1
```

---

### Я обновил состояние, но экран не обновляется {/*ive-updated-the-state-but-the-screen-doesnt-update*/}

React **проигнорирует ваше обновление, если следующее состояние равно предыдущему**, что определяется сравнением [`Object.is`](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Object/is). Обычно это происходит, когда вы напрямую изменяете объект или массив в состоянии:

```js
obj.x = 10;  // 🚩 Неправильно: мутация существующего объекта
setObj(obj); // 🚩 Ничего не делает
```

Вы мутировали существующий объект `obj` и передали его обратно в `setObj`, поэтому React проигнорировал обновление. Чтобы исправить это, вам нужно убедиться, что вы всегда [_заменяете_ объекты и массивы в состоянии, а не _мутируете_ их](#updating-objects-and-arrays-in-state):

```js
// ✅ Правильно: создание нового объекта
setObj({
  ...obj,
  x: 10
});
```

---

### Я получаю ошибку: "Слишком много перерисовок" {/*im-getting-an-error-too-many-re-renders*/}

Вы можете получить ошибку: `Слишком много перерисовок. React ограничивает количество перерисовок для предотвращения бесконечного цикла.` Обычно это означает, что вы безусловно устанавливаете состояние *во время рендеринга*, поэтому ваш компонент входит в цикл: рендеринг, установка состояния (что вызывает рендеринг), рендеринг, установка состояния (что вызывает рендеринг) и так далее. Очень часто это вызвано ошибкой в указании обработчика событий:

```js {1-2}
// 🚩 Неправильно: вызывает обработчик во время рендеринга
return <button onClick={handleClick()}>Нажми меня</button>

// ✅ Правильно: передаёт обработчик событий
return <button onClick={handleClick}>Нажми меня</button>

// ✅ Правильно: передаёт функцию-стрелку
return <button onClick={(e) => handleClick(e)}>Нажми меня</button>
```

Если вы не можете найти причину этой ошибки, нажмите на стрелку рядом с ошибкой в консоли и просмотрите стек JavaScript, чтобы найти конкретный вызов функции `set`, ответственный за ошибку.

---

### Моя функция инициализации или обновления вызывается дважды {/*my-initializer-or-updater-function-runs-twice*/}

В [Strict Mode](/reference/react/StrictMode) React будет вызывать некоторые ваши функции дважды вместо одного раза:

```js {2,5-6,11-12}
function TodoList() {
  // Эта функция компонента будет вызвана дважды для каждого рендеринга.

  const [todos, setTodos] = useState(() => {
    // Эта функция инициализации будет вызвана дважды во время инициализации.
    return createTodos();
  });

  function handleClick() {
    setTodos(prevTodos => {
      // Эта функция обновления будет вызвана дважды для каждого клика.
      return [...prevTodos, createTodo()];
    });
  }
  // ...
```

Это ожидаемо и не должно нарушить ваш код.

Это поведение, **предназначенное только для разработки**, помогает вам [сохранять чистоту компонентов](/learn/keeping-components-pure). React использует результат одного из вызовов и игнорирует результат другого. Пока ваш компонент, инициализатор и функции обновления чисты, это не повлияет на вашу логику. Однако, если они случайно окажутся нечистыми, это поможет вам заметить ошибки.

Например, эта нечистая функция обновления мутирует массив в состоянии:

```js {2,3}
setTodos(prevTodos => {
  // 🚩 Ошибка: мутация состояния
  prevTodos.push(createTodo());
});
```

Поскольку React вызывает вашу функцию обновления дважды, вы увидите, что todo был добавлен дважды, и таким образом узнаете об ошибке. В этом примере вы можете исправить ошибку, [заменив массив вместо мутации](#updating-objects-and-arrays-in-state):

```js {2,3}
setTodos(prevTodos => {
  // ✅ Правильно: замена новым состоянием
  return [...prevTodos, createTodo()];
});
```

Теперь, когда эта функция обновления чиста, её вызов лишний раз не влияет на поведение. Вот почему вызов её дважды помогает вам находить ошибки. **Только компоненты, инициализаторы и функции обновления должны быть чистыми.** Обработчики событий не должны быть чистыми, поэтому React никогда не вызовет ваши обработчики событий дважды.

Прочтите [сохранение чистоты компонентов](/learn/keeping-components-pure), чтобы узнать больше.

---

### Я пытаюсь установить состояние как функцию, но она вызывается вместо этого {/*im-trying-to-set-state-to-a-function-but-it-gets-called-instead*/}

Вы не можете поместить функцию в состояние таким образом:

```js
const [fn, setFn] = useState(someFunction);

function handleClick() {
  setFn(someOtherFunction);
}
```

Поскольку вы передаёте функцию, React предполагает, что `someFunction` является [функцией инициализации](#avoiding-recreating-the-initial-state), а `someOtherFunction` — [функцией обновления](#updating-state-based-on-the-previous-state), поэтому он пытается вызвать их и сохранить результат. Чтобы фактически *сохранить* функцию, вам нужно поместить `() =>` перед ними в обоих случаях. Тогда React сохранит переданные вами функции.

```js {1,4}
const [fn, setFn] = useState(() => someFunction);

function handleClick() {
  setFn(() => someOtherFunction);
}
```