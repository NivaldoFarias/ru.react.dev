---
title: "`<form>`"
---
```
<Intro>

[Встроенный компонент `<form>` браузера](https://developer.mozilla.org/ru/docs/Web/HTML/Element/form) позволяет создавать интерактивные элементы управления для отправки информации.

```js
<form action={search}>
    <input name="query" />
    <button type="submit">Поиск</button>
</form>
```

</Intro>

<InlineToc />

---

## Справочник {/*reference*/}

### `<form>` {/*form*/}

Чтобы создать интерактивные элементы управления для отправки информации, отрендерите [встроенный компонент `<form>` браузера](https://developer.mozilla.org/ru/docs/Web/HTML/Element/form).

```js
<form action={search}>
    <input name="query" />
    <button type="submit">Поиск</button>
</form>
```

[См. больше примеров ниже.](#usage)

#### Пропсы {/*props*/}

`<form>` поддерживает все [общие пропсы элементов.](/reference/react-dom/components/common#props)

[`action`](https://developer.mozilla.org/ru/docs/Web/HTML/Element/form#action): URL или функция. Когда в `action` передаётся URL, форма будет вести себя как компонент HTML-формы. Когда в `action` передаётся функция, функция будет обрабатывать отправку формы. Функция, переданная в `action`, может быть асинхронной и будет вызвана с одним аргументом, содержащим [данные формы](https://developer.mozilla.org/ru/docs/Web/API/FormData) отправленной формы. Проп `action` может быть переопределён атрибутом `formAction` в компоненте `<button>`, `<input type="submit">` или `<input type="image">`.

#### Предостережения {/*caveats*/}

* Когда в `action` или `formAction` передаётся функция, HTTP-метод будет POST, независимо от значения пропа `method`.

---

## Использование {/*usage*/}

### Обработка отправки формы на клиенте {/*handle-form-submission-on-the-client*/}

Передайте функцию в проп `action` формы, чтобы запустить функцию при отправке формы. [`formData`](https://developer.mozilla.org/ru/docs/Web/API/FormData) будет передана в функцию в качестве аргумента, чтобы вы могли получить доступ к данным, отправленным формой. Это отличается от обычной [HTML-action](https://developer.mozilla.org/ru/docs/Web/HTML/Element/form#action), которая принимает только URL-адреса. После успешного выполнения функции `action` все неуправляемые элементы полей в форме сбрасываются.

<Sandpack>

```js src/App.js
export default function Search() {
  function search(formData) {
    const query = formData.get("query");
    alert(`Вы искали '${query}'`);
  }
  return (
    <form action={search}>
      <input name="query" />
      <button type="submit">Поиск</button>
    </form>
  );
}
```

</Sandpack>

### Обработка отправки формы с помощью серверной функции {/*handle-form-submission-with-a-server-function*/}

Отрендерите `<form>` с полем ввода и кнопкой отправки. Передайте серверную функцию (функцию, помеченную как [`'use server'`](/reference/rsc/use-server)) в проп `action` формы, чтобы запустить функцию при отправке формы.

Передача серверной функции в `<form action>` позволяет пользователям отправлять формы без включённого JavaScript или до загрузки кода. Это полезно для пользователей с медленным подключением, устройством или отключённым JavaScript и аналогично тому, как работают формы, когда в проп `action` передаётся URL.

Вы можете использовать скрытые поля формы, чтобы предоставить данные для действия `<form>`. Серверная функция будет вызвана с данными скрытого поля формы в виде экземпляра [`FormData`](https://developer.mozilla.org/ru/docs/Web/API/FormData).

```jsx
import { updateCart } from './lib.js';

function AddToCart({productId}) {
  async function addToCart(formData) {
    'use server'
    const productId = formData.get('productId')
    await updateCart(productId)
  }
  return (
    <form action={addToCart}>
        <input type="hidden" name="productId" value={productId} />
        <button type="submit">Add to Cart</button>
    </form>

  );
}
```

Вместо использования скрытых полей формы для предоставления данных для действия `<form>`, вы можете вызвать метод <CodeStep step={1}>`bind`</CodeStep>, чтобы предоставить ему дополнительные аргументы. Это привяжет новый аргумент (<CodeStep step={2}>`productId`</CodeStep>) к функции в дополнение к <CodeStep step={3}>`formData`</CodeStep>, который передаётся в качестве аргумента функции.

```jsx [[1, 8, "bind"], [2,8, "productId"], [2,4, "productId"], [3,4, "formData"]]
import { updateCart } from './lib.js';

function AddToCart({productId}) {
  async function addToCart(productId, formData) {
    "use server";
    await updateCart(productId)
  }
  const addProductToCart = addToCart.bind(null, productId);
  return (
    <form action={addProductToCart}>
      <button type="submit">Add to Cart</button>
    </form>
  );
}
```

Когда `<form>` отрендерен [серверным компонентом](/reference/rsc/use-client), а [серверная функция](/reference/rsc/server-functions) передаётся в проп `action` `<form>`, форма [постепенно улучшается](https://developer.mozilla.org/ru/docs/Glossary/Progressive_Enhancement).

### Отображение состояния ожидания во время отправки формы {/*display-a-pending-state-during-form-submission*/}

Чтобы отобразить состояние ожидания при отправке формы, вы можете вызвать хук `useFormStatus` в компоненте, отрендеренном в `<form>`, и прочитать возвращённое свойство `pending`.

Здесь мы используем свойство `pending`, чтобы указать, что форма отправляется.

<Sandpack>

```js src/App.js
import { useFormStatus } from "react-dom";
import { submitForm } from "./actions.js";

function Submit() {
  const { pending } = useFormStatus();
  return (
    <button type="submit" disabled={pending}>
      {pending ? "Отправка..." : "Отправить"}
    </button>
  );
}

function Form({ action }) {
  return (
    <form action={action}>
      <Submit />
    </form>
  );
}

export default function App() {
  return <Form action={submitForm} />;
}
```

```js src/actions.js hidden
export async function submitForm(query) {
    await new Promise((res) => setTimeout(res, 1000));
}
```

</Sandpack>

Чтобы узнать больше о хуке `useFormStatus`, см. [справочную документацию](/reference/react-dom/hooks/useFormStatus).

### Оптимистичное обновление данных формы {/*optimistically-updating-form-data*/}

Хук `useOptimistic` предоставляет способ оптимистичного обновления пользовательского интерфейса до завершения фоновой операции, такой как сетевой запрос. В контексте форм эта техника помогает сделать приложения более отзывчивыми. Когда пользователь отправляет форму, вместо ожидания ответа сервера для отражения изменений, интерфейс немедленно обновляется с ожидаемым результатом.

Например, когда пользователь вводит сообщение в форму и нажимает кнопку «Отправить», хук `useOptimistic` позволяет сообщению сразу появиться в списке с меткой «Отправка...», даже до того, как сообщение будет отправлено на сервер. Этот «оптимистичный» подход создаёт впечатление скорости и отзывчивости. Затем форма пытается по-настоящему отправить сообщение в фоновом режиме. Как только сервер подтвердит, что сообщение получено, метка «Отправка...» удаляется.

<Sandpack>


```js src/App.js
import { useOptimistic, useState, useRef } from "react";
import { deliverMessage } from "./actions.js";

function Thread({ messages, sendMessage }) {
  const formRef = useRef();
  async function formAction(formData) {
    addOptimisticMessage(formData.get("message"));
    formRef.current.reset();
    await sendMessage(formData);
  }
  const [optimisticMessages, addOptimisticMessage] = useOptimistic(
    messages,
    (state, newMessage) => [
      ...state,
      {
        text: newMessage,
        sending: true
      }
    ]
  );

  return (
    <>
      {optimisticMessages.map((message, index) => (
        <div key={index}>
          {message.text}
          {!!message.sending && <small> (Отправка...)</small>}
        </div>
      ))}
      <form action={formAction} ref={formRef}>
        <input type="text" name="message" placeholder="Привет!" />
        <button type="submit">Отправить</button>
      </form>
    </>
  );
}

export default function App() {
  const [messages, setMessages] = useState([
    { text: "Привет!", sending: false, key: 1 }
  ]);
  async function sendMessage(formData) {
    const sentMessage = await deliverMessage(formData.get("message"));
    setMessages((messages) => [...messages, { text: sentMessage }]);
  }
  return <Thread messages={messages} sendMessage={sendMessage} />;
}
```

```js src/actions.js
export async function deliverMessage(message) {
  await new Promise((res) => setTimeout(res, 1000));
  return message;
}
```

</Sandpack>

[//]: # 'Uncomment the next line, and delete this line after the `useOptimistic` reference documentatino page is published'
[//]: # 'To learn more about the `useOptimistic` Hook see the [reference documentation](/reference/react/hooks/useOptimistic).'

### Обработка ошибок отправки формы {/*handling-form-submission-errors*/}

В некоторых случаях функция, вызываемая пропом `action` `<form>`, выдаёт ошибку. Вы можете обработать эти ошибки, обернув `<form>` в Error Boundary. Если функция, вызываемая пропом `action` `<form>`, выдаёт ошибку, будет отображена резервная копия для предохранителя.

<Sandpack>

```js src/App.js
import { ErrorBoundary } from "react-error-boundary";

export default function Search() {
  function search() {
    throw new Error("ошибка поиска");
  }
  return (
    <ErrorBoundary
      fallback={<p>Произошла ошибка при отправке формы</p>}
    >
      <form action={search}>
        <input name="query" />
        <button type="submit">Поиск</button>
      </form>
    </ErrorBoundary>
  );
}

```

```json package.json hidden
{
  "dependencies": {
    "react": "19.0.0-rc-3edc000d-20240926",
    "react-dom": "19.0.0-rc-3edc000d-20240926",
    "react-scripts": "^5.0.0",
    "react-error-boundary": "4.0.3"
  },
  "main": "/index.js",
  "devDependencies": {}
}
```

</Sandpack>

### Отображение сообщения об ошибке отправки формы без JavaScript {/*display-a-form-submission-error-without-javascript*/}

Отображение сообщения об ошибке отправки формы до загрузки пакета JavaScript для прогрессивного улучшения требует, чтобы:

1. `<form>` был отрендерен [серверным компонентом](/reference/rsc/use-client)
1. функция, переданная в проп `action` `<form>`, была [серверной функцией](/reference/rsc/server-functions)
1. хук `useActionState` использовался для отображения сообщения об ошибке

`useActionState` принимает два параметра: [серверную функцию](/reference/rsc/server-functions) и начальное состояние. `useActionState` возвращает два значения: переменную состояния и действие. Действие, возвращаемое `useActionState`, должно быть передано в проп `action` формы. Переменная состояния, возвращаемая `useActionState`, может использоваться для отображения сообщения об ошибке. Значение, возвращаемое серверной функцией, переданной в `useActionState`, будет использоваться для обновления переменной состояния.

<Sandpack>

```js src/App.js
import { useActionState } from "react";
import { signUpNewUser } from "./api";

export default function Page() {
  async function signup(prevState, formData) {
    "use server";
    const email = formData.get("email");
    try {
      await signUpNewUser(email);
      alert(`Добавлено "${email}"`);
    } catch (err) {
      return err.toString();
    }
  }
  const [message, signupAction] = useActionState(signup, null);
  return (
    <>
      <h1>Зарегистрируйтесь для получения моей рассылки</h1>
      <p>Зарегистрируйтесь с одним и тем же адресом электронной почты дважды, чтобы увидеть ошибку</p>
      <form action={signupAction} id="signup-form">
        <label htmlFor="email">Электронная почта: </label>
        <input name="email" id="email" placeholder="react@example.com" />
        <button>Зарегистрироваться</button>
        {!!message && <p>{message}</p>}
      </form>
    </>
  );
}
```

```js src/api.js hidden
let emails = [];

export async function signUpNewUser(newEmail) {
  if (emails.includes(newEmail)) {
    throw new Error("Этот адрес электронной почты уже добавлен");
  }
  emails.push(newEmail);
}
```

</Sandpack>

Узнайте больше об обновлении состояния из действия формы с помощью документации [`useActionState`](/reference/react/useActionState)

### Обработка нескольких типов отправки {/*handling-multiple-submission-types*/}

Формы могут быть разработаны для обработки нескольких действий отправки в зависимости от кнопки, нажатой пользователем. Каждая кнопка внутри формы может быть связана с отдельным действием или поведением, установив проп `formAction`.

Когда пользователь нажимает определенную кнопку, форма отправляется, и выполняется соответствующее действие, определенное атрибутами и действием этой кнопки. Например, форма может по умолчанию отправлять статью на рассмотрение, но иметь отдельную кнопку с установленным `formAction` для сохранения статьи в качестве черновика.

<Sandpack>

```js src/App.js
export default function Search() {
  function publish(formData) {
    const content = formData.get("content");
    const button = formData.get("button");
    alert(`'${content}' был опубликован с помощью кнопки '${button}'`);
  }

  function save(formData) {
    const content = formData.get("content");
    alert(`Ваш черновик '${content}' был сохранён!`);
  }

  return (
    <form action={publish}>
      <textarea name="content" rows={4} cols={40} />
      <br />
      <button type="submit" name="button" value="submit">Опубликовать</button>
      <button formAction={save}>Сохранить черновик</button>
    </form>
  );
}
```

</Sandpack>
```