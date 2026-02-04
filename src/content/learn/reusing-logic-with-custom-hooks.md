---
title: 'Повторное использование логики с помощью пользовательских хуков'
---

<Intro>

React поставляется с несколькими встроенными хуками, такими как `useState`, `useContext` и `useEffect`. Иногда вам может захотеться, чтобы существовал хук для более специфической цели: например, для получения данных, отслеживания того, находится ли пользователь в сети, или для подключения к чату. Вы можете не найти эти хуки в React, но вы можете создавать свои собственные хуки для нужд вашего приложения.

</Intro>

<YouWillLearn>

- Что такое пользовательские хуки и как писать свои собственные
- Как повторно использовать логику между компонентами
- Как называть и структурировать свои пользовательские хуки
- Когда и почему следует извлекать пользовательские хуки

</YouWillLearn>

## Пользовательские хуки: Повторное использование логики между компонентами {/*custom-hooks-sharing-logic-between-components*/}

Представьте, что вы разрабатываете приложение, которое сильно зависит от сети (как и большинство приложений). Вы хотите предупредить пользователя, если его сетевое соединение случайно отключилось во время использования вашего приложения. Как бы вы это сделали? Похоже, вам понадобятся две вещи в вашем компоненте:

1. Часть состояния, которая отслеживает, подключена ли сеть.
2. Эффект, который подписывается на глобальные события [`online`](https://developer.mozilla.org/en-US/docs/Web/API/Window/online_event) и [`offline`](https://developer.mozilla.org/en-US/docs/Web/API/Window/offline_event) и обновляет это состояние.

Это позволит вашему компоненту [синхронизироваться](/learn/synchronizing-with-effects) со статусом сети. Вы можете начать с чего-то вроде этого:

<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function StatusBar() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);

  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}
```

</Sandpack>

Попробуйте включить и выключить сеть и заметьте, как `StatusBar` обновляется в ответ на ваши действия.

Теперь представьте, что вы хотите использовать ту же логику в другом компоненте. Вы хотите реализовать кнопку "Сохранить", которая будет отключена и будет показывать "Переподключение..." вместо "Сохранить", пока сеть отключена.

Для начала вы можете скопировать и вставить состояние `isOnline` и эффект в `SaveButton`:

<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function SaveButton() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);

  function handleSaveClick() {
    console.log('✅ Progress saved');
  }

  return (
    <button disabled={!isOnline} onClick={handleSaveClick}>
      {isOnline ? 'Save progress' : 'Reconnecting...'}
    </button>
  );
}
```

</Sandpack>

Убедитесь, что если вы отключите сеть, кнопка изменит свой внешний вид.

Эти два компонента работают нормально, но дублирование логики между ними печально. Похоже, что, хотя у них разный *визуальный вид*, вы хотите повторно использовать логику между ними.

### Извлечение собственного пользовательского хука из компонента {/*extracting-your-own-custom-hook-from-a-component*/}

Представьте на мгновение, что, подобно [`useState`](/reference/react/useState) и [`useEffect`](/reference/react/useEffect), существует встроенный хук `useOnlineStatus`. Тогда оба этих компонента можно было бы упростить, и вы могли бы устранить дублирование между ними:

```js {2,7}
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
```

Хотя такого встроенного хука не существует, вы можете написать его сами. Объявите функцию с именем `useOnlineStatus` и переместите в нее весь дублирующийся код из ранее написанных вами компонентов:

```js {2-16}
function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);
  return isOnline;
}
```

В конце функции верните `isOnline`. Это позволит вашим компонентам читать это значение:

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
import { useState, useEffect } from 'react';

export function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);
  return isOnline;
}
```

</Sandpack>

Убедитесь, что переключение сети включает и выключает оба компонента.

Теперь в ваших компонентах меньше повторяющейся логики. **Что более важно, код внутри них описывает *что они хотят делать* (использовать статус сети!), а не *как это сделать* (подписываясь на события браузера).**

Когда вы извлекаете логику в пользовательские хуки, вы можете скрыть сложные детали того, как вы работаете с некоторой внешней системой или API браузера. Код ваших компонентов выражает ваше намерение, а не реализацию.

### Имена хуков всегда начинаются с `use` {/*hook-names-always-start-with-use*/}

Приложения React строятся из компонентов. Компоненты строятся из хуков, как встроенных, так и пользовательских. Вы, вероятно, часто будете использовать пользовательские хуки, созданные другими, но иногда вы можете написать один сами!

Вы должны следовать этим соглашениям об именовании:

1. **Имена компонентов React должны начинаться с заглавной буквы,** как `StatusBar` и `SaveButton`. Компоненты React также должны возвращать что-то, что React знает, как отобразить, например, фрагмент JSX.
2. **Имена хуков должны начинаться с `use`, за которым следует заглавная буква,** как [`useState`](/reference/react/useState) (встроенный) или `useOnlineStatus` (пользовательский, как ранее на странице). Хуки могут возвращать произвольные значения.

Это соглашение гарантирует, что вы всегда можете посмотреть на компонент и узнать, где могут "скрываться" его состояние, эффекты и другие функции React. Например, если вы видите вызов функции `getColor()` внутри вашего компонента, вы можете быть уверены, что она не может содержать состояние React, потому что ее имя не начинается с `use`. Однако вызов функции, такой как `useOnlineStatus()`, скорее всего, будет содержать вызовы других хуков!

<Note>

Если ваш линтер [настроен для React,](/learn/editor-setup#linting) он будет обеспечивать соблюдение этого соглашения об именовании. Прокрутите вверх до песочницы выше и переименуйте `useOnlineStatus` в `getOnlineStatus`. Обратите внимание, что линтер не позволит вам вызывать `useState` или `useEffect` внутри нее. Только хуки и компоненты могут вызывать другие хуки!

</Note>

<DeepDive>

#### Должны ли все функции, вызываемые во время рендеринга, начинаться с префикса use? {/*should-all-functions-called-during-rendering-start-with-the-use-prefix*/}

Нет. Функции, которые *не вызывают* хуки, не должны *быть* хуками.

Если ваша функция не вызывает никаких хуков, избегайте префикса `use`. Вместо этого напишите ее как обычную функцию *без* префикса `use`. Например, `useSorted` ниже не вызывает хуков, поэтому назовите ее `getSorted` вместо этого:

```js
// 🔴 Избегайте: Хук, который не использует хуки
function useSorted(items) {
  return items.slice().sort();
}

// ✅ Хорошо: Обычная функция, которая не использует хуки
function getSorted(items) {
  return items.slice().sort();
}
```

Это гарантирует, что ваш код может вызывать эту обычную функцию где угодно, в том числе в условных выражениях:

```js
function List({ items, shouldSort }) {
  let displayedItems = items;
  if (shouldSort) {
    // ✅ Можно вызывать getSorted() условно, потому что это не хук
    displayedItems = getSorted(items);
  }
  // ...
}
```

Вы должны дать префикс `use` функции (и, следовательно, сделать ее хуком), если она вызывает хотя бы один хук внутри себя:

```js
// ✅ Хорошо: Хук, который использует другие хуки
function useAuth() {
  return useContext(Auth);
}
```

Технически это не принуждается React. В принципе, вы можете создать хук, который не вызывает других хуков. Это часто сбивает с толку и ограничивает, поэтому лучше избегать такого шаблона. Однако могут быть редкие случаи, когда это полезно. Например, возможно, ваша функция пока не использует никаких хуков, но вы планируете добавить некоторые вызовы хуков в нее в будущем. Тогда имеет смысл назвать ее с префиксом `use`:

```js {3-4}
// ✅ Хорошо: Хук, который, вероятно, будет использовать другие хуки позже
function useAuth() {
  // TODO: Заменить этой строкой, когда будет реализована аутентификация:
  // return useContext(Auth);
  return TEST_USER;
}
```

Тогда компоненты не смогут вызывать ее условно. Это станет важным, когда вы фактически добавите вызовы хуков внутрь. Если вы не планируете использовать хуки внутри нее (сейчас или позже), не делайте ее хуком.

</DeepDive>

### Пользовательские хуки позволяют совместно использовать логику с состоянием, а не само состояние {/*custom-hooks-let-you-share-stateful-logic-not-state-itself*/}

В предыдущем примере, когда вы включали и выключали сеть, оба компонента обновлялись одновременно. Однако ошибочно полагать, что между ними совместно используется одна переменная состояния `isOnline`. Посмотрите на этот код:

```js {2,7}
function StatusBar() {
  const isOnline = useOnlineStatus();
  // ...
}

function SaveButton() {
  const isOnline = useOnlineStatus();
  // ...
}
```

Это работает так же, как и до извлечения дублирования:

```js {2-5,10-13}
function StatusBar() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    // ...
  }, []);
  // ...
}

function SaveButton() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    // ...
  }, []);
  // ...
}
```

Это две совершенно независимые переменные состояния и эффекты! Они случайно имели одинаковое значение в одно и то же время, потому что вы синхронизировали их с одним и тем же внешним значением (включена ли сеть).

Чтобы лучше проиллюстрировать это, нам понадобится другой пример. Рассмотрим этот компонент `Form`:

<Sandpack>

```js
import { useState } from 'react';

export default function Form() {
  const [firstName, setFirstName] = useState('Mary');
  const [lastName, setLastName] = useState('Poppins');

  function handleFirstNameChange(e) {
    setFirstName(e.target.value);
  }

  function handleLastNameChange(e) {
    setLastName(e.target.value);
  }

  return (
    <>
      <label>
        First name:
        <input value={firstName} onChange={handleFirstNameChange} />
      </label>
      <label>
        Last name:
        <input value={lastName} onChange={handleLastNameChange} />
      </label>
      <p><b>Good morning, {firstName} {lastName}.</b></p>
    </>
  );
}
```

```css
label { display: block; }
input { margin-left: 10px; }
```

</Sandpack>

Для каждого поля формы есть некоторая повторяющаяся логика:

1. Есть часть состояния (`firstName` и `lastName`).
1. Есть обработчик изменений (`handleFirstNameChange` и `handleLastNameChange`).
1. Есть фрагмент JSX, который определяет атрибуты `value` и `onChange` для этого ввода.

Вы можете извлечь повторяющуюся логику в этот пользовательский хук `useFormInput`:

<Sandpack>

```js
import { useFormInput } from './useFormInput.js';

export default function Form() {
  const firstNameProps = useFormInput('Mary');
  const lastNameProps = useFormInput('Poppins');

  return (
    <>
      <label>
        First name:
        <input {...firstNameProps} />
      </label>
      <label>
        Last name:
        <input {...lastNameProps} />
      </label>
      <p><b>Good morning, {firstNameProps.value} {lastNameProps.value}.</b></p>
    </>
  );
}
```

```js src/useFormInput.js active
import { useState } from 'react';

export function useFormInput(initialValue) {
  const [value, setValue] = useState(initialValue);

  function handleChange(e) {
    setValue(e.target.value);
  }

  const inputProps = {
    value: value,
    onChange: handleChange
  };

  return inputProps;
}
```

```css
label { display: block; }
input { margin-left: 10px; }
```

</Sandpack>

Обратите внимание, что он объявляет только *одну* переменную состояния под названием `value`.

Однако компонент `Form` вызывает `useFormInput` *два раза:*

```js
function Form() {
  const firstNameProps = useFormInput('Mary');
  const lastNameProps = useFormInput('Poppins');
  // ...
```

Вот почему это работает так же, как объявление двух отдельных переменных состояния!

**Пользовательские хуки позволяют совместно использовать *логику с состоянием*, но не *само состояние*. Каждый вызов хука полностью независим от любого другого вызова того же хука.** Вот почему два песочницы выше полностью эквивалентны. Если хотите, прокрутите вверх и сравните их. Поведение до и после извлечения пользовательского хука идентично.

Когда вам нужно совместно использовать само состояние между несколькими компонентами, [поднимите его вверх и передайте вниз](/learn/sharing-state-between-components) вместо этого.

## Передача реактивных значений между хуками {/*passing-reactive-values-between-hooks*/}

Код внутри ваших пользовательских хуков будет перезапускаться при каждом повторном рендеринге вашего компонента. Именно поэтому, как и компоненты, пользовательские хуки [должны быть чистыми.](/learn/keeping-components-pure) Думайте о коде пользовательских хуков как о части тела вашего компонента!

Поскольку пользовательские хуки перезапускаются вместе с вашим компонентом, они всегда получают последние пропсы и состояние. Чтобы увидеть, что это означает, рассмотрите этот пример чат-комнаты. Измените URL сервера или чат-комнату:

<Sandpack>

```js src/App.js
import { useState } from 'react';
import ChatRoom from './ChatRoom.js';

export default function App() {
  const [roomId, setRoomId] = useState('general');
  return (
    <>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <hr />
      <ChatRoom
        roomId={roomId}
      />
    </>
  );
}
```

```js src/ChatRoom.js active
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';
import { showNotification } from './notifications.js';

export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.on('message', (msg) => {
      showNotification('New message: ' + msg);
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, serverUrl]);

  return (
    <>
      <label>
        Server URL:
        <input value={serverUrl} onChange={e => setServerUrl(e.target.value)} />
      </label>
      <h1>Welcome to the {roomId} room!</h1>
    </>
  );
}
```

```js src/chat.js
export function createConnection({ serverUrl, roomId }) {
  // A real implementation would actually connect to the server
  if (typeof serverUrl !== 'string') {
    throw Error('Expected serverUrl to be a string. Received: ' + serverUrl);
  }
  if (typeof roomId !== 'string') {
    throw Error('Expected roomId to be a string. Received: ' + roomId);
  }
  let intervalId;
  let messageCallback;
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
      clearInterval(intervalId);
      intervalId = setInterval(() => {
        if (messageCallback) {
          if (Math.random() > 0.5) {
            messageCallback('hey')
          } else {
            messageCallback('lol');
          }
        }
      }, 3000);
    },
    disconnect() {
      clearInterval(intervalId);
      messageCallback = null;
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl + '');
    },
    on(event, callback) {
      if (messageCallback) {
        throw Error('Cannot add the handler twice.');
      }
      if (event !== 'message') {
        throw Error('Only "message" event is supported.');
      }
      messageCallback = callback;
    },
  };
}
```

```js src/notifications.js
import Toastify from 'toastify-js';
import 'toastify-js/src/toastify.css';

export function showNotification(message, theme = 'dark') {
  Toastify({
    text: message,
    duration: 2000,
    gravity: 'top',
    position: 'right',
    style: {
      background: theme === 'dark' ? 'black' : 'white',
      color: theme === 'dark' ? 'white' : 'black',
    },
  }).showToast();
}
```

```json package.json hidden
{
  "dependencies": {
    "react": "latest",
    "react-dom": "latest",
    "react-scripts": "latest",
    "toastify-js": "1.12.0"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

Когда вы изменяете `serverUrl` или `roomId`, эффект ["реагирует" на ваши изменения](/learn/lifecycle-of-reactive-effects#effects-react-to-reactive-values) и повторно синхронизируется. Вы можете увидеть в сообщениях консоли, что чат переподключается каждый раз, когда вы изменяете зависимости вашего эффекта.

Теперь переместите код эффекта в пользовательский хук:

```js {2-13}
export function useChatRoom({ serverUrl, roomId }) {
  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      showNotification('New message: ' + msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl]);
}
```

Это позволяет вашему компоненту `ChatRoom` вызывать ваш пользовательский хук, не беспокоясь о том, как он работает внутри:

```js {4-7}
export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl
  });

  return (
    <>
      <label>
        Server URL:
        <input value={serverUrl} onChange={e => setServerUrl(e.target.value)} />
      </label>
      <h1>Welcome to the {roomId} room!</h1>
    </>
  );
}
```

Это выглядит намного проще! (Но делает то же самое.)

Обратите внимание, что логика *по-прежнему реагирует* на изменения пропсов и состояния. Попробуйте изменить URL сервера или выбранную комнату:

<Sandpack>

```js src/App.js
import { useState } from 'react';
import ChatRoom from './ChatRoom.js';

export default function App() {
  const [roomId, setRoomId] = useState('general');
  return (
    <>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <hr />
      <ChatRoom
        roomId={roomId}
      />
    </>
  );
}
```

```js src/ChatRoom.js active
import { useState } from 'react';
import { useChatRoom } from './useChatRoom.js';

export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl
  });

  return (
    <>
      <label>
        Server URL:
        <input value={serverUrl} onChange={e => setServerUrl(e.target.value)} />
      </label>
      <h1>Welcome to the {roomId} room!</h1>
    </>
  );
}
```

```js src/useChatRoom.js
import { useEffect } from 'react';
import { createConnection } from './chat.js';
import { showNotification } from './notifications.js';

export function useChatRoom({ serverUrl, roomId }) {
  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      showNotification('New message: ' + msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl]);
}
```

```js src/chat.js
export function createConnection({ serverUrl, roomId }) {
  // A real implementation would actually connect to the server
  if (typeof serverUrl !== 'string') {
    throw Error('Expected serverUrl to be a string. Received: ' + serverUrl);
  }
  if (typeof roomId !== 'string') {
    throw Error('Expected roomId to be a string. Received: ' + roomId);
  }
  let intervalId;
  let messageCallback;
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
      clearInterval(intervalId);
      intervalId = setInterval(() => {
        if (messageCallback) {
          if (Math.random() > 0.5) {
            messageCallback('hey')
          } else {
            messageCallback('lol');
          }
        }
      }, 3000);
    },
    disconnect() {
      clearInterval(intervalId);
      messageCallback = null;
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl + '');
    },
    on(event, callback) {
      if (messageCallback) {
        throw Error('Cannot add the handler twice.');
      }
      if (event !== 'message') {
        throw Error('Only "message" event is supported.');
      }
      messageCallback = callback;
    },
  };
}
```

```js src/notifications.js
import Toastify from 'toastify-js';
import 'toastify-js/src/toastify.css';

export function showNotification(message, theme = 'dark') {
  Toastify({
    text: message,
    duration: 2000,
    gravity: 'top',
    position: 'right',
    style: {
      background: theme === 'dark' ? 'black' : 'white',
      color: theme === 'dark' ? 'white' : 'black',
    },
  }).showToast();
}
```

```json package.json hidden
{
  "dependencies": {
    "react": "latest",
    "react-dom": "latest",
    "react-scripts": "latest",
    "toastify-js": "1.12.0"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

Обратите внимание, как вы берете возвращаемое значение одного хука:

```js {2}
export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl
  });
  // ...
```

и передаете его как входное значение другому хуку:

```js {6}
export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl
  });
  // ...
```

Каждый раз, когда ваш компонент `ChatRoom` повторно рендерится, он передает последний `roomId` и `serverUrl` вашему хуку. Вот почему ваш эффект повторно подключается к чату всякий раз, когда их значения отличаются после повторного рендеринга. (Если вы когда-либо работали с программным обеспечением для обработки аудио или видео, цепочка хуков, подобная этой, может напомнить вам о цепочке визуальных или аудиоэффектов. Как будто вывод `useState` "поступает" во входные данные `useChatRoom`.)

### Передача обработчиков событий в пользовательские хуки {/*passing-event-handlers-to-custom-hooks*/}

<Wip>

Этот раздел описывает **экспериментальный API, который ещё не был выпущен** в стабильной версии React.

</Wip>

Когда вы начнёте использовать `useChatRoom` в большем количестве компонентов, вы можете захотеть позволить компонентам настраивать его поведение. Например, в настоящее время логика того, что делать при поступлении сообщения, жёстко закодирована внутри хука:

```js {9-11}
export function useChatRoom({ serverUrl, roomId }) {
  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      showNotification('New message: ' + msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl]);
}
```

Допустим, вы хотите переместить эту логику обратно в ваш компонент:

```js {7-9}
export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl,
    onReceiveMessage(msg) {
      showNotification('New message: ' + msg);
    }
  });
  // ...
```

Чтобы это заработало, измените ваш пользовательский хук так, чтобы он принимал `onReceiveMessage` в качестве одной из именованных опций:

```js {1,10,13}
export function useChatRoom({ serverUrl, roomId, onReceiveMessage }) {
  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      onReceiveMessage(msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl, onReceiveMessage]); // ✅ Все зависимости объявлены
}
```

Это будет работать, но есть ещё одно улучшение, которое вы можете сделать, когда ваш пользовательский хук принимает обработчики событий.

Добавление зависимости `onReceiveMessage` не идеально, потому что это приведёт к повторному подключению чата при каждом перерисовке компонента. [Оберните этот обработчик событий в `useEffectEvent`, чтобы удалить его из зависимостей:](/learn/removing-effect-dependencies#wrapping-an-event-handler-from-the-props)

```js {1,4,5,15,18}
import { useEffect, useEffectEvent } from 'react';
// ...

export function useChatRoom({ serverUrl, roomId, onReceiveMessage }) {
  const onMessage = useEffectEvent(onReceiveMessage);

  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      onMessage(msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl]); // ✅ Все зависимости объявлены
}
```

Теперь чат не будет переподключаться при каждой перерисовке компонента `ChatRoom`. Вот полностью рабочий пример передачи обработчика событий в пользовательский хук, с которым вы можете поэкспериментировать:

<Sandpack>

```js src/App.js
import { useState } from 'react';
import ChatRoom from './ChatRoom.js';

export default function App() {
  const [roomId, setRoomId] = useState('general');
  return (
    <>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <hr />
      <ChatRoom
        roomId={roomId}
      />
    </>
  );
}
```

```js src/ChatRoom.js active
import { useState } from 'react';
import { useChatRoom } from './useChatRoom.js';
import { showNotification } from './notifications.js';

export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl,
    onReceiveMessage(msg) {
      showNotification('New message: ' + msg);
    }
  });

  return (
    <>
      <label>
        Server URL:
        <input value={serverUrl} onChange={e => setServerUrl(e.target.value)} />
      </label>
      <h1>Welcome to the {roomId} room!</h1>
    </>
  );
}
```

```js src/useChatRoom.js
import { useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';
import { createConnection } from './chat.js';

export function useChatRoom({ serverUrl, roomId, onReceiveMessage }) {
  const onMessage = useEffectEvent(onReceiveMessage);

  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      onMessage(msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl]);
}
```

```js src/chat.js
export function createConnection({ serverUrl, roomId }) {
  // A real implementation would actually connect to the server
  if (typeof serverUrl !== 'string') {
    throw Error('Expected serverUrl to be a string. Received: ' + serverUrl);
  }
  if (typeof roomId !== 'string') {
    throw Error('Expected roomId to be a string. Received: ' + roomId);
  }
  let intervalId;
  let messageCallback;
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
      clearInterval(intervalId);
      intervalId = setInterval(() => {
        if (messageCallback) {
          if (Math.random() > 0.5) {
            messageCallback('hey')
          } else {
            messageCallback('lol');
          }
        }
      }, 3000);
    },
    disconnect() {
      clearInterval(intervalId);
      messageCallback = null;
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl + '');
    },
    on(event, callback) {
      if (messageCallback) {
        throw Error('Cannot add the handler twice.');
      }
      if (event !== 'message') {
        throw Error('Only "message" event is supported.');
      }
      messageCallback = callback;
    },
  };
}
```

```js src/notifications.js
import Toastify from 'toastify-js';
import 'toastify-js/src/toastify.css';

export function showNotification(message, theme = 'dark') {
  Toastify({
    text: message,
    duration: 2000,
    gravity: 'top',
    position: 'right',
    style: {
      background: theme === 'dark' ? 'black' : 'white',
      color: theme === 'dark' ? 'white' : 'black',
    },
  }).showToast();
}
```

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental",
    "react-scripts": "latest",
    "toastify-js": "1.12.0"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

Обратите внимание, как вам больше не нужно знать, *как* работает `useChatRoom`, чтобы использовать его. Вы можете добавить его в любой другой компонент, передать любые другие опции, и он будет работать одинаково. В этом сила пользовательских хуков.

## Когда использовать пользовательские хуки {/*when-to-use-custom-hooks*/}

Вам не нужно извлекать пользовательский хук для каждого небольшого дублирующегося фрагмента кода. Некоторое дублирование — это нормально. Например, извлечение хука `useFormInput` для обертывания одного вызова `useState`, как было показано ранее, вероятно, излишне.

Однако, когда вы пишете эффект, подумайте, не будет ли понятнее обернуть его в пользовательский хук. [Эффекты вам не понадобятся очень часто,](/learn/you-might-not-need-an-effect) поэтому, если вы пишете один, это означает, что вам нужно «выйти за пределы React», чтобы синхронизироваться с некоторой внешней системой или сделать что-то, для чего в React нет встроенного API. Обертывание его в пользовательский хук позволяет точно передать ваше намерение и то, как данные проходят через него.

Например, рассмотрим компонент `ShippingForm`, который отображает два выпадающих списка: один показывает список городов, а другой — список районов в выбранном городе. Вы можете начать с кода, который выглядит примерно так:

```js {3-16,20-35}
function ShippingForm({ country }) {
  const [cities, setCities] = useState(null);
  // Этот эффект загружает города для страны
  useEffect(() => {
    let ignore = false;
    fetch(`/api/cities?country=${country}`)
      .then(response => response.json())
      .then(json => {
        if (!ignore) {
          setCities(json);
        }
      });
    return () => {
      ignore = true;
    };
  }, [country]);

  const [city, setCity] = useState(null);
  const [areas, setAreas] = useState(null);
  // Этот эффект загружает районы для выбранного города
  useEffect(() => {
    if (city) {
      let ignore = false;
      fetch(`/api/areas?city=${city}`)
        .then(response => response.json())
        .then(json => {
          if (!ignore) {
            setAreas(json);
          }
        });
      return () => {
        ignore = true;
      };
    }
  }, [city]);

  // ...
}
```

Хотя этот код довольно повторяется, [правильно держать эти эффекты отдельно друг от друга.](/learn/removing-effect-dependencies#is-your-effect-doing-several-unrelated-things) Они синхронизируют две разные вещи, поэтому вам не следует объединять их в один эффект. Вместо этого вы можете упростить приведенный выше компонент `ShippingForm`, извлекши общую логику между ними в ваш собственный хук `useData`:

```js {2-18}
function useData(url) {
  const [data, setData] = useState(null);
  useEffect(() => {
    if (url) {
      let ignore = false;
      fetch(url)
        .then(response => response.json())
        .then(json => {
          if (!ignore) {
            setData(json);
          }
        });
      return () => {
        ignore = true;
      };
    }
  }, [url]);
  return data;
}
```

Теперь вы можете заменить оба эффекта в компонентах `ShippingForm` вызовами `useData`:

```js {2,4}
function ShippingForm({ country }) {
  const cities = useData(`/api/cities?country=${country}`);
  const [city, setCity] = useState(null);
  const areas = useData(city ? `/api/areas?city=${city}` : null);
  // ...
}
```

Извлечение пользовательского хука делает поток данных явным. Вы передаете `url` и получаете `data`. «Скрывая» ваш эффект внутри `useData`, вы также предотвращаете добавление [ненужных зависимостей](/learn/removing-effect-dependencies) в него кем-то, кто работает над компонентом `ShippingForm`. Со временем большинство эффектов вашего приложения будут находиться в пользовательских хуках.

<DeepDive>

#### Держите ваши пользовательские хуки сфокусированными на конкретных высокоуровневых сценариях использования {/*keep-your-custom-hooks-focused-on-concrete-high-level-use-cases*/}

Начните с выбора имени для вашего пользовательского хука. Если вам трудно подобрать понятное имя, это может означать, что ваш эффект слишком сильно связан с остальной логикой вашего компонента и еще не готов к извлечению.

В идеале имя вашего пользовательского хука должно быть достаточно ясным, чтобы даже человек, который не часто пишет код, мог хорошо догадаться, что делает ваш пользовательский хук, что он принимает и что возвращает:

* ✅ `useData(url)`
* ✅ `useImpressionLog(eventName, extraData)`
* ✅ `useChatRoom(options)`

Когда вы синхронизируетесь с внешней системой, имя вашего пользовательского хука может быть более техническим и использовать жаргон, специфичный для этой системы. Это хорошо, если это будет понятно человеку, знакомому с этой системой:

* ✅ `useMediaQuery(query)`
* ✅ `useSocket(url)`
* ✅ `useIntersectionObserver(ref, options)`

**Держите пользовательские хуки сфокусированными на конкретных высокоуровневых сценариях использования.** Избегайте создания и использования пользовательских хуков «жизненного цикла», которые действуют как альтернативы и удобные обертки для самого API `useEffect`:

* 🔴 `useMount(fn)`
* 🔴 `useEffectOnce(fn)`
* 🔴 `useUpdateEffect(fn)`

Например, этот хук `useMount` пытается гарантировать, что некоторый код выполняется только «при монтировании»:

```js {4-5,14-15}
function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  // 🔴 Избегайте: использования пользовательских хуков «жизненного цикла»
  useMount(() => {
    const connection = createConnection({ roomId, serverUrl });
    connection.connect();

    post('/analytics/event', { eventName: 'visit_chat' });
  });
  // ...
}

// 🔴 Избегайте: создания пользовательских хуков «жизненного цикла»
function useMount(fn) {
  useEffect(() => {
    fn();
  }, []); // 🔴 React Hook useEffect имеет отсутствующую зависимость: 'fn'
}
```

**Пользовательские хуки «жизненного цикла», такие как `useMount`, плохо вписываются в парадигму React.** Например, в этом примере кода есть ошибка (он не «реагирует» на изменения `roomId` или `serverUrl`), но линтер не предупредит вас об этом, потому что линтер проверяет только прямые вызовы `useEffect`. Он не будет знать о вашем хуке.

Если вы пишете эффект, начните с прямого использования API React:

```js
function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  // ✅ Хорошо: два необработанных эффекта, разделенных по назначению

  useEffect(() => {
    const connection = createConnection({ serverUrl, roomId });
    connection.connect();
    return () => connection.disconnect();
  }, [serverUrl, roomId]);

  useEffect(() => {
    post('/analytics/event', { eventName: 'visit_chat', roomId });
  }, [roomId]);

  // ...
}
```

Затем вы можете (но не обязаны) извлекать пользовательские хуки для различных высокоуровневых сценариев использования:

```js
function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  // ✅ Отлично: пользовательские хуки, названные по их назначению
  useChatRoom({ serverUrl, roomId });
  useImpressionLog('visit_chat', { roomId });
  // ...
}
```

**Хороший пользовательский хук делает вызываемый код более декларативным, ограничивая то, что он делает.** Например, `useChatRoom(options)` может только подключаться к чат-комнате, а `useImpressionLog(eventName, extraData)` может только отправлять лог впечатлений в аналитику. Если API вашего пользовательского хука не ограничивает сценарии использования и является очень абстрактным, в долгосрочной перспективе он, вероятно, принесет больше проблем, чем решит.

</DeepDive>

### Пользовательские хуки помогают мигрировать на лучшие шаблоны {/*custom-hooks-help-you-migrate-to-better-patterns*/}

Эффекты — это ["лазейка"](/learn/escape-hatches): вы используете их, когда вам нужно «выйти за пределы React» и когда нет лучшего встроенного решения для вашего сценария использования. Со временем команда React стремится свести к минимуму количество эффектов в вашем приложении, предоставляя более конкретные решения для более конкретных проблем. Обертывание ваших эффектов в пользовательские хуки упрощает обновление вашего кода, когда эти решения становятся доступными.

Вернемся к этому примеру:

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

```js src/useOnlineStatus.js active
import { useState, useEffect } from 'react';

export function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);
  return isOnline;
}
```

</Sandpack>

В приведенном выше примере `useOnlineStatus` реализован с помощью пары [`useState`](/reference/react/useState) и [`useEffect`.](/reference/react/useEffect) Однако это не лучшее возможное решение. Существует ряд крайних случаев, которые он не учитывает. Например, он предполагает, что при монтировании компонента `isOnline` уже равно `true`, но это может быть неверно, если сеть уже отключена. Вы можете использовать браузерный API [`navigator.onLine`](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/onLine) для проверки этого, но его прямое использование не сработает на сервере для генерации начального HTML. Короче говоря, этот код можно улучшить.

React включает специальный API под названием [`useSyncExternalStore`](/reference/react/useSyncExternalStore), который решает все эти проблемы за вас. Вот ваш хук `useOnlineStatus`, переписанный с использованием этого нового API:

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

```js src/useOnlineStatus.js active
import { useSyncExternalStore } from 'react';

function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}

export function useOnlineStatus() {
  return useSyncExternalStore(
    subscribe,
    () => navigator.onLine, // Как получить значение на клиенте
    () => true // Как получить значение на сервере
  );
}

```

</Sandpack>

Обратите внимание, что вам **не пришлось изменять какие-либо компоненты**, чтобы выполнить эту миграцию:

```js {2,7}
function StatusBar() {
  const isOnline = useOnlineStatus();
  // ...
}

function SaveButton() {
  const isOnline = useOnlineStatus();
  // ...
}
```

Это еще одна причина, по которой обертывание эффектов в пользовательские хуки часто полезно:

1. Вы делаете поток данных к вашим эффектам и от них очень явным.
2. Вы позволяете вашим компонентам сосредоточиться на намерении, а не на точной реализации ваших эффектов.
3. Когда React добавляет новые функции, вы можете удалить эти эффекты, не изменяя ни один из ваших компонентов.

Подобно [системе дизайна,](https://uxdesign.cc/everything-you-need-to-know-about-design-systems-54b109851969) вы можете обнаружить, что полезно начать извлекать общие идиомы из компонентов вашего приложения в пользовательские хуки. Это позволит вашему коду компонентов сосредоточиться на намерении и позволит вам реже писать необработанные эффекты. Многие отличные пользовательские хуки поддерживаются сообществом React.

<DeepDive>

#### Предоставит ли React какое-либо встроенное решение для получения данных? {/*will-react-provide-any-built-in-solution-for-data-fetching*/}

Мы все еще прорабатываем детали, но ожидаем, что в будущем вы будете писать получение данных так:

```js {1,4,6}
import { use } from 'react'; // Пока недоступно!

function ShippingForm({ country }) {
  const cities = use(fetch(`/api/cities?country=${country}`));
  const [city, setCity] = useState(null);
  const areas = city ? use(fetch(`/api/areas?city=${city}`)) : null;
  // ...
```

Если вы используете пользовательские хуки, такие как `useData` выше, в своем приложении, это потребует меньше изменений для миграции на в конечном итоге рекомендуемый подход, чем если бы вы писали необработанные эффекты в каждом компоненте вручную. Однако старый подход все равно будет работать нормально, поэтому, если вы чувствуете себя комфортно, написав необработанные эффекты, вы можете продолжать это делать.

</DeepDive>

### Существует несколько способов сделать это {/*there-is-more-than-one-way-to-do-it*/}

Допустим, вы хотите реализовать анимацию плавного появления *с нуля*, используя браузерный API [`requestAnimationFrame`](https://developer.mozilla.org/ru/docs/Web/API/window/requestAnimationFrame). Вы можете начать с хука `useEffect`, который настраивает цикл анимации. В каждом кадре анимации вы можете изменять прозрачность DOM-узла, который [хранится в ref](/learn/manipulating-the-dom-with-refs), пока она не достигнет `1`. Ваш код может выглядеть так:

<Sandpack>

```js
import { useState, useEffect, useRef } from 'react';

function Welcome() {
  const ref = useRef(null);

  useEffect(() => {
    const duration = 1000;
    const node = ref.current;

    let startTime = performance.now();
    let frameId = null;

    function onFrame(now) {
      const timePassed = now - startTime;
      const progress = Math.min(timePassed / duration, 1);
      onProgress(progress);
      if (progress < 1) {
        // Нам нужно отрисовать еще кадры
        frameId = requestAnimationFrame(onFrame);
      }
    }

    function onProgress(progress) {
      node.style.opacity = progress;
    }

    function start() {
      onProgress(0);
      startTime = performance.now();
      frameId = requestAnimationFrame(onFrame);
    }

    function stop() {
      cancelAnimationFrame(frameId);
      startTime = null;
      frameId = null;
    }

    start();
    return () => stop();
  }, []);

  return (
    <h1 className="welcome" ref={ref}>
      Welcome
    </h1>
  );
}

export default function App() {
  const [show, setShow] = useState(false);
  return (
    <>
      <button onClick={() => setShow(!show)}>
        {show ? 'Remove' : 'Show'}
      </button>
      <hr />
      {show && <Welcome />}
    </>
  );
}
```

```css
label, button { display: block; margin-bottom: 20px; }
html, body { min-height: 300px; }
.welcome {
  opacity: 0;
  color: white;
  padding: 50px;
  text-align: center;
  font-size: 50px;
  background-image: radial-gradient(circle, rgba(63,94,251,1) 0%, rgba(252,70,107,1) 100%);
}
```

</Sandpack>

Чтобы сделать компонент более читаемым, вы можете вынести логику в пользовательский хук `useFadeIn`:

<Sandpack>

```js
import { useState, useEffect, useRef } from 'react';
import { useFadeIn } from './useFadeIn.js';

function Welcome() {
  const ref = useRef(null);

  useFadeIn(ref, 1000);

  return (
    <h1 className="welcome" ref={ref}>
      Welcome
    </h1>
  );
}

export default function App() {
  const [show, setShow] = useState(false);
  return (
    <>
      <button onClick={() => setShow(!show)}>
        {show ? 'Remove' : 'Show'}
      </button>
      <hr />
      {show && <Welcome />}
    </>
  );
}
```

```js src/useFadeIn.js
import { useEffect } from 'react';

export function useFadeIn(ref, duration) {
  useEffect(() => {
    const node = ref.current;

    let startTime = performance.now();
    let frameId = null;

    function onFrame(now) {
      const timePassed = now - startTime;
      const progress = Math.min(timePassed / duration, 1);
      onProgress(progress);
      if (progress < 1) {
        // Нам нужно отрисовать еще кадры
        frameId = requestAnimationFrame(onFrame);
      }
    }

    function onProgress(progress) {
      node.style.opacity = progress;
    }

    function start() {
      onProgress(0);
      startTime = performance.now();
      frameId = requestAnimationFrame(onFrame);
    }

    function stop() {
      cancelAnimationFrame(frameId);
      startTime = null;
      frameId = null;
    }

    start();
    return () => stop();
  }, [ref, duration]);
}
```

```css
label, button { display: block; margin-bottom: 20px; }
html, body { min-height: 300px; }
.welcome {
  opacity: 0;
  color: white;
  padding: 50px;
  text-align: center;
  font-size: 50px;
  background-image: radial-gradient(circle, rgba(63,94,251,1) 0%, rgba(252,70,107,1) 100%);
}
```

</Sandpack>

Вы могли бы оставить код `useFadeIn` как есть, но можно и дальше рефакторить его. Например, вынести логику настройки цикла анимации из `useFadeIn` в пользовательский хук `useAnimationLoop`:

<Sandpack>

```js
import { useState, useEffect, useRef } from 'react';
import { useFadeIn } from './useFadeIn.js';

function Welcome() {
  const ref = useRef(null);

  useFadeIn(ref, 1000);

  return (
    <h1 className="welcome" ref={ref}>
      Welcome
    </h1>
  );
}

export default function App() {
  const [show, setShow] = useState(false);
  return (
    <>
      <button onClick={() => setShow(!show)}>
        {show ? 'Remove' : 'Show'}
      </button>
      <hr />
      {show && <Welcome />}
    </>
  );
}
```

```js src/useFadeIn.js active
import { useState, useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';

export function useFadeIn(ref, duration) {
  const [isRunning, setIsRunning] = useState(true);

  useAnimationLoop(isRunning, (timePassed) => {
    const progress = Math.min(timePassed / duration, 1);
    ref.current.style.opacity = progress;
    if (progress === 1) {
      setIsRunning(false);
    }
  });
}

function useAnimationLoop(isRunning, drawFrame) {
  const onFrame = useEffectEvent(drawFrame);

  useEffect(() => {
    if (!isRunning) {
      return;
    }

    const startTime = performance.now();
    let frameId = null;

    function tick(now) {
      const timePassed = now - startTime;
      onFrame(timePassed);
      frameId = requestAnimationFrame(tick);
    }

    tick();
    return () => cancelAnimationFrame(frameId);
  }, [isRunning]);
}
```

```css
label, button { display: block; margin-bottom: 20px; }
html, body { min-height: 300px; }
.welcome {
  opacity: 0;
  color: white;
  padding: 50px;
  text-align: center;
  font-size: 50px;
  background-image: radial-gradient(circle, rgba(63,94,251,1) 0%, rgba(252,70,107,1) 100%);
}
```

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental",
    "react-scripts": "latest"
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

Однако, вам *не обязательно* было это делать. Как и в случае с обычными функциями, в конечном итоге вы сами решаете, где проводить границы между различными частями вашего кода. Вы также могли бы выбрать совершенно другой подход. Вместо того чтобы хранить логику в `useEffect`, вы могли бы переместить большую часть императивной логики внутрь JavaScript-класса:

<Sandpack>

```js
import { useState, useEffect, useRef } from 'react';
import { useFadeIn } from './useFadeIn.js';

function Welcome() {
  const ref = useRef(null);

  useFadeIn(ref, 1000);

  return (
    <h1 className="welcome" ref={ref}>
      Welcome
    </h1>
  );
}

export default function App() {
  const [show, setShow] = useState(false);
  return (
    <>
      <button onClick={() => setShow(!show)}>
        {show ? 'Remove' : 'Show'}
      </button>
      <hr />
      {show && <Welcome />}
    </>
  );
}
```

```js src/useFadeIn.js active
import { useState, useEffect } from 'react';
import { FadeInAnimation } from './animation.js';

export function useFadeIn(ref, duration) {
  useEffect(() => {
    const animation = new FadeInAnimation(ref.current);
    animation.start(duration);
    return () => {
      animation.stop();
    };
  }, [ref, duration]);
}
```

```js src/animation.js
export class FadeInAnimation {
  constructor(node) {
    this.node = node;
  }
  start(duration) {
    this.duration = duration;
    this.onProgress(0);
    this.startTime = performance.now();
    this.frameId = requestAnimationFrame(() => this.onFrame());
  }
  onFrame() {
    const timePassed = performance.now() - this.startTime;
    const progress = Math.min(timePassed / this.duration, 1);
    this.onProgress(progress);
    if (progress === 1) {
      this.stop();
    } else {
      // Нам нужно отрисовать еще кадры
      this.frameId = requestAnimationFrame(() => this.onFrame());
    }
  }
  onProgress(progress) {
    this.node.style.opacity = progress;
  }
  stop() {
    cancelAnimationFrame(this.frameId);
    this.startTime = null;
    this.frameId = null;
    this.duration = 0;
  }
}
```

```css
label, button { display: block; margin-bottom: 20px; }
html, body { min-height: 300px; }
.welcome {
  opacity: 0;
  color: white;
  padding: 50px;
  text-align: center;
  font-size: 50px;
  background-image: radial-gradient(circle, rgba(63,94,251,1) 0%, rgba(252,70,107,1) 100%);
}
```

</Sandpack>

Хуки `useEffect` позволяют связывать React с внешними системами. Чем больше координации между хуками `useEffect` требуется (например, для цепочки нескольких анимаций), тем разумнее полностью вынести эту логику из `useEffect` и хуков, как в примере выше. Тогда код, который вы вынесли, *становится* «внешней системой». Это позволяет вашим хукам `useEffect` оставаться простыми, поскольку им нужно только отправлять сообщения системе, которую вы переместили за пределы React.

Приведенные выше примеры предполагают, что логику плавного появления нужно писать на JavaScript. Однако, конкретно эта анимация плавного появления является как более простой, так и гораздо более эффективной при реализации с помощью обычной [CSS-анимации:](https://developer.mozilla.org/ru/docs/Web/CSS/CSS_Animations/Using_CSS_animations)

<Sandpack>

```js
import { useState, useEffect, useRef } from 'react';
import './welcome.css';

function Welcome() {
  return (
    <h1 className="welcome">
      Welcome
    </h1>
  );
}

export default function App() {
  const [show, setShow] = useState(false);
  return (
    <>
      <button onClick={() => setShow(!show)}>
        {show ? 'Remove' : 'Show'}
      </button>
      <hr />
      {show && <Welcome />}
    </>
  );
}
```

```css src/styles.css
label, button { display: block; margin-bottom: 20px; }
html, body { min-height: 300px; }
```

```css src/welcome.css active
.welcome {
  color: white;
  padding: 50px;
  text-align: center;
  font-size: 50px;
  background-image: radial-gradient(circle, rgba(63,94,251,1) 0%, rgba(252,70,107,1) 100%);

  animation: fadeIn 1000ms;
}

@keyframes fadeIn {
  0% { opacity: 0; }
  100% { opacity: 1; }
}

```

</Sandpack>

Иногда вам даже не нужен хук!

<Recap>

- Пользовательские хуки позволяют вам совместно использовать логику между компонентами.
- Пользовательские хуки должны называться, начиная с `use`, за которым следует заглавная буква.
- Пользовательские хуки разделяют только логику с состоянием, но не само состояние.
- Вы можете передавать реактивные значения от одного хука к другому, и они будут оставаться актуальными.
- Все хуки перезапускаются каждый раз, когда ваш компонент перерисовывается.
- Код ваших пользовательских хуков должен быть чистым, как и код вашего компонента.
- Оборачивайте обработчики событий, полученные пользовательскими хуками, в хуки событий (`Effect Events`).
- Не создавайте пользовательские хуки вроде `useMount`. Сохраняйте их назначение конкретным.
- Вы сами решаете, как и где выбирать границы вашего кода.

</Recap>

<Challenges>

#### Извлеките хук `useCounter` {/*extract-a-usecounter-hook*/}

Этот компонент использует переменную состояния и хук `useEffect` для отображения числа, которое увеличивается каждую секунду. Извлеките эту логику в пользовательский хук под названием `useCounter`. Ваша цель — добиться того, чтобы реализация компонента `Counter` выглядела точно так:

```js
export default function Counter() {
  const count = useCounter();
  return <h1>Seconds passed: {count}</h1>;
}
```

Вам нужно будет написать свой пользовательский хук в файле `useCounter.js` и импортировать его в файл `App.js`.

<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return <h1>Seconds passed: {count}</h1>;
}
```

```js src/useCounter.js
// Напишите свой пользовательский хук в этом файле!
```

</Sandpack>

<Solution>

Ваш код должен выглядеть так:

<Sandpack>

```js
import { useCounter } from './useCounter.js';

export default function Counter() {
  const count = useCounter();
  return <h1>Seconds passed: {count}</h1>;
}
```

```js src/useCounter.js
import { useState, useEffect } from 'react';

export function useCounter() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return count;
}
```

</Sandpack>

Обратите внимание, что `App.js` больше не нужно импортировать `useState` или `useEffect`.

</Solution>

#### Сделать задержку счетчика настраиваемой {/*make-the-counter-delay-configurable*/}

В этом примере есть переменная состояния `delay`, управляемая ползунком, но её значение не используется. Передайте значение `delay` в ваш пользовательский хук `useCounter` и измените хук `useCounter` так, чтобы он использовал переданный `delay` вместо жестко заданного значения в `1000` мс.

<Sandpack>

```js
import { useState } from 'react';
import { useCounter } from './useCounter.js';

export default function Counter() {
  const [delay, setDelay] = useState(1000);
  const count = useCounter();
  return (
    <>
      <label>
        Tick duration: {delay} ms
        <br />
        <input
          type="range"
          value={delay}
          min="10"
          max="2000"
          onChange={e => setDelay(Number(e.target.value))}
        />
      </label>
      <hr />
      <h1>Ticks: {count}</h1>
    </>
  );
}
```

```js src/useCounter.js
import { useState, useEffect } from 'react';

export function useCounter() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return count;
}
```

</Sandpack>

<Solution>

Передайте `delay` в ваш хук с помощью `useCounter(delay)`. Затем, внутри хука, используйте `delay` вместо жестко заданного значения `1000`. Вам нужно будет добавить `delay` в зависимости вашего эффекта. Это гарантирует, что изменение `delay` сбросит интервал.

<Sandpack>

```js
import { useState } from 'react';
import { useCounter } from './useCounter.js';

export default function Counter() {
  const [delay, setDelay] = useState(1000);
  const count = useCounter(delay);
  return (
    <>
      <label>
        Tick duration: {delay} ms
        <br />
        <input
          type="range"
          value={delay}
          min="10"
          max="2000"
          onChange={e => setDelay(Number(e.target.value))}
        />
      </label>
      <hr />
      <h1>Ticks: {count}</h1>
    </>
  );
}
```

```js src/useCounter.js
import { useState, useEffect } from 'react';

export function useCounter(delay) {
  const [count, setCount] = useState(0);
  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + 1);
    }, delay);
    return () => clearInterval(id);
  }, [delay]);
  return count;
}
```

</Sandpack>

</Solution>

#### Вынести `useInterval` из `useCounter` {/*extract-useinterval-out-of-usecounter*/}

В настоящее время ваш хук `useCounter` делает две вещи. Он настраивает интервал и увеличивает переменную состояния при каждом тике интервала. Выделите логику настройки интервала в отдельный хук под названием `useInterval`. Он должен принимать два аргумента: колбэк `onTick` и `delay`. После этого изменения реализация вашего `useCounter` должна выглядеть так:

```js
export function useCounter(delay) {
  const [count, setCount] = useState(0);
  useInterval(() => {
    setCount(c => c + 1);
  }, delay);
  return count;
}
```

Напишите `useInterval` в файле `useInterval.js` и импортируйте его в файл `useCounter.js`.

<Sandpack>

```js
import { useCounter } from './useCounter.js';

export default function Counter() {
  const count = useCounter(1000);
  return <h1>Seconds passed: {count}</h1>;
}
```

```js src/useCounter.js
import { useState, useEffect } from 'react';

export function useCounter(delay) {
  const [count, setCount] = useState(0);
  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + 1);
    }, delay);
    return () => clearInterval(id);
  }, [delay]);
  return count;
}
```

```js src/useInterval.js
// Write your Hook here!
```

</Sandpack>

<Solution>

Логика внутри `useInterval` должна настраивать и очищать интервал. Ей не нужно делать ничего другого.

<Sandpack>

```js
import { useCounter } from './useCounter.js';

export default function Counter() {
  const count = useCounter(1000);
  return <h1>Seconds passed: {count}</h1>;
}
```

```js src/useCounter.js
import { useState } from 'react';
import { useInterval } from './useInterval.js';

export function useCounter(delay) {
  const [count, setCount] = useState(0);
  useInterval(() => {
    setCount(c => c + 1);
  }, delay);
  return count;
}
```

```js src/useInterval.js active
import { useEffect } from 'react';

export function useInterval(onTick, delay) {
  useEffect(() => {
    const id = setInterval(onTick, delay);
    return () => clearInterval(id);
  }, [onTick, delay]);
}
```

</Sandpack>

Примечание: в этом решении есть небольшая проблема, которую вы решите в следующем задании.

</Solution>

#### Исправить сбрасываемый интервал {/*fix-a-resetting-interval*/}

В этом примере есть *два* отдельных интервала.

Компонент `App` вызывает `useCounter`, который вызывает `useInterval` для обновления счетчика каждую секунду. Но компонент `App` *также* вызывает `useInterval` для случайного обновления цвета фона страницы каждые две секунды.

По какой-то причине колбэк, обновляющий цвет фона страницы, никогда не выполняется. Добавьте несколько логов внутрь `useInterval`:

```js {2,5}
  useEffect(() => {
    console.log('✅ Setting up an interval with delay ', delay)
    const id = setInterval(onTick, delay);
    return () => {
      console.log('❌ Clearing an interval with delay ', delay)
      clearInterval(id);
    };
  }, [onTick, delay]);
```

Соответствуют ли логи тому, что вы ожидаете увидеть? Если некоторые из ваших эффектов, кажется, синхронизируются заново без необходимости, можете ли вы догадаться, какая зависимость вызывает это? Есть ли способ [удалить эту зависимость](/learn/removing-effect-dependencies) из вашего эффекта?

После исправления проблемы вы должны ожидать, что цвет фона страницы будет обновляться каждые две секунды.

<Hint>

Похоже, ваш хук `useInterval` принимает в качестве аргумента обработчик событий. Можете ли вы придумать способ обернуть этот обработчик событий так, чтобы он не нуждался в зависимости для вашего эффекта?

</Hint>

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental",
    "react-scripts": "latest"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js
import { useCounter } from './useCounter.js';
import { useInterval } from './useInterval.js';

export default function Counter() {
  const count = useCounter(1000);

  useInterval(() => {
    const randomColor = `hsla(${Math.random() * 360}, 100%, 50%, 0.2)`;
    document.body.style.backgroundColor = randomColor;
  }, 2000);

  return <h1>Seconds passed: {count}</h1>;
}
```

```js src/useCounter.js
import { useState } from 'react';
import { useInterval } from './useInterval.js';

export function useCounter(delay) {
  const [count, setCount] = useState(0);
  useInterval(() => {
    setCount(c => c + 1);
  }, delay);
  return count;
}
```

```js src/useInterval.js
import { useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';

export function useInterval(onTick, delay) {
  useEffect(() => {
    const id = setInterval(onTick, delay);
    return () => {
      clearInterval(id);
    };
  }, [onTick, delay]);
}
```

</Sandpack>

<Solution>

Внутри `useInterval` оберните колбэк тика в "эффект-событие", как вы делали [ранее на этой странице.](/learn/reusing-logic-with-custom-hooks#passing-event-handlers-to-custom-hooks)

Это позволит вам опустить `onTick` из зависимостей вашего эффекта. Эффект не будет повторно синхронизироваться при каждом повторном рендеринге компонента, поэтому интервал изменения цвета фона страницы не будет сбрасываться каждую секунду до того, как он успеет сработать.

С этим изменением оба интервала работают как ожидалось и не мешают друг другу:

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental",
    "react-scripts": "latest"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```


```js
import { useCounter } from './useCounter.js';
import { useInterval } from './useInterval.js';

export default function Counter() {
  const count = useCounter(1000);

  useInterval(() => {
    const randomColor = `hsla(${Math.random() * 360}, 100%, 50%, 0.2)`;
    document.body.style.backgroundColor = randomColor;
  }, 2000);

  return <h1>Seconds passed: {count}</h1>;
}
```

```js src/useCounter.js
import { useState } from 'react';
import { useInterval } from './useInterval.js';

export function useCounter(delay) {
  const [count, setCount] = useState(0);
  useInterval(() => {
    setCount(c => c + 1);
  }, delay);
  return count;
}
```

```js src/useInterval.js active
import { useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';

export function useInterval(callback, delay) {
  const onTick = useEffectEvent(callback);
  useEffect(() => {
    const id = setInterval(onTick, delay);
    return () => clearInterval(id);
  }, [delay]);
}
```

</Sandpack>

</Solution>

#### Реализуйте эффект задержки движения {/*implement-a-staggering-movement*/}

В этом примере хук `usePointerPosition()` отслеживает текущую позицию указателя. Попробуйте переместить курсор или палец по области предварительного просмотра, и вы увидите, как красная точка следует за вашим движением. Её позиция сохраняется в переменной `pos1`.

На самом деле отрисовывается пять (!) разных красных точек. Вы их не видите, потому что в данный момент все они отображаются в одной и той же позиции. Это то, что вам нужно исправить. Вместо этого вы хотите реализовать «эффект задержки движения»: каждая точка должна «следовать» за траекторией предыдущей точки. Например, если вы быстро переместите курсор, первая точка должна немедленно следовать за ним, вторая точка должна следовать за первой с небольшой задержкой, третья точка должна следовать за второй, и так далее.

Вам нужно реализовать пользовательский хук `useDelayedValue`. Его текущая реализация возвращает предоставленное ему значение `value`. Вместо этого вы хотите вернуть значение, которое было `delay` миллисекунд назад. Вам может понадобиться некоторое состояние и эффект для этого.

После реализации `useDelayedValue` вы должны увидеть, как точки движутся одна за другой.

<Hint>

Вам нужно будет сохранить `delayedValue` как переменную состояния внутри вашего пользовательского хука. Когда `value` изменится, вы захотите запустить эффект. Этот эффект должен обновить `delayedValue` через `delay` миллисекунд. Вам может быть полезно вызвать `setTimeout`.

Нужна ли этому эффекту очистка? Почему да или почему нет?

</Hint>

<Sandpack>

```js
import { usePointerPosition } from './usePointerPosition.js';

function useDelayedValue(value, delay) {
  // TODO: Implement this Hook
  return value;
}

export default function Canvas() {
  const pos1 = usePointerPosition();
  const pos2 = useDelayedValue(pos1, 100);
  const pos3 = useDelayedValue(pos2, 200);
  const pos4 = useDelayedValue(pos3, 100);
  const pos5 = useDelayedValue(pos3, 50);
  return (
    <>
      <Dot position={pos1} opacity={1} />
      <Dot position={pos2} opacity={0.8} />
      <Dot position={pos3} opacity={0.6} />
      <Dot position={pos4} opacity={0.4} />
      <Dot position={pos5} opacity={0.2} />
    </>
  );
}

function Dot({ position, opacity }) {
  return (
    <div style={{
      position: 'absolute',
      backgroundColor: 'pink',
      borderRadius: '50%',
      opacity,
      transform: `translate(${position.x}px, ${position.y}px)`,
      pointerEvents: 'none',
      left: -20,
      top: -20,
      width: 40,
      height: 40,
    }} />
  );
}
```

```js src/usePointerPosition.js
import { useState, useEffect } from 'react';

export function usePointerPosition() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  useEffect(() => {
    function handleMove(e) {
      setPosition({ x: e.clientX, y: e.clientY });
    }
    window.addEventListener('pointermove', handleMove);
    return () => window.removeEventListener('pointermove', handleMove);
  }, []);
  return position;
}
```

```css
body { min-height: 300px; }
```

</Sandpack>

<Solution>

Вот рабочая версия. Вы сохраняете `delayedValue` как переменную состояния. Когда `value` обновляется, ваш эффект планирует тайм-аут для обновления `delayedValue`. Вот почему `delayedValue` всегда «отстает» от фактического `value`.

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { usePointerPosition } from './usePointerPosition.js';

function useDelayedValue(value, delay) {
  const [delayedValue, setDelayedValue] = useState(value);

  useEffect(() => {
    setTimeout(() => {
      setDelayedValue(value);
    }, delay);
  }, [value, delay]);

  return delayedValue;
}

export default function Canvas() {
  const pos1 = usePointerPosition();
  const pos2 = useDelayedValue(pos1, 100);
  const pos3 = useDelayedValue(pos2, 200);
  const pos4 = useDelayedValue(pos3, 100);
  const pos5 = useDelayedValue(pos3, 50);
  return (
    <>
      <Dot position={pos1} opacity={1} />
      <Dot position={pos2} opacity={0.8} />
      <Dot position={pos3} opacity={0.6} />
      <Dot position={pos4} opacity={0.4} />
      <Dot position={pos5} opacity={0.2} />
    </>
  );
}

function Dot({ position, opacity }) {
  return (
    <div style={{
      position: 'absolute',
      backgroundColor: 'pink',
      borderRadius: '50%',
      opacity,
      transform: `translate(${position.x}px, ${position.y}px)`,
      pointerEvents: 'none',
      left: -20,
      top: -20,
      width: 40,
      height: 40,
    }} />
  );
}
```

```js src/usePointerPosition.js
import { useState, useEffect } from 'react';

export function usePointerPosition() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  useEffect(() => {
    function handleMove(e) {
      setPosition({ x: e.clientX, y: e.clientY });
    }
    window.addEventListener('pointermove', handleMove);
    return () => window.removeEventListener('pointermove', handleMove);
  }, []);
  return position;
}
```

```css
body { min-height: 300px; }
```

</Sandpack>

Обратите внимание, что этому эффекту *не* требуется очистка. Если бы вы вызвали `clearTimeout` в функции очистки, то каждый раз, когда `value` изменяется, он сбрасывал бы уже запланированный тайм-аут. Чтобы движение было непрерывным, вы хотите, чтобы все тайм-ауты срабатывали.

</Solution>

</Challenges>