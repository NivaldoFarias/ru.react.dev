---
title: useState
---
```html
<Intro>

`useState` — это хук React, который позволяет добавить [переменную состояния](/learn/state-a-components-memory) в ваш компонент.

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

Принято называть переменные состояния, например, `[something, setSomething]` с помощью [деструктуризации массива.](https://javascript.info/destructuring-assignment)

[См. больше примеров ниже.](#usage)

#### Параметры {/*parameters*/}

*   `initialState`: Значение, которое вы хотите, чтобы состояние имело изначально. Это может быть значение любого типа, но для функций существует особое поведение. Этот аргумент игнорируется после начального рендеринга.
    *   Если вы передаете функцию в качестве `initialState`, она будет рассматриваться как _функция инициализации_. Она должна быть чистой, не должна принимать аргументов и должна возвращать значение любого типа. React вызовет вашу функцию инициализации при инициализации компонента и сохранит ее возвращаемое значение в качестве начального состояния. [См. пример ниже.](#avoiding-recreating-the-initial-state)

#### Возвращает {/*returns*/}

`useState` возвращает массив ровно с двумя значениями:

1.  Текущее состояние. Во время первого рендеринга оно будет соответствовать переданному вами `initialState`.
2.  [`set` функцию](#setstate), которая позволяет обновить состояние до другого значения и запустить повторный рендеринг.

#### Предостережения {/*caveats*/}

*   `useState` — это хук, поэтому вы можете вызывать его только **на верхнем уровне вашего компонента** или ваших собственных хуков. Вы не можете вызывать его внутри циклов или условий. Если вам это нужно, извлеките новый компонент и переместите состояние в него.
*   В строгом режиме React **вызовет вашу функцию инициализации дважды**, чтобы [помочь вам найти случайные примеси.](#my-initializer-or-updater-function-runs-twice) Это поведение только для разработки и не влияет на продакшен. Если ваша функция инициализации чистая (как и должно быть), это не должно влиять на поведение. Результат одного из вызовов будет проигнорирован.

---

### `set` функции, такие как `setSomething(nextState)` {/*setstate*/}

Функция `set`, возвращаемая `useState`, позволяет обновить состояние до другого значения и запустить повторный рендеринг. Вы можете передать следующее состояние напрямую или функцию, которая вычисляет его из предыдущего состояния:

```js
const [name, setName] = useState('Edward');

function handleClick() {
  setName('Taylor');
  setAge(a => a + 1);
  // ...
```

#### Параметры {/*setstate-parameters*/}

*   `nextState`: Значение, которое вы хотите, чтобы имело состояние. Это может быть значение любого типа, но для функций существует особое поведение.
    *   Если вы передаете функцию в качестве `nextState`, она будет рассматриваться как _функция обновления_. Она должна быть чистой, должна принимать ожидающее состояние в качестве единственного аргумента и должна возвращать следующее состояние. React поместит вашу функцию обновления в очередь и повторно отрендерит ваш компонент. Во время следующего рендеринга React вычислит следующее состояние, применив все поставленные в очередь обновления к предыдущему состоянию. [См. пример ниже.](#updating-state-based-on-the-previous-state)

#### Возвращает {/*setstate-returns*/}

`set` функции не имеют возвращаемого значения.

#### Предостережения {/*setstate-caveats*/}

*   Функция `set` **обновляет переменную состояния только для *следующего* рендеринга**. Если вы читаете переменную состояния после вызова функции `set`, [вы все равно получите старое значение](#ive-updated-the-state-but-logging-gives-me-the-old-value), которое было на экране до вашего вызова.

*   Если новое значение, которое вы предоставляете, идентично текущему `state`, как определено сравнением [`Object.is`](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Object/is), React **пропустит повторный рендеринг компонента и его дочерних элементов.** Это оптимизация. Хотя в некоторых случаях React может по-прежнему вызывать ваш компонент перед пропуском дочерних элементов, это не должно влиять на ваш код.

*   React [группирует обновления состояния.](/learn/queueing-a-series-of-state-updates) Он обновляет экран **после того, как все обработчики событий завершили работу** и вызвали свои функции `set`. Это предотвращает множественные повторные рендеринги во время одного события. В редких случаях, когда вам нужно заставить React обновить экран раньше, например, для доступа к DOM, вы можете использовать [`flushSync`.](/reference/react-dom/flushSync)

*   Функция `set` имеет стабильную идентичность, поэтому вы часто будете видеть, что она опущена из зависимостей Effect, но включение ее не приведет к запуску Effect. Если линтер позволяет вам опустить зависимость без ошибок, это безопасно. [Узнайте больше об удалении зависимостей Effect.](/learn/removing-effect-dependencies#move-dynamic-objects-and-functions-inside-your-effect)

*   Вызов функции `set` *во время рендеринга* разрешен только из текущего рендеринга компонента. React отбросит его вывод и немедленно попытается отрендерить его снова с новым состоянием. Этот шаблон редко требуется, но вы можете использовать его для **хранения информации из предыдущих рендерингов**. [См. пример ниже.](#storing-information-from-previous-renders)

*   В строгом режиме React **вызовет вашу функцию обновления дважды**, чтобы [помочь вам найти случайные примеси.](#my-initializer-or-updater-function-runs-twice) Это поведение только для разработки и не влияет на продакшен. Если ваша функция обновления чистая (как и должно быть), это не должно влиять на поведение. Результат одного из вызовов будет проигнорирован.

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

Принято называть переменные состояния, например, `[something, setSomething]` с помощью [деструктуризации массива.](https://javascript.info/destructuring-assignment)

`useState` возвращает массив ровно с двумя элементами:

1.  <CodeStep step={1}>Текущее состояние</CodeStep> этой переменной состояния, изначально установленное в <CodeStep step={3}>начальное состояние</CodeStep>, которое вы предоставили.
2.  <CodeStep step={2}>`set` функцию</CodeStep>, которая позволяет вам изменить его на любое другое значение в ответ на взаимодействие.

Чтобы обновить то, что находится на экране, вызовите функцию `set` с некоторым следующим состоянием:

```js [[2, 2, "setName"]]
function handleClick() {
  setName('Robin');
}
```

React сохранит следующее состояние, снова отрендерит ваш компонент с новыми значениями и обновит UI.

<Pitfall>

Вызов функции `set` [**не** изменяет текущее состояние в уже выполняющемся коде](#ive-updated-the-state-but-logging-gives-me-the-old-value):

```js {3}
function handleClick() {
  setName('Robin');
  console.log(name); // Still "Taylor"!
}
```

Это влияет только на то, что `useState` вернет, начиная со следующего рендеринга.

</Pitfall>

<Recipes titleText="Основные примеры useState" titleId="examples-basic">

#### Счетчик (число) {/*counter-number*/}

В этом примере переменная состояния `count` содержит число. Нажатие кнопки увеличивает его.

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

#### Текстовое поле (строка) {/*text-field-string*/}

В этом примере переменная состояния `text` содержит строку. Когда вы печатаете, `handleChange` считывает последнее введенное значение из элемента DOM ввода браузера и вызывает `setText`, чтобы обновить состояние. Это позволяет вам отображать текущий `text` ниже.

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

#### Чекбокс (логическое значение) {/*checkbox-boolean*/}

В этом примере переменная состояния `liked` содержит логическое значение. Когда вы нажимаете на ввод, `setLiked` обновляет переменную состояния `liked` с помощью того, отмечен ли чекбокс ввода браузера. Переменная `liked` используется для отображения текста под чекбоксом.

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

Вы можете объявить более одной переменной состояния в одном и том же компоненте. Каждая переменная состояния полностью независима.

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

Предположим, что `age` равно `42`. Этот обработчик вызывает `setAge(age + 1)` три раза:

```js
function handleClick() {
  setAge(age + 1); // setAge(42 + 1)
  setAge(age + 1); // setAge(42 + 1)
  setAge(age + 1); // setAge(42 + 1)
}
```

Однако после одного щелчка `age` будет равно только `43`, а не `45`! Это связано с тем, что вызов функции `set` [не обновляет](/learn/state-as-a-snapshot) переменную состояния `age` в уже запущенном коде. Поэтому каждый вызов `setAge(age + 1)` становится `setAge(43)`.

Чтобы решить эту проблему, **вы можете передать *функцию обновления*** в `setAge` вместо следующего состояния:

```js [[1, 2, "a", 0], [2, 2, "a + 1"], [1, 3, "a", 0], [2, 3, "a + 1"], [1, 4, "a", 0], [2, 4, "a + 1"]]
function handleClick() {
  setAge(a => a + 1); // setAge(42 => 43)
  setAge(a => a + 1); // setAge(43 => 44)
  setAge(a => a + 1); // setAge(44 => 45)
}
```

Здесь `a => a + 1` — ваша функция обновления. Она принимает <CodeStep step={1}>ожидающее состояние</CodeStep> и вычисляет <CodeStep step={2}>следующее состояние</CodeStep> из него.

React помещает ваши функции обновления в [очередь.](/learn/queueing-a-series-of-state-updates) Затем, во время следующего рендеринга, он вызовет их в том же порядке:

1.  `a => a + 1` получит `42` в качестве ожидающего состояния и вернет `43` в качестве следующего состояния.
2.  `a => a + 1` получит `43` в качестве ожидающего состояния и вернет `44` в качестве следующего состояния.
3.  `a => a + 1` получит `44` в качестве ожидающего состояния и вернет `45` в качестве следующего состояния.

Других поставленных в очередь обновлений нет, поэтому React в конечном итоге сохранит `45` в качестве текущего состояния.

По соглашению, обычно называют аргумент ожидающего состояния первой буквой имени переменной состояния, например, `a` для `age`. Однако вы также можете назвать его, например, `prevAge` или как-то еще, что вам кажется более понятным.

React может [вызвать ваши обновления дважды](#my-initializer-or-updater-function-runs-twice) в процессе разработки, чтобы убедиться, что они [чистые.](/learn/keeping-components-pure)

<DeepDive>

#### Всегда ли предпочтительно использовать обновление? {/*is-using-an-updater-always-preferred*/}

Вы можете услышать рекомендацию всегда писать код вроде `setAge(a => a + 1)`, если состояние, которое вы устанавливаете, вычисляется из предыдущего состояния. В этом нет ничего плохого, но это также не всегда необходимо.

В большинстве случаев нет никакой разницы между этими двумя подходами. React всегда следит за тем, чтобы для преднамеренных действий пользователя, таких как щелчки, переменная состояния `age` обновлялась до следующего щелчка. Это означает, что нет риска того, что обработчик щелчка увидит «устаревшее» значение `age` в начале обработчика события.

Однако, если вы выполняете несколько обновлений в рамках одного и того же события, обновления могут быть полезны. Они также полезны, если доступ к самой переменной состояния неудобен (вы можете столкнуться с этим при оптимизации повторных рендерингов).

Если вы предпочитаете согласованность немного более многословному синтаксису, разумно всегда писать обновление, если состояние, которое вы устанавливаете, вычисляется из предыдущего состояния. Если оно вычисляется из предыдущего состояния какой-то *другой* переменной состояния, вы можете объединить их в один объект и [использовать редьюсер.](/learn/extracting-state-logic-into-a-reducer)

</DeepDive>

<Recipes titleText="Разница между передачей обновления и прямой передачей следующего состояния" titleId="examples-updater">

#### Передача функции обновления {/*passing-the-updater-function*/}

В этом примере передается функция обновления, поэтому кнопка «+3» работает.

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

#### Прямая передача следующего состояния {/*passing-the-next-state-directly*/}

В этом примере **не** передается функция обновления, поэтому кнопка «+3» **не работает должным образом**.

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

Вы можете помещать объекты и массивы в состояние. В React состояние считается доступным только для чтения, поэтому **вы должны *заменять* его, а не *изменять* существующие объекты**. Например, если у вас есть объект `form` в состоянии, не изменяйте его:

```js
// 🚩 Не изменяйте объект в состоянии вот так:
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

Прочтите [обновление объектов в состоянии](/learn/updating-objects-in-state) и [обновление массивов в состоянии](/learn/updating-arrays-in-state), чтобы узнать больше.

<Recipes titleText="Примеры объектов и массивов в состоянии" titleId="examples-objects">

#### Форма (объект) {/*form-object*/}

В этом примере переменная состояния `form` содержит объект. Каждый ввод имеет обработчик изменений, который вызывает `setForm` со следующим состоянием всей формы. Синтаксис `{ ...form }` обеспечивает замену, а не изменение объекта состояния.

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

В этом примере состояние более вложенное. Когда вы обновляете вложенное состояние, вам нужно создать копию объекта, который вы обновляете, а также любых объектов, «содержащих» его на пути вверх. Прочтите [обновление вложенного объекта](/learn/updating-objects-in-state#updating-a-nested-object), чтобы узнать больше.

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

В этом примере переменная состояния `todos` содержит массив. Каждый обработчик кнопки вызывает `setTodos` со следующей версией этого массива. Синтаксис `[...todos]`, `todos.map()` и `todos.filter()` гарантируют, что массив состояния заменяется, а не изменяется.

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

#### Написание краткой логики обновления с помощью Immer {/*writing-concise-update-logic-with-immer*/}

Если обновление массивов и объектов без мутации кажется утомительным, вы можете использовать такую библиотеку, как [Immer](https://github.com/immerjs/use-immer), чтобы уменьшить повторяющийся код. Immer позволяет вам писать краткий код, как если бы вы изменяли объекты, но под капотом он выполняет неизменяемые обновления:

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

React сохраняет начальное состояние один раз и игнорирует его при следующих рендерингах.

```js
function TodoList() {
  const [todos, setTodos] = useState(createInitialTodos());
  // ...
```

Хотя результат `createInitialTodos()` используется только для начального рендеринга, вы все равно вызываете эту функцию при каждом рендеринге. Это может быть пустой тратой времени, если она создает большие массивы или выполняет дорогостоящие вычисления.

Чтобы решить эту проблему, вы можете **передать ее в качестве _функции инициализации_** в `useState` вместо этого:

```js
function TodoList() {
  const [todos, setTodos] = useState(createInitialTodos);
  // ...
```

Обратите внимание, что вы передаете `createInitialTodos`, которая является *самой функцией*, а не `createInitialTodos()`, которая является результатом ее вызова. Если вы передаете функцию в `useState`, React вызовет ее только во время инициализации.

React может [вызвать ваши инициализаторы дважды](#my-initializer-or-updater-function-runs-twice) в процессе разработки, чтобы убедиться, что они [чистые.](/learn/keeping-components-pure)

<Recipes titleText="Разница между передачей инициализатора и прямой передачей начального состояния" titleId="examples-initializer">

#### Передача функции инициализации {/*passing-the-initializer-function*/}

В этом примере передается функция инициализации, поэтому функция `createInitialTodos` выполняется только во время инициализации. Она не выполняется при повторном рендеринге компонента, например, при вводе текста в поле ввода.

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

#### Прямая передача начального состояния {/*passing-the-initial-state-directly*/}

В этом примере **не** передается функция инициализации, поэтому функция `createInitialTodos` выполняется при каждом рендеринге, например, при вводе текста в поле ввода. Нет наблюдаемой разницы в поведении, но этот код менее эффективен.

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
        value