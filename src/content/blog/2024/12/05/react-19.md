---
title: "React v19"
author: The React Team
date: 2024/12/05
description: React 19 теперь доступен в npm! В этой статье мы рассмотрим новые возможности React 19 и то, как вы можете их использовать.
---

5 декабря 2024 г. [The React Team](/community/team)

---
<Note>

### React 19 теперь стабилен! {/*react-19-is-now-stable*/}

Дополнения с момента первоначальной публикации этой статьи с React 19 RC в апреле:

- **Предварительная подготовка для отложенных деревьев**: см. [Улучшения Suspense](/blog/2024/04/25/react-19-upgrade-guide#improvements-to-suspense).
- **Статические API React DOM**: см. [Новые статические API React DOM](#new-react-dom-static-apis).

_Дата этой публикации была обновлена, чтобы отразить дату стабильного выпуска._

</Note>

<Intro>

React v19 теперь доступен в npm!

</Intro>

В нашем [Руководстве по обновлению до React 19](/blog/2024/04/25/react-19-upgrade-guide) мы поделились пошаговыми инструкциями по обновлению вашего приложения до React 19. В этой статье мы рассмотрим новые возможности React 19 и то, как вы можете их использовать.

- [Что нового в React 19](#whats-new-in-react-19)
- [Улучшения в React 19](#improvements-in-react-19)
- [Как обновиться](#how-to-upgrade)

Список критических изменений см. в [Руководстве по обновлению](/blog/2024/04/25/react-19-upgrade-guide).

---

## Что нового в React 19 {/*whats-new-in-react-19*/}

### Actions {/*actions*/}

Распространенный сценарий использования в приложениях React — это изменение данных с последующим обновлением состояния в ответ. Например, когда пользователь отправляет форму для смены имени, вы делаете запрос к API, а затем обрабатываете ответ. Раньше для этого приходилось вручную обрабатывать состояния ожидания, ошибки, оптимистичные обновления и последовательные запросы.

Например, вы могли обрабатывать состояние ожидания и ошибки с помощью `useState`:

```js
// До Actions
function UpdateName({}) {
  const [name, setName] = useState("");
  const [error, setError] = useState(null);
  const [isPending, setIsPending] = useState(false);

  const handleSubmit = async () => {
    setIsPending(true);
    const error = await updateName(name);
    setIsPending(false);
    if (error) {
      setError(error);
      return;
    }
    redirect("/path");
  };

  return (
    <div>
      <input value={name} onChange={(event) => setName(event.target.value)} />
      <button onClick={handleSubmit} disabled={isPending}>
        Update
      </button>
      {error && <p>{error}</p>}
    </div>
  );
}
```

В React 19 мы добавили поддержку использования асинхронных функций в переходах для автоматической обработки состояний ожидания, ошибок, форм и оптимистичных обновлений.

Например, вы можете использовать `useTransition` для автоматической обработки состояния ожидания:

```js
// Использование состояния ожидания из Actions
function UpdateName({}) {
  const [name, setName] = useState("");
  const [error, setError] = useState(null);
  const [isPending, startTransition] = useTransition();

  const handleSubmit = () => {
    startTransition(async () => {
      const error = await updateName(name);
      if (error) {
        setError(error);
        return;
      }
      redirect("/path");
    })
  };

  return (
    <div>
      <input value={name} onChange={(event) => setName(event.target.value)} />
      <button onClick={handleSubmit} disabled={isPending}>
        Update
      </button>
      {error && <p>{error}</p>}
    </div>
  );
}
```

Асинхронный переход немедленно установит `isPending` в `true`, выполнит асинхронный запрос(ы) и установит `isPending` в `false` после всех обновлений состояния. Это позволяет сохранить текущий интерфейс отзывчивым и интерактивным во время изменения данных.

<Note>

#### По соглашению, функции, использующие асинхронные переходы, называются "Actions". {/*by-convention-functions-that-use-async-transitions-are-called-actions*/}

Actions автоматически управляют отправкой данных для вас:

- **Состояние ожидания**: Actions предоставляют состояние ожидания, которое начинается в начале запроса и автоматически сбрасывается при фиксации последнего обновления состояния.
- **Оптимистичные обновления**: Actions поддерживают новый хук [`useOptimistic`](#new-hook-optimistic-updates) для мгновенной обратной связи пользователям во время отправки запросов.
- **Обработка ошибок**: Actions обеспечивают обработку ошибок, позволяя отображать Error Boundaries при сбое запроса и автоматически откатывать оптимистичные обновления к исходному значению.
- **Формы**: Элементы `<form>` теперь поддерживают передачу функций в пропсы `action` и `formAction`. Передача функций в пропсы `action` использует Actions по умолчанию и автоматически сбрасывает форму после отправки.

</Note>

Основываясь на Actions, React 19 представляет [`useOptimistic`](#new-hook-optimistic-updates) для управления оптимистичными обновлениями и новый хук [`React.useActionState`](#new-hook-useactionstate) для обработки распространенных случаев использования Actions. В `react-dom` мы добавляем [`<form>` Actions](#form-actions) для автоматического управления формами и [`useFormStatus`](#new-hook-useformstatus) для поддержки распространенных случаев использования Actions в формах.

В React 19 приведенный выше пример можно упростить следующим образом:

```js
// Использование <form> Actions и useActionState
function ChangeName({ name, setName }) {
  const [error, submitAction, isPending] = useActionState(
    async (previousState, formData) => {
      const error = await updateName(formData.get("name"));
      if (error) {
        return error;
      }
      redirect("/path");
      return null;
    },
    null,
  );

  return (
    <form action={submitAction}>
      <input type="text" name="name" />
      <button type="submit" disabled={isPending}>Update</button>
      {error && <p>{error}</p>}
    </form>
  );
}
```

В следующем разделе мы подробно рассмотрим каждую из новых функций Actions в React 19.

### Новый хук: `useActionState` {/*new-hook-useactionstate*/}

Чтобы упростить распространенные случаи использования Actions, мы добавили новый хук под названием `useActionState`:

```js
const [error, submitAction, isPending] = useActionState(
  async (previousState, newName) => {
    const error = await updateName(newName);
    if (error) {
      // Вы можете вернуть любой результат действия.
      // Здесь мы возвращаем только ошибку.
      return error;
    }

    // обработка успеха
    return null;
  },
  null,
);
```

`useActionState` принимает функцию ( "Action") и возвращает обернутую Action для вызова. Это работает, потому что Actions компонуются. Когда вызывается обернутая Action, `useActionState` возвращает последний результат Action в виде `data`, а состояние ожидания Action — в виде `pending`.

<Note>

`React.useActionState` ранее назывался `ReactDOM.useFormState` в Canary-релизах, но мы переименовали его и объявили `useFormState` устаревшим.

См. [#28491](https://github.com/facebook/react/pull/28491) для получения дополнительной информации.

</Note>

Дополнительную информацию см. в документации по [`useActionState`](/reference/react/useActionState).

### React DOM: `<form>` Actions {/*form-actions*/}

Actions также интегрированы с новыми функциями `<form>` в React 19 для `react-dom`. Мы добавили поддержку передачи функций в качестве пропсов `action` и `formAction` элементов `<form>`, `<input>` и `<button>` для автоматической отправки форм с помощью Actions:

```js [[1,1,"actionFunction"]]
<form action={actionFunction}>
```

Когда Action `<form>` успешно выполняется, React автоматически сбрасывает форму для неуправляемых компонентов. Если вам нужно сбросить `<form>` вручную, вы можете вызвать новый API React DOM `requestFormReset`.

Дополнительную информацию см. в документации `react-dom` для [`<form>`](/reference/react-dom/components/form), [`<input>`](/reference/react-dom/components/input) и `<button>`.

### React DOM: Новый хук: `useFormStatus` {/*new-hook-useformstatus*/}

В дизайн-системах часто приходится писать компоненты дизайна, которым нужен доступ к информации о `<form>`, в котором они находятся, без необходимости передавать пропсы вниз по дереву компонентов. Это можно сделать через Context, но чтобы упростить распространенный случай, мы добавили новый хук `useFormStatus`:

```js [[1, 4, "pending"], [1, 5, "pending"]]
import {useFormStatus} from 'react-dom';

function DesignButton() {
  const {pending} = useFormStatus();
  return <button type="submit" disabled={pending} />
}
```

`useFormStatus` считывает статус родительской формы `<form>`, как если бы форма была провайдером Context.

Дополнительную информацию см. в документации `react-dom` для [`useFormStatus`](/reference/react-dom/hooks/useFormStatus).

### Новый хук: `useOptimistic` {/*new-hook-optimistic-updates*/}

Еще один распространенный шаблон пользовательского интерфейса при выполнении изменения данных — это оптимистичное отображение конечного состояния во время выполнения асинхронного запроса. В React 19 мы добавляем новый хук под названием `useOptimistic`, чтобы упростить это:

```js {2,6,13,19}
function ChangeName({currentName, onUpdateName}) {
  const [optimisticName, setOptimisticName] = useOptimistic(currentName);

  const submitAction = async formData => {
    const newName = formData.get("name");
    setOptimisticName(newName);
    const updatedName = await updateName(newName);
    onUpdateName(updatedName);
  };

  return (
    <form action={submitAction}>
      <p>Your name is: {optimisticName}</p>
      <p>
        <label>Change Name:</label>
        <input
          type="text"
          name="name"
          disabled={currentName !== optimisticName}
        />
      </p>
    </form>
  );
}
```

Хук `useOptimistic` немедленно отобразит `optimisticName` во время выполнения запроса `updateName`. Когда обновление завершится или произойдет ошибка, React автоматически вернется к значению `currentName`.

Дополнительную информацию см. в документации по [`useOptimistic`](/reference/react/useOptimistic).

### Новый API: `use` {/*new-feature-use*/}

В React 19 мы представляем новый API для чтения ресурсов в процессе рендеринга: `use`.

Например, вы можете прочитать промис с помощью `use`, и React приостановит выполнение до тех пор, пока промис не будет разрешен:

```js {1,5}
import {use} from 'react';

function Comments({commentsPromise}) {
  // `use` приостановит выполнение до тех пор, пока промис не будет разрешен.
  const comments = use(commentsPromise);
  return comments.map(comment => <p key={comment.id}>{comment}</p>);
}

function Page({commentsPromise}) {
  // Когда `use` приостановит выполнение в Comments,
  // будет показана эта граница Suspense.
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Comments commentsPromise={commentsPromise} />
    </Suspense>
  )
}
```

<Note>

#### `use` не поддерживает промисы, созданные в процессе рендеринга. {/*use-does-not-support-promises-created-in-render*/}

Если вы попытаетесь передать промис, созданный в процессе рендеринга, в `use`, React выдаст предупреждение:

<ConsoleBlockMulti>

<ConsoleLogLine level="error">

Компонент был приостановлен из-за некэшированного промиса. Создание промисов внутри клиентского компонента или хука пока не поддерживается, за исключением случаев использования библиотеки или фреймворка, совместимых с Suspense.

</ConsoleLogLine>

</ConsoleBlockMulti>

Чтобы исправить это, вам нужно передать промис из библиотеки или фреймворка с поддержкой Suspense, который поддерживает кэширование промисов. В будущем мы планируем выпустить функции, которые упростят кэширование промисов в процессе рендеринга.

</Note>

Вы также можете читать контекст с помощью `use`, что позволяет условно считывать контекст, например, после ранних возвратов:

```js {1,11}
import {use} from 'react';
import ThemeContext from './ThemeContext'

function Heading({children}) {
  if (children == null) {
    return null;
  }

  // Это не сработало бы с useContext
  // из-за раннего возврата.
  const theme = use(ThemeContext);
  return (
    <h1 style={{color: theme.color}}>
      {children}
    </h1>
  );
}
```

API `use` можно вызывать только в процессе рендеринга, аналогично хукам. В отличие от хуков, `use` можно вызывать условно. В будущем мы планируем поддерживать больше способов потребления ресурсов в процессе рендеринга с помощью `use`.

Дополнительную информацию см. в документации по [`use`](/reference/react/use).

## Новые статические API React DOM {/*new-react-dom-static-apis*/}

Мы добавили два новых API в `react-dom/static` для генерации статических сайтов:
- [`prerender`](/reference/react-dom/static/prerender)
- [`prerenderToNodeStream`](/reference/react-dom/static/prerenderToNodeStream)

Эти новые API улучшают `renderToString`, ожидая загрузки данных для генерации статического HTML. Они разработаны для работы в потоковых средах, таких как Node.js Streams и Web Streams. Например, в среде Web Stream вы можете предварительно отрендерить React-дерево в статический HTML с помощью `prerender`:

```js
import { prerender } from 'react-dom/static';

async function handler(request) {
  const {prelude} = await prerender(<App />, {
    bootstrapScripts: ['/main.js']
  });
  return new Response(prelude, {
    headers: { 'content-type': 'text/html' },
  });
}
```

API `prerender` будут ждать загрузки всех данных перед возвратом потока статического HTML. Потоки можно преобразовать в строки или отправить с потоковым ответом. Они не поддерживают потоковую передачу контента по мере его загрузки, что поддерживается существующими [API серверного рендеринга React DOM](/reference/react-dom/server).

Для получения дополнительной информации см. [Статические API React DOM](/reference/react-dom/static).

## Компоненты Сервера React {/*react-server-components*/}

### Компоненты Сервера {/*server-components*/}

Компоненты Сервера — это новая опция, которая позволяет рендерить компоненты заранее, до сборки, в среде, отдельной от вашего клиентского приложения или SSR-сервера. Эта отдельная среда — «сервер» в Компонентах Сервера React. Компоненты Сервера могут выполняться один раз во время сборки на вашем CI-сервере или могут выполняться для каждого запроса с использованием веб-сервера.

React 19 включает все функции Компонентов Сервера, которые были доступны в Canary-канале. Это означает, что библиотеки, поставляемые с Компонентами Сервера, теперь могут ориентироваться на React 19 как на `peer dependency` с условием экспорта `react-server` ([export condition](https://github.com/reactjs/rfcs/blob/main/text/0227-server-module-conventions.md#react-server-conditional-exports)) для использования в фреймворках, поддерживающих [Полную архитектуру React](/learn/start-a-new-react-project#which-features-make-up-the-react-teams-full-stack-architecture-vision).


<Note>

#### Как реализовать поддержку Компонентов Сервера? {/*how-do-i-build-support-for-server-components*/}

Хотя Компоненты Сервера React в React 19 стабильны и не будут ломаться между минорными версиями, базовые API, используемые для реализации бандлера или фреймворка Компонентов Сервера React, не следуют semver и могут ломаться между минорными версиями в React 19.x.

Для поддержки Компонентов Сервера React в качестве бандлера или фреймворка мы рекомендуем зафиксировать определенную версию React или использовать Canary-релиз. Мы продолжим работать с бандлерами и фреймворками над стабилизацией API, используемых для реализации Компонентов Сервера React в будущем.

</Note>


Дополнительную информацию см. в документации по [Компонентам Сервера React](/reference/rsc/server-components).

### Серверные Действия {/*server-actions*/}

Серверные Действия позволяют клиентским компонентам вызывать асинхронные функции, выполняемые на сервере.

Когда Серверное Действие определяется директивой `"use server"`, ваш фреймворк автоматически создаст ссылку на серверную функцию и передаст эту ссылку клиентскому компоненту. Когда эта функция вызывается на клиенте, React отправит запрос на сервер для выполнения функции и вернет результат.

<Note>

#### Директивы для Компонентов Сервера не существует. {/*there-is-no-directive-for-server-components*/}

Распространенное заблуждение заключается в том, что Компоненты Сервера обозначаются директивой `"use server"`, но для Компонентов Сервера директивы не существует. Директива `"use server"` используется для Серверных Действий.

Дополнительную информацию см. в документации по [Директивам](/reference/rsc/directives).

</Note>

Серверные Действия могут быть созданы в Компонентах Сервера и переданы в качестве пропсов клиентским компонентам, или они могут быть импортированы и использованы в клиентских компонентах.

Дополнительную информацию см. в документации по [Серверным Действиям React](/reference/rsc/server-actions).

## Улучшения в React 19 {/*improvements-in-react-19*/}

### `ref` как проп {/*ref-as-a-prop*/}

Начиная с React 19, вы можете получать `ref` как проп для функциональных компонентов:

```js [[1, 1, "ref"], [1, 2, "ref", 45], [1, 6, "ref", 14]]
function MyInput({placeholder, ref}) {
  return <input placeholder={placeholder} ref={ref} />
}

//...
<MyInput ref={ref} />
```

Новые функциональные компоненты больше не будут нуждаться в `forwardRef`, и мы опубликуем codemod для автоматического обновления ваших компонентов для использования нового пропа `ref`. В будущих версиях мы объявим `forwardRef` устаревшим и удалим его.

<Note>

`refs`, переданные в классы, не передаются как пропсы, поскольку они ссылаются на экземпляр компонента.

</Note>

### Различия для ошибок гидратации {/*diffs-for-hydration-errors*/}

Мы также улучшили отчётность об ошибках гидратации в `react-dom`. Например, вместо вывода множества ошибок в режиме разработки без какой-либо информации о несоответствии:

<ConsoleBlockMulti>

<ConsoleLogLine level="error">

Warning: Text content did not match. Server: "Server" Client: "Client"
{'  '}at span
{'  '}at App

</ConsoleLogLine>

<ConsoleLogLine level="error">

Warning: An error occurred during hydration. The server HTML was replaced with client content in \<div\>.

</ConsoleLogLine>

<ConsoleLogLine level="error">

Warning: Text content did not match. Server: "Server" Client: "Client"
{'  '}at span
{'  '}at App

</ConsoleLogLine>

<ConsoleLogLine level="error">

Warning: An error occurred during hydration. The server HTML was replaced with client content in \<div\>.

</ConsoleLogLine>

<ConsoleLogLine level="error">

Uncaught Error: Text content does not match server-rendered HTML.
{'  '}at checkForUnmatchedText
{'  '}...

</ConsoleLogLine>

</ConsoleBlockMulti>

Теперь мы выводим одно сообщение с различиями несоответствия:


<ConsoleBlockMulti>

<ConsoleLogLine level="error">

Uncaught Error: Hydration failed because the server rendered HTML didn't match the client. As a result this tree will be regenerated on the client. This can happen if an SSR-ed Client Component used:{'\n'}
\- A server/client branch `if (typeof window !== 'undefined')`.
\- Variable input such as `Date.now()` or `Math.random()` which changes each time it's called.
\- Date formatting in a user's locale which doesn't match the server.
\- External changing data without sending a snapshot of it along with the HTML.
\- Invalid HTML tag nesting.{'\n'}
It can also happen if the client has a browser extension installed which messes with the HTML before React loaded.{'\n'}
https://react.dev/link/hydration-mismatch {'\n'}
{'  '}\<App\>
{'    '}\<span\>
{'+    '}Client
{'-    '}Server{'\n'}
{'  '}at throwOnHydrationMismatch
{'  '}...

</ConsoleLogLine>

</ConsoleBlockMulti>

### `<Context>` как провайдер {/*context-as-a-provider*/}

В React 19 вы можете рендерить `<Context>` как провайдер вместо `<Context.Provider>`:


```js {5,7}
const ThemeContext = createContext('');

function App({children}) {
  return (
    <ThemeContext value="dark">
      {children}
    </ThemeContext>
  );  
}
```

Новые провайдеры Context могут использовать `<Context>`, и мы опубликуем codemod для преобразования существующих провайдеров. В будущих версиях мы объявим `<Context.Provider>` устаревшим.

### Функции очистки для `ref` {/*cleanup-functions-for-refs*/}

Теперь мы поддерживаем возврат функции очистки из колбэков `ref`:

```js {7-9}
<input
  ref={(ref) => {
    // ref создан

    // НОВОЕ: вернуть функцию очистки для сброса
    // ref, когда элемент удалён из DOM.
    return () => {
      // очистка ref
    };
  }}
/>
```

Когда компонент размонтируется, React вызовет функцию очистки, возвращённую из колбэка `ref`. Это работает для DOM-refs, refs к классовым компонентам и `useImperativeHandle`.

<Note>

Ранее React вызывал `ref`-функции со значением `null` при размонтировании компонента. Если ваш `ref` возвращает функцию очистки, React теперь пропустит этот шаг.

В будущих версиях мы объявим устаревшим вызов refs со значением `null` при размонтировании компонентов.

</Note>

Из-за введения функций очистки для `ref`, возврат чего-либо другого из колбэка `ref` теперь будет отклоняться TypeScript. Исправление обычно заключается в прекращении использования неявных возвратов, например:

```diff [[1, 1, "("], [1, 1, ")"], [2, 2, "{", 15], [2, 2, "}", 1]]
- <div ref={current => (instance = current)} />
+ <div ref={current => {instance = current}} />
```

Исходный код возвращал экземпляр `HTMLDivElement`, и TypeScript не знал, было ли это _предназначено_ быть функцией очистки, или вы не хотели возвращать функцию очистки.

Вы можете преобразовать этот шаблон с помощью [`no-implicit-ref-callback-return`](https://github.com/eps1lon/types-react-codemod/#no-implicit-ref-callback-return).

### Начальное значение `useDeferredValue` {/*use-deferred-value-initial-value*/}

Мы добавили опцию `initialValue` в `useDeferredValue`:

```js [[1, 1, "deferredValue"], [1, 4, "deferredValue"], [2, 4, "''"]]
function Search({deferredValue}) {
  // При начальном рендере значение равно ''.
  // Затем планируется повторный рендер с deferredValue.
  const value = useDeferredValue(deferredValue, '');
  
  return (
    <Results query={value} />
  );
}
````

Когда предоставлено <CodeStep step={2}>initialValue</CodeStep>, `useDeferredValue` вернёт его как `value` для начального рендеринга компонента и запланирует повторный рендеринг в фоновом режиме с возвращаемым <CodeStep step={1}>deferredValue</CodeStep>.

Подробнее см. [`useDeferredValue`](/reference/react/useDeferredValue).

### Поддержка метаданных документа {/*support-for-metadata-tags*/}

В HTML теги метаданных документа, такие как `<title>`, `<link>` и `<meta>`, зарезервированы для размещения в секции `<head>` документа. В React компонент, который определяет, какие метаданные подходят для приложения, может находиться очень далеко от места, где вы рендерите `<head>`, или React вообще не рендерит `<head>`. В прошлом эти элементы приходилось вставлять вручную в эффекте или с помощью библиотек, таких как [`react-helmet`](https://github.com/nfl/react-helmet), и требовалась тщательная обработка при серверном рендеринге приложения React.

В React 19 мы добавляем нативную поддержку рендеринга тегов метаданных документа в компонентах:

```js {5-8}
function BlogPost({post}) {
  return (
    <article>
      <h1>{post.title}</h1>
      <title>{post.title}</title>
      <meta name="author" content="Josh" />
      <link rel="author" href="https://twitter.com/joshcstory/" />
      <meta name="keywords" content={post.keywords} />
      <p>
        Eee equals em-see-squared...
      </p>
    </article>
  );
}
```

Когда React отрендерит этот компонент, он увидит теги `<title>`, `<link>` и `<meta>` и автоматически переместит их в секцию `<head>` документа. Поддерживая эти теги метаданных нативно, мы можем обеспечить их работу с приложениями, работающими только на клиенте, потоковой SSR и Server Components.

<Note>

#### Вам всё ещё может понадобиться библиотека метаданных {/*you-may-still-want-a-metadata-library*/}

Для простых случаев рендеринг метаданных документа в виде тегов может быть подходящим, но библиотеки могут предлагать более мощные функции, такие как переопределение общих метаданных специфическими метаданными на основе текущего маршрута. Эти функции облегчают фреймворкам и библиотекам, таким как [`react-helmet`](https://github.com/nfl/react-helmet), поддержку тегов метаданных, а не их замену.

</Note>

Для получения дополнительной информации см. документацию по [`<title>`](/reference/react-dom/components/title), [`<link>`](/reference/react-dom/components/link) и [`<meta>`](/reference/react-dom/components/meta).

### Поддержка таблиц стилей {/*support-for-stylesheets*/}

Таблицы стилей, как внешние (`<link rel="stylesheet" href="...">`), так и встроенные (`<style>...</style>`), требуют тщательного позиционирования в DOM из-за правил приоритета стилей. Создание функциональности таблиц стилей, допускающей композицию в компонентах, сложно, поэтому пользователи часто либо загружают все свои стили далеко от компонентов, которые могут от них зависеть, либо используют библиотеку стилей, которая инкапсулирует эту сложность.

В React 19 мы решаем эту сложность и обеспечиваем более глубокую интеграцию с Concurrent Rendering на клиенте и Streaming Rendering на сервере благодаря встроенной поддержке таблиц стилей. Если вы укажете React `precedence` вашей таблицы стилей, он будет управлять порядком вставки таблицы стилей в DOM и гарантировать, что таблица стилей (если она внешняя) будет загружена перед отображением контента, который зависит от этих правил стилей.

```js {4,5,17}
function ComponentOne() {
  return (
    <Suspense fallback="loading...">
      <link rel="stylesheet" href="foo" precedence="default" />
      <link rel="stylesheet" href="bar" precedence="high" />
      <article class="foo-class bar-class">
        {...}
      </article>
    </Suspense>
  )
}

function ComponentTwo() {
  return (
    <div>
      <p>{...}</p>
      <link rel="stylesheet" href="baz" precedence="default" />  <-- будет вставлен между foo и bar
    </div>
  )
}
```

Во время серверного рендеринга React включит таблицу стилей в `<head>`, что гарантирует, что браузер не будет отрисовывать до её загрузки. Если таблица стилей обнаружена поздно, после того как мы уже начали потоковую передачу, React гарантирует, что таблица стилей будет вставлена в `<head>` на клиенте перед отображением контента границы Suspense, которая зависит от этой таблицы стилей.

Во время клиентского рендеринга React будет ждать загрузки вновь отрисованных таблиц стилей перед фиксацией рендеринга. Если вы рендерите этот компонент из нескольких мест в вашем приложении, React включит таблицу стилей только один раз в DOM:

```js {5}
function App() {
  return <>
    <ComponentOne />
    ...
    <ComponentOne /> // не приведет к дублированию ссылки на таблицу стилей в DOM
  </>
}
```

Для пользователей, привыкших загружать таблицы стилей вручную, это возможность разместить эти таблицы стилей рядом с компонентами, которые от них зависят, что обеспечивает лучшее локальное понимание и упрощает гарантирование загрузки только тех таблиц стилей, которые вам действительно нужны.

Библиотеки стилей и интеграции стилей с бандлерами также могут использовать эту новую возможность, так что даже если вы не рендерите свои таблицы стилей напрямую, вы всё равно можете получить выгоду, поскольку ваши инструменты будут обновлены для использования этой функции.

Для получения дополнительной информации прочитайте документацию по [`<link>`](/reference/react-dom/components/link) и [`<style>`](/reference/react-dom/components/style).

### Поддержка асинхронных скриптов {/*support-for-async-scripts*/}

В HTML обычные скрипты (`<script src="...">`) и отложенные скрипты (`<script defer="" src="...">`) загружаются в порядке документа, что затрудняет рендеринг таких скриптов глубоко в дереве компонентов. Асинхронные скрипты (`<script async="" src="...">`), однако, загружаются в произвольном порядке.

В React 19 мы включили лучшую поддержку асинхронных скриптов, позволив вам рендерить их в любом месте дерева компонентов, внутри компонентов, которые фактически зависят от скрипта, без необходимости управлять перемещением и дедупликацией экземпляров скриптов.

```js {4,15}
function MyComponent() {
  return (
    <div>
      <script async={true} src="..." />
      Hello World
    </div>
  )
}

function App() {
  <html>
    <body>
      <MyComponent>
      ...
      <MyComponent> // не приведет к дублированию скрипта в DOM
    </body>
  </html>
}
```

Во всех средах рендеринга асинхронные скрипты будут дедуплицироваться, так что React загрузит и выполнит скрипт только один раз, даже если он будет отрендерен несколькими разными компонентами.

При серверном рендеринге асинхронные скрипты будут включены в `<head>` и будут иметь приоритет ниже критических ресурсов, блокирующих отрисовку, таких как таблицы стилей, шрифты и предварительная загрузка изображений.

Для получения дополнительной информации прочитайте документацию по [`<script>`](/reference/react-dom/components/script).

### Поддержка предварительной загрузки ресурсов {/*support-for-preloading-resources*/}

Во время начальной загрузки документа и при обновлениях на стороне клиента, заблаговременное уведомление браузера о ресурсах, которые ему, вероятно, понадобятся, может значительно повысить производительность страницы.

React 19 включает ряд новых API для загрузки и предварительной загрузки браузерных ресурсов, чтобы максимально упростить создание отличных пользовательских интерфейсов, не сдерживаемых неэффективной загрузкой ресурсов.

```js
import { prefetchDNS, preconnect, preload, preinit } from 'react-dom'
function MyComponent() {
  preinit('https://.../path/to/some/script.js', {as: 'script' }) // загружает и выполняет этот скрипт немедленно
  preload('https://.../path/to/font.woff', { as: 'font' }) // предварительно загружает этот шрифт
  preload('https://.../path/to/stylesheet.css', { as: 'style' }) // предварительно загружает эту таблицу стилей
  prefetchDNS('https://...') // когда вы, возможно, ничего не будете запрашивать с этого хоста
  preconnect('https://...') // когда вы будете что-то запрашивать, но не уверены что именно
}
```
```html
<!-- вышеприведенный код приведет к следующему DOM/HTML -->
<html>
  <head>
    <!-- ссылки/скрипты приоритизируются по их полезности для ранней загрузки, а не по порядку вызова -->
    <link rel="prefetch-dns" href="https://...">
    <link rel="preconnect" href="https://...">
    <link rel="preload" as="font" href="https://.../path/to/font.woff">
    <link rel="preload" as="style" href="https://.../path/to/stylesheet.css">
    <script async="" src="https://.../path/to/some/script.js"></script>
  </head>
  <body>
    ...
  </body>
</html>
```

Эти API можно использовать для оптимизации начальной загрузки страницы, перемещая обнаружение дополнительных ресурсов, таких как шрифты, из загрузки таблиц стилей. Они также могут ускорить обновления на стороне клиента, предварительно загружая список ресурсов, используемых ожидаемой навигацией, а затем немедленно предварительно загружая эти ресурсы при клике или даже при наведении курсора.

Для получения более подробной информации см. [API предварительной загрузки ресурсов](/reference/react-dom#resource-preloading-apis).

### Совместимость со сторонними скриптами и расширениями {/*compatibility-with-third-party-scripts-and-extensions*/}

Мы улучшили гидратацию для учета сторонних скриптов и расширений браузера.

При гидратации, если элемент, который рендерится на клиенте, не совпадает с элементом, найденным в HTML с сервера, React принудительно выполнит клиентский рендеринг для исправления содержимого. Ранее, если элемент был вставлен сторонними скриптами или расширениями браузера, это приводило к ошибке несоответствия и клиентскому рендерингу.

В React 19 неожиданные теги в `<head>` и `<body>` будут пропущены, что позволит избежать ошибок несоответствия. Если React потребуется повторно отрендерить весь документ из-за несвязанного несоответствия гидратации, он оставит на месте таблицы стилей, вставленные сторонними скриптами и расширениями браузера.

### Улучшенная отчетность об ошибках {/*error-handling*/}

Мы улучшили обработку ошибок в React 19, чтобы устранить дублирование и предоставить опции для обработки перехваченных и неперехваченных ошибок. Например, когда возникает ошибка при рендеринге, пойманная Error Boundary, ранее React выбрасывал ошибку дважды (один раз для исходной ошибки, затем снова после неудачной автоматической регенерации), а затем вызывал `console.error` с информацией о месте возникновения ошибки.

Это приводило к трем ошибкам на каждую пойманную ошибку:

<ConsoleBlockMulti>

<ConsoleLogLine level="error">

Uncaught Error: hit
{'  '}at Throws
{'  '}at renderWithHooks
{'  '}...

</ConsoleLogLine>

<ConsoleLogLine level="error">

Uncaught Error: hit<span className="ms-2 text-gray-30">{'    <--'} Duplicate</span>
{'  '}at Throws
{'  '}at renderWithHooks
{'  '}...

</ConsoleLogLine>

<ConsoleLogLine level="error">

The above error occurred in the Throws component:
{'  '}at Throws
{'  '}at ErrorBoundary
{'  '}at App{'\n'}
React will try to recreate this component tree from scratch using the error boundary you provided, ErrorBoundary.

</ConsoleLogLine>

</ConsoleBlockMulti>

В React 19 мы регистрируем одну ошибку со всей включенной информацией об ошибке:

<ConsoleBlockMulti>

<ConsoleLogLine level="error">

Error: hit
{'  '}at Throws
{'  '}at renderWithHooks
{'  '}...{'\n'}
The above error occurred in the Throws component:
{'  '}at Throws
{'  '}at ErrorBoundary
{'  '}at App{'\n'}
React will try to recreate this component tree from scratch using the error boundary you provided, ErrorBoundary.
{'  '}at ErrorBoundary
{'  '}at App

</ConsoleLogLine>

</ConsoleBlockMulti>

Кроме того, мы добавили две новые опции корневого узла в дополнение к `onRecoverableError`:

- `onCaughtError`: вызывается, когда React перехватывает ошибку в Error Boundary.
- `onUncaughtError`: вызывается, когда выбрасывается ошибка, не перехваченная Error Boundary.
- `onRecoverableError`: вызывается, когда выбрасывается ошибка и происходит автоматическое восстановление.

Для получения дополнительной информации и примеров см. документацию по [`createRoot`](/reference/react-dom/client/createRoot) и [`hydrateRoot`](/reference/react-dom/client/hydrateRoot).

### Поддержка пользовательских элементов {/*support-for-custom-elements*/}

React 19 добавляет полную поддержку пользовательских элементов и проходит все тесты на [Custom Elements Everywhere](https://custom-elements-everywhere.com/).

В предыдущих версиях использование пользовательских элементов в React было затруднено, поскольку React рассматривал неузнанные пропсы как атрибуты, а не свойства. В React 19 мы добавили поддержку свойств, которая работает на клиенте и во время SSR со следующей стратегией:

- **Рендеринг на стороне сервера**: пропсы, переданные пользовательскому элементу, будут отображаться как атрибуты, если их тип является примитивным значением, таким как `string`, `number`, или значение равно `true`. Пропсы с непромитивными типами, такими как `object`, `symbol`, `function`, или значением `false`, будут опущены.
- **Рендеринг на стороне клиента**: пропсы, соответствующие свойству экземпляра пользовательского элемента, будут назначены как свойства, в противном случае они будут назначены как атрибуты.

Благодаря [Джоуи Архару](https://github.com/josepharhar) за разработку дизайна и реализацию поддержки пользовательских элементов в React.


#### Как обновиться {/*how-to-upgrade*/}
См. [Руководство по обновлению до React 19](/blog/2024/04/25/react-19-upgrade-guide) для пошаговых инструкций и полного списка критических и примечательных изменений.

_Примечание: этот пост был первоначально опубликован 25.04.2024 и обновлен до 05.12.2024 с выходом стабильной версии._