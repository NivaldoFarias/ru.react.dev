---
title: renderToPipeableStream
---

<Intro>

`renderToPipeableStream` рендерит React-дерево в [потоковый объект Node.js Stream.](https://nodejs.org/api/stream.html)

```js
const { pipe, abort } = renderToPipeableStream(reactNode, options?)
```

</Intro>

<InlineToc />

<Note>

Этот API специфичен для Node.js. В средах с [Web Streams](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API), таких как Deno и современные edge-среды выполнения, следует использовать [`renderToReadableStream`](/reference/react-dom/server/renderToReadableStream) вместо этого.

</Note>

---

## Справочник {/*reference*/}

### `renderToPipeableStream(reactNode, options?)` {/*rendertopipeablestream*/}

Вызовите `renderToPipeableStream`, чтобы отрендерить ваше React-дерево в HTML в виде [Node.js Stream.](https://nodejs.org/api/stream.html#writable-streams)

```js
import { renderToPipeableStream } from 'react-dom/server';

const { pipe } = renderToPipeableStream(<App />, {
  bootstrapScripts: ['/main.js'],
  onShellReady() {
    response.setHeader('content-type', 'text/html');
    pipe(response);
  }
});
```

На клиенте вызовите [`hydrateRoot`](/reference/react-dom/client/hydrateRoot), чтобы сделать сгенерированный сервером HTML интерактивным.

[См. больше примеров ниже.](#usage)

#### Параметры {/*parameters*/}

* `reactNode`: React-узел, который вы хотите отрендерить в HTML. Например, JSX-элемент вроде `<App />`. Ожидается, что он будет представлять собой весь документ, поэтому компонент `App` должен рендерить тег `<html>`.

* **необязательный** `options`: Объект с опциями потоковой передачи.
  * **необязательный** `bootstrapScriptContent`: Если указано, эта строка будет помещена во встроенный тег `<script>`.
  * **необязательный** `bootstrapScripts`: Массив строк с URL-адресами для тегов `<script>`, которые будут вставлены на страницу. Используйте это для включения `<script>`, который вызывает [`hydrateRoot`.](/reference/react-dom/client/hydrateRoot) Пропустите его, если вы вообще не хотите запускать React на клиенте.
  * **необязательный** `bootstrapModules`: Подобно `bootstrapScripts`, но вставляет [`<script type="module">`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules) вместо этого.
  * **необязательный** `identifierPrefix`: Строка-префикс, которую React использует для идентификаторов, сгенерированных [`useId`.](/reference/react/useId) Полезно для предотвращения конфликтов при использовании нескольких корней на одной странице. Должен быть тем же префиксом, что и переданный в [`hydrateRoot`.](/reference/react-dom/client/hydrateRoot#parameters)
  * **необязательный** `namespaceURI`: Строка с корневым [URI пространства имён](https://developer.mozilla.org/en-US/docs/Web/API/Document/createElementNS#important_namespace_uris) для потока. По умолчанию используется обычный HTML. Передайте `'http://www.w3.org/2000/svg'` для SVG или `'http://www.w3.org/1998/Math/MathML'` для MathML.
  * **необязательный** `nonce`: Строка [`nonce`](http://developer.mozilla.org/en-US/docs/Web/HTML/Element/script#nonce) для разрешения скриптов для [`script-src` Content-Security-Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/script-src).
  * **необязательный** `onAllReady`: Обратный вызов, который срабатывает, когда рендеринг полностью завершён, включая как [оболочку](#specifying-what-goes-into-the-shell), так и весь дополнительный [контент.](#streaming-more-content-as-it-loads) Вы можете использовать это вместо `onShellReady` [для краулеров и статической генерации.](#waiting-for-all-content-to-load-for-crawlers-and-static-generation) Если вы начнёте потоковую передачу здесь, вы не получите никакой прогрессивной загрузки. Поток будет содержать окончательный HTML.
  * **необязательный** `onError`: Обратный вызов, который срабатывает при любой ошибке сервера, будь то [восстановимая](#recovering-from-errors-outside-the-shell) или [нет.](#recovering-from-errors-inside-the-shell) По умолчанию этот метод просто вызывает `console.error`. Если вы переопределите его для [логирования сбоев на сервере,](#logging-crashes-on-the-server) убедитесь, что вы всё равно вызываете `console.error`. Вы также можете использовать его для [установки кода состояния](#setting-the-status-code) перед отправкой оболочки.
  * **необязательный** `onShellReady`: Обратный вызов, который срабатывает сразу после рендеринга [начальной оболочки](#specifying-what-goes-into-the-shell). Вы можете [установить код состояния](#setting-the-status-code) и вызвать `pipe` здесь, чтобы начать потоковую передачу. React будет [потоково передавать дополнительный контент](#streaming-more-content-as-it-loads) после оболочки вместе со встроенными тегами `<script>`, которые заменяют HTML-заполнители для загрузки контентом.
  * **необязательный** `onShellError`: Обратный вызов, который срабатывает, если при рендеринге начальной оболочки произошла ошибка. Он получает ошибку в качестве аргумента. Пока ещё ни один байт не был отправлен в поток, и ни `onShellReady`, ни `onAllReady` не будут вызваны, поэтому вы можете [вывести запасную HTML-оболочку.](#recovering-from-errors-inside-the-shell)
  * **необязательный** `progressiveChunkSize`: Количество байтов в чанке. [Подробнее о стандартной эвристике.](https://github.com/facebook/react/blob/14c2be8dac2d5482fda8a0906a31d239df8551fc/packages/react-server/src/ReactFizzServer.js#L210-L225)


#### Возвращает {/*returns*/}

`renderToPipeableStream` возвращает объект с двумя методами:

* `pipe` выводит HTML в предоставленный [Writable Node.js Stream.](https://nodejs.org/api/stream.html#writable-streams) Вызовите `pipe` в `onShellReady`, если вы хотите включить потоковую передачу, или в `onAllReady` для краулеров и статической генерации.
* `abort` позволяет вам [отменить рендеринг на сервере](#aborting-server-rendering) и выполнить оставшуюся часть на клиенте.

---

## Использование {/*usage*/}

### Отрисовка React-дерева в HTML для Node.js Stream {/*rendering-a-react-tree-as-html-to-a-nodejs-stream*/}

Вызовите `renderToPipeableStream`, чтобы отрисовать ваше React-дерево в HTML в [Node.js Stream:](https://nodejs.org/api/stream.html#writable-streams)

```js [[1, 5, "<App />"], [2, 6, "['/main.js']"]]
import { renderToPipeableStream } from 'react-dom/server';

// Синтаксис обработчика маршрута зависит от вашего серверного фреймворка
app.use('/', (request, response) => {
  const { pipe } = renderToPipeableStream(<App />, {
    bootstrapScripts: ['/main.js'],
    onShellReady() {
      response.setHeader('content-type', 'text/html');
      pipe(response);
    }
  });
});
```

Вместе с <CodeStep step={1}>корневым компонентом</CodeStep> вам нужно предоставить список <CodeStep step={2}>путей к bootstrap `<script>`</CodeStep>. Ваш корневой компонент должен возвращать **весь документ, включая корневой тег `<html>`.**

Например, это может выглядеть так:

```js [[1, 1, "App"]]
export default function App() {
  return (
    <html>
      <head>
        <meta charSet="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1" />
        <link rel="stylesheet" href="/styles.css"></link>
        <title>My app</title>
      </head>
      <body>
        <Router />
      </body>
    </html>
  );
}
```

React вставит [doctype](https://developer.mozilla.org/en-US/docs/Glossary/Doctype) и ваши <CodeStep step={2}>bootstrap `<script>` теги</CodeStep> в результирующий HTML-поток:

```html [[2, 5, "/main.js"]]
<!DOCTYPE html>
<html>
  <!-- ... HTML из ваших компонентов ... -->
</html>
<script src="/main.js" async=""></script>
```

На клиенте ваш bootstrap-скрипт должен [гидрировать весь `document` вызовом `hydrateRoot`:](/reference/react-dom/client/hydrateRoot#hydrating-an-entire-document)

```js [[1, 4, "<App />"]]
import { hydrateRoot } from 'react-dom/client';
import App from './App.js';

hydrateRoot(document, <App />);
```

Это подключит обработчики событий к сгенерированному сервером HTML и сделает его интерактивным.

<DeepDive>

#### Чтение путей к CSS и JS ресурсам из сборки {/*reading-css-and-js-asset-paths-from-the-build-output*/}

Финальные URL-адреса ресурсов (например, файлов JavaScript и CSS) часто хешируются после сборки. Например, вместо `styles.css` вы можете получить `styles.123456.css`. Хеширование имен файлов статических ресурсов гарантирует, что каждая отдельная сборка одного и того же ресурса будет иметь другое имя файла. Это полезно, потому что позволяет безопасно включать долгосрочное кеширование для статических ресурсов: файл с определенным именем никогда не изменит свое содержимое.

Однако, если вы не знаете URL-адреса ресурсов до завершения сборки, вы не сможете указать их в исходном коде. Например, жесткое кодирование `"/styles.css"` в JSX, как было показано ранее, не сработает. Чтобы исключить их из вашего исходного кода, корневой компонент может считывать реальные имена файлов из карты, передаваемой в качестве пропса:

```js {1,6}
export default function App({ assetMap }) {
  return (
    <html>
      <head>
        ...
        <link rel="stylesheet" href={assetMap['styles.css']}></link>
        ...
      </head>
      ...
    </html>
  );
}
```

На сервере отрисуйте `<App assetMap={assetMap} />` и передайте вашу `assetMap` с URL-адресами ресурсов:

```js {1-5,8,9}
// Вам нужно будет получить этот JSON из ваших инструментов сборки, например, прочитать его из выходных данных сборки.
const assetMap = {
  'styles.css': '/styles.123456.css',
  'main.js': '/main.123456.js'
};

app.use('/', (request, response) => {
  const { pipe } = renderToPipeableStream(<App assetMap={assetMap} />, {
    bootstrapScripts: [assetMap['main.js']],
    onShellReady() {
      response.setHeader('content-type', 'text/html');
      pipe(response);
    }
  });
});
```

Поскольку ваш сервер теперь отрисовывает `<App assetMap={assetMap} />`, вам также нужно отрисовать его с `assetMap` на клиенте, чтобы избежать ошибок гидратации. Вы можете сериализовать и передать `assetMap` клиенту следующим образом:

```js {9-10}
// Вам нужно будет получить этот JSON из ваших инструментов сборки.
const assetMap = {
  'styles.css': '/styles.123456.css',
  'main.js': '/main.123456.js'
};

app.use('/', (request, response) => {
  const { pipe } = renderToPipeableStream(<App assetMap={assetMap} />, {
    // Внимание: Безопасно использовать stringify(), так как эти данные не генерируются пользователем.
    bootstrapScriptContent: `window.assetMap = ${JSON.stringify(assetMap)};`,
    bootstrapScripts: [assetMap['main.js']],
    onShellReady() {
      response.setHeader('content-type', 'text/html');
      pipe(response);
    }
  });
});
```

В приведенном выше примере опция `bootstrapScriptContent` добавляет дополнительный встроенный тег `<script>`, который устанавливает глобальную переменную `window.assetMap` на клиенте. Это позволяет клиентскому коду считывать ту же `assetMap`:

```js {4}
import { hydrateRoot } from 'react-dom/client';
import App from './App.js';

hydrateRoot(document, <App assetMap={window.assetMap} />);
```

И клиент, и сервер отрисовывают `App` с одинаковым пропсом `assetMap`, поэтому ошибок гидратации не возникает.

</DeepDive>

---

### Потоковая передача большего количества контента по мере его загрузки {/*streaming-more-content-as-it-loads*/}

Потоковая передача позволяет пользователю начать видеть контент еще до того, как все данные будут загружены на сервере. Например, рассмотрим страницу профиля, которая отображает обложку, боковую панель с друзьями и фотографиями, а также список постов:

```js
function ProfilePage() {
  return (
    <ProfileLayout>
      <ProfileCover />
      <Sidebar>
        <Friends />
        <Photos />
      </Sidebar>
      <Posts />
    </ProfileLayout>
  );
}
```

Предположим, загрузка данных для `<Posts />` занимает некоторое время. В идеале вы хотели бы показать остальной контент страницы профиля пользователю, не дожидаясь загрузки постов. Для этого [оберните `Posts` в `<Suspense>` границу:](/reference/react/Suspense#displaying-a-fallback-while-content-is-loading)

```js {9,11}
function ProfilePage() {
  return (
    <ProfileLayout>
      <ProfileCover />
      <Sidebar>
        <Friends />
        <Photos />
      </Sidebar>
      <Suspense fallback={<PostsGlimmer />}>
        <Posts />
      </Suspense>
    </ProfileLayout>
  );
}
```

Это говорит React начать потоковую передачу HTML до того, как `Posts` загрузит свои данные. React сначала отправит HTML для запасного варианта загрузки (`PostsGlimmer`), а затем, когда `Posts` закончит загрузку своих данных, React отправит оставшийся HTML вместе со встроенным тегом `<script>`, который заменит запасной вариант загрузки этим HTML. С точки зрения пользователя, страница сначала появится с `PostsGlimmer`, а затем будет заменена на `Posts`.

Вы можете дополнительно [вкладывать `<Suspense>` границы](/reference/react/Suspense#revealing-nested-content-as-it-loads), чтобы создать более гранулированную последовательность загрузки:

```js {5,13}
function ProfilePage() {
  return (
    <ProfileLayout>
      <ProfileCover />
      <Suspense fallback={<BigSpinner />}>
        <Sidebar>
          <Friends />
          <Photos />
        </Sidebar>
        <Suspense fallback={<PostsGlimmer />}>
          <Posts />
        </Suspense>
      </Suspense>
    </ProfileLayout>
  );
}
```

В этом примере React может начать потоковую передачу страницы еще раньше. Только `ProfileLayout` и `ProfileCover` должны завершить отрисовку первыми, поскольку они не обернуты ни в какую `<Suspense>` границу. Однако, если `Sidebar`, `Friends` или `Photos` требуют загрузки каких-либо данных, React вместо этого отправит HTML для запасного варианта `BigSpinner`. Затем, по мере поступления новых данных, будет раскрываться больше контента, пока все не станет видимым.

Потоковая передача не требует ожидания загрузки самого React в браузере или интерактивности вашего приложения. HTML-контент с сервера будет постепенно раскрываться до загрузки каких-либо `<script>` тегов.

[Подробнее о том, как работает потоковая передача HTML.](https://github.com/reactwg/react-18/discussions/37)

<Note>

**Только источники данных, поддерживающие Suspense, активируют компонент Suspense.** К ним относятся:

- Получение данных с помощью фреймворков, поддерживающих Suspense, таких как [Relay](https://relay.dev/docs/guided-tour/rendering/loading-states/) и [Next.js](https://nextjs.org/docs/getting-started/react-essentials)
- Ленивая загрузка кода компонентов с помощью [`lazy`](/reference/react/lazy)
- Чтение значения Promise с помощью [`use`](/reference/react/use)

Suspense **не** обнаруживает, когда данные извлекаются внутри Effect или обработчика событий.

Точный способ загрузки данных в компоненте `Posts` выше зависит от вашего фреймворка. Если вы используете фреймворк, поддерживающий Suspense, вы найдете подробности в его документации по получению данных.

Получение данных с поддержкой Suspense без использования авторитетного фреймворка пока не поддерживается. Требования к реализации источника данных с поддержкой Suspense нестабильны и не документированы. Официальный API для интеграции источников данных с Suspense будет выпущен в будущей версии React.

</Note>

---

### Указание того, что входит в оболочку {/*specifying-what-goes-into-the-shell*/}

Часть вашего приложения, находящаяся вне любых `<Suspense>` границ, называется *оболочкой (shell):*

```js {3-5,13,14}
function ProfilePage() {
  return (
    <ProfileLayout>
      <ProfileCover />
      <Suspense fallback={<BigSpinner />}>
        <Sidebar>
          <Friends />
          <Photos />
        </Sidebar>
        <Suspense fallback={<PostsGlimmer />}>
          <Posts />
        </Suspense>
      </Suspense>
    </ProfileLayout>
  );
}
```

Она определяет самое раннее состояние загрузки, которое может увидеть пользователь:

```js {3-5,13
<ProfileLayout>
  <ProfileCover />
  <BigSpinner />
</ProfileLayout>
```

Если вы обернете все приложение в `<Suspense>` границу на корневом уровне, оболочка будет содержать только этот спиннер. Однако это не очень приятный пользовательский опыт, поскольку видеть большой спиннер на экране может ощущаться медленнее и раздражать больше, чем немного подождать и увидеть реальный макет. Поэтому обычно вы захотите разместить `<Suspense>` границы так, чтобы оболочка выглядела *минимальной, но полной* — как скелет всего макета страницы.

Колбэк `onShellReady` срабатывает, когда вся оболочка отрисована. Обычно вы начинаете потоковую передачу в этот момент:

```js {3-6}
const { pipe } = renderToPipeableStream(<App />, {
  bootstrapScripts: ['/main.js'],
  onShellReady() {
    response.setHeader('content-type', 'text/html');
    pipe(response);
  }
});
```

К моменту срабатывания `onShellReady` компоненты во вложенных `<Suspense>` границах могут все еще загружать данные.

---

### Логирование сбоев на сервере {/*logging-crashes-on-the-server*/}

По умолчанию все ошибки на сервере логируются в консоль. Вы можете переопределить это поведение, чтобы логировать отчеты о сбоях:

```js {7-10}
const { pipe } = renderToPipeableStream(<App />, {
  bootstrapScripts: ['/main.js'],
  onShellReady() {
    response.setHeader('content-type', 'text/html');
    pipe(response);
  },
  onError(error) {
    console.error(error);
    logServerCrashReport(error);
  }
});
```

Если вы предоставляете пользовательскую реализацию `onError`, не забудьте также логировать ошибки в консоль, как показано выше.

---

### Восстановление после ошибок внутри оболочки {/*recovering-from-errors-inside-the-shell*/}

В этом примере оболочка содержит `ProfileLayout`, `ProfileCover` и `PostsGlimmer`:

```js {3-5,7-8}
function ProfilePage() {
  return (
    <ProfileLayout>
      <ProfileCover />
      <Suspense fallback={<PostsGlimmer />}>
        <Posts />
      </Suspense>
    </ProfileLayout>
  );
}
```

Если при отрисовке этих компонентов возникает ошибка, у React не будет значимого HTML для отправки клиенту. Переопределите `onShellError`, чтобы отправить запасной HTML, который не полагается на серверный рендеринг в качестве крайней меры:

```js {7-11}
const { pipe } = renderToPipeableStream(<App />, {
  bootstrapScripts: ['/main.js'],
  onShellReady() {
    response.setHeader('content-type', 'text/html');
    pipe(response);
  },
  onShellError(error) {
    response.statusCode = 500;
    response.setHeader('content-type', 'text/html');
    response.send('<h1>Something went wrong</h1>');
  },
  onError(error) {
    console.error(error);
    logServerCrashReport(error);
  }
});
```

Если при генерации оболочки возникает ошибка, сработают как `onError`, так и `onShellError`. Используйте `onError` для отчетности об ошибках и `onShellError` для отправки запасного HTML-документа. Ваш запасной HTML не обязательно должен быть страницей ошибки. Вместо этого вы можете включить альтернативную оболочку, которая отрисовывает ваше приложение только на клиенте.

---

### Восстановление после ошибок вне оболочки {/*recovering-from-errors-outside-the-shell*/}

В этом примере компонент `<Posts />` обернут в `<Suspense>`, поэтому он *не* является частью оболочки:

```js {6}
function ProfilePage() {
  return (
    <ProfileLayout>
      <ProfileCover />
      <Suspense fallback={<PostsGlimmer />}>
        <Posts />
      </Suspense>
    </ProfileLayout>
  );
}
```

Если ошибка возникает в компоненте `Posts` или где-то внутри него, React [попытается восстановиться:](/reference/react/Suspense#providing-a-fallback-for-server-errors-and-client-only-content)

1. Он отправит запасной вариант загрузки для ближайшей `<Suspense>` границы (`PostsGlimmer`) в HTML.
2. Он "откажется" от попытки отрисовать контент `Posts` на сервере.
3. Когда JavaScript-код загрузится на клиенте, React *повторит попытку* отрисовки `Posts` на клиенте.

Если повторная попытка отрисовки `Posts` на клиенте *также* завершится ошибкой, React вызовет ошибку на клиенте. Как и при всех ошибках, возникающих во время рендеринга, [ближайший родительский обработчик ошибок](/reference/react/Component#static-getderivedstatefromerror) определяет, как представить ошибку пользователю. На практике это означает, что пользователь увидит индикатор загрузки до тех пор, пока не станет ясно, что ошибка не может быть исправлена.

Если повторная попытка отрисовки `Posts` на клиенте увенчается успехом, запасной вариант загрузки с сервера будет заменен выводом клиентского рендеринга. Пользователь не узнает, что была ошибка сервера. Однако сработают колбэк сервера `onError` и клиентские колбэки [`onRecoverableError`](/reference/react-dom/client/hydrateRoot#hydrateroot), чтобы вы могли получать уведомления об ошибке.

---

### Установка кода состояния {/*setting-the-status-code*/}

Потоковая передача (streaming) вводит компромисс. Вы хотите начать потоковую передачу страницы как можно раньше, чтобы пользователь мог увидеть контент быстрее. Однако, как только вы начали потоковую передачу, вы больше не можете установить код состояния ответа.

Разделив ваше приложение на оболочку (выше всех границ `<Suspense>`) и остальной контент, вы уже решили часть этой проблемы. Если оболочка выдает ошибку, вы получите обратный вызов `onShellError`, который позволяет установить код состояния ошибки. В противном случае вы знаете, что приложение может восстановиться на клиенте, поэтому вы можете отправить "OK".

```js {4}
const { pipe } = renderToPipeableStream(<App />, {
  bootstrapScripts: ['/main.js'],
  onShellReady() {
    response.statusCode = 200;
    response.setHeader('content-type', 'text/html');
    pipe(response);
  },
  onShellError(error) {
    response.statusCode = 500;
    response.setHeader('content-type', 'text/html');
    response.send('<h1>Something went wrong</h1>');
  },
  onError(error) {
    console.error(error);
    logServerCrashReport(error);
  }
});
```

Если компонент *вне* оболочки (т.е. внутри границы `<Suspense>`) выбрасывает ошибку, React не остановит рендеринг. Это означает, что будет вызван обратный вызов `onError`, но вы все равно получите `onShellReady` вместо `onShellError`. Это связано с тем, что React попытается восстановиться после этой ошибки на клиенте, [как описано выше.](#recovering-from-errors-outside-the-shell)

Однако, если вы хотите, вы можете использовать тот факт, что что-то вызвало ошибку, для установки кода состояния:

```js {1,6,16}
let didError = false;

const { pipe } = renderToPipeableStream(<App />, {
  bootstrapScripts: ['/main.js'],
  onShellReady() {
    response.statusCode = didError ? 500 : 200;
    response.setHeader('content-type', 'text/html');
    pipe(response);
  },
  onShellError(error) {
    response.statusCode = 500;
    response.setHeader('content-type', 'text/html');
    response.send('<h1>Something went wrong</h1>');
  },
  onError(error) {
    didError = true;
    console.error(error);
    logServerCrashReport(error);
  }
});
```

Это перехватит только ошибки вне оболочки, которые произошли во время генерации начального контента оболочки, поэтому это не исчерпывающе. Если знание о том, произошла ли ошибка для какого-либо контента, критично, вы можете переместить его выше, в оболочку.

---

### Обработка различных ошибок по-разному {/*handling-different-errors-in-different-ways*/}

Вы можете [создать свои собственные подклассы `Error`](https://javascript.info/custom-errors) и использовать оператор [`instanceof`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/instanceof) для проверки, какая ошибка была выброшена. Например, вы можете определить пользовательский `NotFoundError` и выбросить его из вашего компонента. Затем ваши обратные вызовы `onError`, `onShellReady` и `onShellError` смогут делать что-то разное в зависимости от типа ошибки:

```js {2,4-14,19,24,30}
let didError = false;
let caughtError = null;

function getStatusCode() {
  if (didError) {
    if (caughtError instanceof NotFoundError) {
      return 404;
    } else {
      return 500;
    }
  } else {
    return 200;
  }
}

const { pipe } = renderToPipeableStream(<App />, {
  bootstrapScripts: ['/main.js'],
  onShellReady() {
    response.statusCode = getStatusCode();
    response.setHeader('content-type', 'text/html');
    pipe(response);
  },
  onShellError(error) {
   response.statusCode = getStatusCode();
   response.setHeader('content-type', 'text/html');
   response.send('<h1>Something went wrong</h1>');
  },
  onError(error) {
    didError = true;
    caughtError = error;
    console.error(error);
    logServerCrashReport(error);
  }
});
```

Имейте в виду, что как только вы отправили оболочку и начали потоковую передачу, вы не можете изменить код состояния.

---

### Ожидание загрузки всего контента для краулеров и статической генерации {/*waiting-for-all-content-to-load-for-crawlers-and-static-generation*/}

Потоковая передача предлагает лучший пользовательский опыт, потому что пользователь может видеть контент по мере его доступности.

Однако, когда краулер посещает вашу страницу, или если вы генерируете страницы во время сборки, вы можете захотеть сначала загрузить весь контент, а затем произвести окончательный HTML-вывод вместо постепенного его раскрытия.

Вы можете дождаться загрузки всего контента с помощью обратного вызова `onAllReady`:


```js {2,7,11,18-24}
let didError = false;
let isCrawler = // ... зависит от вашей стратегии обнаружения ботов ...

const { pipe } = renderToPipeableStream(<App />, {
  bootstrapScripts: ['/main.js'],
  onShellReady() {
    if (!isCrawler) {
      response.statusCode = didError ? 500 : 200;
      response.setHeader('content-type', 'text/html');
      pipe(response);
    }
  },
  onShellError(error) {
    response.statusCode = 500;
    response.setHeader('content-type', 'text/html');
    response.send('<h1>Something went wrong</h1>');
  },
  onAllReady() {
    if (isCrawler) {
      response.statusCode = didError ? 500 : 200;
      response.setHeader('content-type', 'text/html');
      pipe(response);
    }
  },
  onError(error) {
    didError = true;
    console.error(error);
    logServerCrashReport(error);
  }
});
```

Обычный посетитель получит поток контента, загружаемого постепенно. Краулер получит окончательный HTML-вывод после загрузки всех данных. Однако это также означает, что краулеру придется ждать *всех* данных, некоторые из которых могут медленно загружаться или вызывать ошибки. В зависимости от вашего приложения, вы можете выбрать отправку оболочки и для краулеров.

---

### Отмена серверного рендеринга {/*aborting-server-rendering*/}

Вы можете принудительно "сдаться" серверному рендерингу по истечении времени ожидания:

```js {1,5-7}
const { pipe, abort } = renderToPipeableStream(<App />, {
  // ...
});

setTimeout(() => {
  abort();
}, 10000);
```

React сбросит оставшиеся заполнители загрузки в виде HTML и попытается отрисовать остальное на клиенте.