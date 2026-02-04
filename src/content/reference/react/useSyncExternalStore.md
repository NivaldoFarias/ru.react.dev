---
title: useSyncExternalStore
---

<Intro>

`useSyncExternalStore` — это хук React, который позволяет подписываться на внешний источник данных.

```js
const snapshot = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?)
```

</Intro>

<InlineToc />

---

## Справочник {/*reference*/}

### `useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?)` {/*usesyncexternalstore*/}

Вызовите `useSyncExternalStore` на верхнем уровне вашего компонента, чтобы прочитать значение из внешнего источника данных.

```js
import { useSyncExternalStore } from 'react';
import { todosStore } from './todoStore.js';

function TodosApp() {
  const todos = useSyncExternalStore(todosStore.subscribe, todosStore.getSnapshot);
  // ...
}
```

Он возвращает снимок данных из хранилища. Вам нужно передать две функции в качестве аргументов:

1. Функция `subscribe` должна подписаться на хранилище и вернуть функцию, которая отписывает.
2. Функция `getSnapshot` должна прочитать снимок данных из хранилища.

[См. примеры ниже.](#usage)

#### Параметры {/*parameters*/}

* `subscribe`: Функция, которая принимает один аргумент `callback` и подписывает его на хранилище. Когда хранилище изменяется, она должна вызвать предоставленный `callback`, что приведет к повторному вызову `getSnapshot` React и (при необходимости) повторному рендерингу компонента. Функция `subscribe` должна вернуть функцию, которая очищает подписку.

* `getSnapshot`: Функция, которая возвращает снимок данных из хранилища, необходимый компоненту. Пока хранилище не изменилось, повторные вызовы `getSnapshot` должны возвращать одно и то же значение. Если хранилище изменяется, а возвращаемое значение отличается (при сравнении с помощью [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is)), React повторно отрендерит компонент.

* **необязательно** `getServerSnapshot`: Функция, которая возвращает начальный снимок данных из хранилища. Она будет использоваться только во время серверного рендеринга и во время гидратации контента, отрендеренного на сервере, на клиенте. Серверный снимок должен быть одинаковым между клиентом и сервером и обычно сериализуется и передается с сервера на клиент. Если вы опустите этот аргумент, рендеринг компонента на сервере вызовет ошибку.

#### Возвращает {/*returns*/}

Текущий снимок хранилища, который вы можете использовать в своей логике рендеринга.

#### Предостережения {/*caveats*/}

* Снимок хранилища, возвращаемый `getSnapshot`, должен быть неизменяемым. Если базовое хранилище содержит изменяемые данные, верните новый неизменяемый снимок, если данные изменились. В противном случае верните кэшированный последний снимок.

* Если во время [неблокирующего обновления Transition](/reference/react/useTransition) передается другая функция `subscribe`, React повторно подпишется на хранилище, используя новую переданную функцию `subscribe`. Вы можете предотвратить это, объявив `subscribe` вне компонента.

* Если хранилище изменяется во время [неблокирующего обновления Transition](/reference/react/useTransition), React откатится к выполнению этого обновления как блокирующего. В частности, для каждого обновления Transition React вызовет `getSnapshot` второй раз непосредственно перед применением изменений к DOM. Если он вернет значение, отличное от того, когда он был вызван изначально, React перезапустит обновление с нуля, на этот раз применяя его как блокирующее обновление, чтобы гарантировать, что каждый компонент на экране отражает одну и ту же версию хранилища.

* Не рекомендуется _приостанавливать_ рендеринг на основе значения хранилища, возвращаемого `useSyncExternalStore`. Причина в том, что мутации внешнего хранилища не могут быть помечены как [неблокирующие обновления Transition](/reference/react/useTransition), поэтому они вызовут ближайший [`Suspense` fallback](/reference/react/Suspense), заменяя уже отрендеренный контент на экране индикатором загрузки, что обычно приводит к плохому пользовательскому опыту.

  Например, следующее не рекомендуется:

  ```js
  const LazyProductDetailPage = lazy(() => import('./ProductDetailPage.js'));

  function ShoppingApp() {
    const selectedProductId = useSyncExternalStore(...);

    // ❌ Вызов `use` с Promise, зависящим от `selectedProductId`
    const data = use(fetchItem(selectedProductId))

    // ❌ Условный рендеринг ленивого компонента на основе `selectedProductId`
    return selectedProductId != null ? <LazyProductDetailPage /> : <FeaturedProducts />;
  }
  ```

---

## Использование {/*usage*/}

### Подписка на внешний источник данных {/*subscribing-to-an-external-store*/}

Большинство ваших React-компонентов будут только читать данные из своих [пропсов](/learn/passing-props-to-a-component), [состояния](/reference/react/useState) и [контекста](/reference/react/useContext). Однако иногда компоненту нужно читать какие-то данные из хранилища вне React, которое изменяется со временем. Сюда входят:

* Сторонние библиотеки управления состоянием, которые хранят состояние вне React.
* Браузерные API, которые предоставляют изменяемое значение и события для подписки на его изменения.

Вызовите `useSyncExternalStore` на верхнем уровне вашего компонента, чтобы прочитать значение из внешнего источника данных.

```js [[1, 5, "todosStore.subscribe"], [2, 5, "todosStore.getSnapshot"], [3, 5, "todos", 0]]
import { useSyncExternalStore } from 'react';
import { todosStore } from './todoStore.js';

function TodosApp() {
  const todos = useSyncExternalStore(todosStore.subscribe, todosStore.getSnapshot);
  // ...
}
```

Он возвращает <CodeStep step={3}>снимок</CodeStep> данных из хранилища. Вам нужно передать две функции в качестве аргументов:

1. <CodeStep step={1}>Функция `subscribe`</CodeStep> должна подписаться на хранилище и вернуть функцию, которая отписывает.
2. <CodeStep step={2}>Функция `getSnapshot`</CodeStep> должна прочитать снимок данных из хранилища.

React будет использовать эти функции, чтобы поддерживать подписку вашего компонента на хранилище и повторно рендерить его при изменениях.

Например, в приведенной ниже песочнице `todosStore` реализован как внешнее хранилище, которое хранит данные вне React. Компонент `TodosApp` подключается к этому внешнему хранилищу с помощью хука `useSyncExternalStore`.

<Sandpack>

```js
import { useSyncExternalStore } from 'react';
import { todosStore } from './todoStore.js';

export default function TodosApp() {
  const todos = useSyncExternalStore(todosStore.subscribe, todosStore.getSnapshot);
  return (
    <>
      <button onClick={() => todosStore.addTodo()}>Add todo</button>
      <hr />
      <ul>
        {todos.map(todo => (
          <li key={todo.id}>{todo.text}</li>
        ))}
      </ul>
    </>
  );
}
```

```js src/todoStore.js
// This is an example of a third-party store
// that you might need to integrate with React.

// If your app is fully built with React,
// we recommend using React state instead.

let nextId = 0;
let todos = [{ id: nextId++, text: 'Todo #1' }];
let listeners = [];

export const todosStore = {
  addTodo() {
    todos = [...todos, { id: nextId++, text: 'Todo #' + nextId }]
    emitChange();
  },
  subscribe(listener) {
    listeners = [...listeners, listener];
    return () => {
      listeners = listeners.filter(l => l !== listener);
    };
  },
  getSnapshot() {
    return todos;
  }
};

function emitChange() {
  for (let listener of listeners) {
    listener();
  }
}
```

</Sandpack>

<Note>

По возможности мы рекомендуем использовать встроенное состояние React с [`useState`](/reference/react/useState) и [`useReducer`](/reference/react/useReducer). API `useSyncExternalStore` в основном полезен, если вам нужно интегрироваться с существующим кодом, не относящимся к React.

</Note>

---

### Подписка на браузерный API {/*subscribing-to-a-browser-api*/}

Еще одна причина использовать `useSyncExternalStore` — это когда вы хотите подписаться на какое-либо значение, предоставляемое браузером, которое изменяется со временем. Например, предположим, вы хотите, чтобы ваш компонент отображал, активно ли сетевое соединение. Браузер предоставляет эту информацию через свойство [`navigator.onLine`.](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/onLine)

Это значение может изменяться без ведома React, поэтому вам следует читать его с помощью `useSyncExternalStore`.

```js
import { useSyncExternalStore } from 'react';

function ChatIndicator() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);
  // ...
}
```

Чтобы реализовать функцию `getSnapshot`, прочитайте текущее значение из браузерного API:

```js
function getSnapshot() {
  return navigator.onLine;
}
```

Далее вам нужно реализовать функцию `subscribe`. Например, когда `navigator.onLine` изменяется, браузер запускает события [`online`](https://developer.mozilla.org/en-US/docs/Web/API/Window/online_event) и [`offline`](https://developer.mozilla.org/en-US/docs/Web/API/Window/offline_event) в объекте `window`. Вам нужно подписать аргумент `callback` на соответствующие события, а затем вернуть функцию, которая очищает подписки:

```js
function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}
```

Теперь React знает, как читать значение из внешнего API `navigator.onLine` и как подписаться на его изменения. Отключите устройство от сети и заметьте, что компонент перерисовывается в ответ:

<Sandpack>

```js
import { useSyncExternalStore } from 'react';

export default function ChatIndicator() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);
  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}

function getSnapshot() {
  return navigator.onLine;
}

function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}
```

</Sandpack>

---

### Извлечение логики в пользовательский хук {/*extracting-the-logic-to-a-custom-hook*/}

Обычно вы не будете писать `useSyncExternalStore` напрямую в своих компонентах. Вместо этого вы, как правило, будете вызывать его из собственного пользовательского хука. Это позволяет использовать одно и то же внешнее хранилище из разных компонентов.

Например, этот пользовательский хук `useOnlineStatus` отслеживает, находится ли сеть в сети:

```js {3,6}
import { useSyncExternalStore } from 'react';

export function useOnlineStatus() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);
  return isOnline;
}

function getSnapshot() {
  // ...
}

function subscribe(callback) {
  // ...
}
```

Теперь разные компоненты могут вызывать `useOnlineStatus` без повторения базовой реализации:

<Sandpack>

```js
import { useOnlineStatus } from './useOnlineStatus.js';

function StatusBar() {
  const isOnline = useOnlineStatus();
  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}

function SaveButton() {
  const isOnline = useOnlineStatus();

  function handleSaveClick() {
    console.log('✅ Progress saved');
  }

  return (
    <button disabled={!isOnline} onClick={handleSaveClick}>
      {isOnline ? 'Save progress' : 'Reconnecting...'}
    </button>
  );
}

export default function App() {
  return (
    <>
      <SaveButton />
      <StatusBar />
    </>
  );
}
```

```js src/useOnlineStatus.js
import { useSyncExternalStore } from 'react';

export function useOnlineStatus() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);
  return isOnline;
}

function getSnapshot() {
  return navigator.onLine;
}

function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}
```

</Sandpack>

---

### Добавление поддержки серверного рендеринга {/*adding-support-for-server-rendering*/}

Если ваше React-приложение использует [серверный рендеринг](/reference/react-dom/server), ваши React-компоненты также будут выполняться вне браузерной среды для генерации начального HTML. Это создает несколько проблем при подключении к внешнему хранилищу:

- Если вы подключаетесь к API, работающему только в браузере, он не будет работать, так как не существует на сервере.
- Если вы подключаетесь к стороннему хранилищу данных, вам понадобятся данные, которые совпадают между сервером и клиентом.

Чтобы решить эти проблемы, передайте функцию `getServerSnapshot` в качестве третьего аргумента в `useSyncExternalStore`:

```js {4,12-14}
import { useSyncExternalStore } from 'react';

export function useOnlineStatus() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot);
  return isOnline;
}

function getSnapshot() {
  return navigator.onLine;
}

function getServerSnapshot() {
  return true; // Всегда показывать "Online" для HTML, сгенерированного сервером
}

function subscribe(callback) {
  // ...
}
```

Функция `getServerSnapshot` похожа на `getSnapshot`, но она выполняется только в двух случаях:

- Она выполняется на сервере при генерации HTML.
- Она выполняется на клиенте во время [гидратации](/reference/react-dom/client/hydrateRoot), то есть когда React берет HTML сервера и делает его интерактивным.

Это позволяет вам предоставить начальное значение снимка, которое будет использоваться до того, как приложение станет интерактивным. Если нет значимого начального значения для серверного рендеринга, опустите этот аргумент, чтобы [принудительно рендерить на клиенте.](/reference/react/Suspense#providing-a-fallback-for-server-errors-and-client-only-content)

<Note>

Убедитесь, что `getServerSnapshot` возвращает те же самые данные при начальном рендеринге клиента, что и при рендеринге на сервере. Например, если `getServerSnapshot` вернул некоторый предварительно заполненный контент хранилища на сервере, вам нужно передать этот контент на клиент. Один из способов сделать это — вывести тег `<script>` во время серверного рендеринга, который устанавливает глобальную переменную, например `window.MY_STORE_DATA`, и читать из этой глобальной переменной на клиенте в `getServerSnapshot`. Ваше внешнее хранилище должно предоставлять инструкции о том, как это сделать.

</Note>

---

## Устранение неполадок {/*troubleshooting*/}

### Я получаю ошибку: «Результат `getSnapshot` должен быть закэширован» {/*im-getting-an-error-the-result-of-getsnapshot-should-be-cached*/}

Эта ошибка означает, что ваша функция `getSnapshot` возвращает новый объект при каждом вызове, например:

```js {2-5}
function getSnapshot() {
  // 🔴 Не возвращайте всегда разные объекты из getSnapshot
  return {
    todos: myStore.todos
  };
}
```

React будет повторно отображать компонент, если `getSnapshot` вернёт значение, отличное от предыдущего. Поэтому, если вы всегда возвращаете другое значение, вы войдёте в бесконечный цикл и получите эту ошибку.

Ваш объект `getSnapshot` должен возвращать другой объект только в том случае, если что-то действительно изменилось. Если ваш магазин содержит неизменяемые данные, вы можете вернуть эти данные напрямую:

```js {2-3}
function getSnapshot() {
  // ✅ Можно возвращать неизменяемые данные
  return myStore.todos;
}
```

Если данные вашего магазина изменяемы, ваша функция `getSnapshot` должна возвращать их неизменяемый снимок. Это означает, что ей *действительно* нужно создавать новые объекты, но не при каждом вызове. Вместо этого она должна хранить последний вычисленный снимок и возвращать тот же снимок, что и в прошлый раз, если данные в магазине не изменились. То, как вы определяете, изменились ли изменяемые данные, зависит от вашего изменяемого магазина.

---

### Моя функция `subscribe` вызывается после каждого повторного рендеринга {/*my-subscribe-function-gets-called-after-every-re-render*/}

Эта функция `subscribe` определена *внутри* компонента, поэтому она отличается при каждом повторном рендеринге:

```js {2-5}
function ChatIndicator() {
  // 🚩 Всегда разная функция, поэтому React будет повторно подписываться при каждом повторном рендеринге
  function subscribe() {
    // ...
  }
  
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);

  // ...
}
```
  
React будет повторно подписываться на ваш магазин, если вы передадите другую функцию `subscribe` между повторными рендерингами. Если это вызывает проблемы с производительностью и вы хотите избежать повторной подписки, вынесите функцию `subscribe` наружу:

```js {1-4}
// ✅ Всегда одна и та же функция, поэтому React не нужно будет повторно подписываться
function subscribe() {
  // ...
}

function ChatIndicator() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);
  // ...
}
```

В качестве альтернативы оберните `subscribe` в [`useCallback`](/reference/react/useCallback), чтобы повторно подписываться только при изменении какого-либо аргумента:

```js {2-5}
function ChatIndicator({ userId }) {
  // ✅ Та же функция, пока userId не изменится
  const subscribe = useCallback(() => {
    // ...
  }, [userId]);
  
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);

  // ...
}
```