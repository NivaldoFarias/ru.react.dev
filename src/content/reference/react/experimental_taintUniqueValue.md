<Experimental>

**Эта функция является экспериментальной и пока недоступна в стабильной версии React.**

Вы можете попробовать её, обновив пакеты React до последней экспериментальной версии:

- `react@experimental`
- `react-dom@experimental`
- `eslint-plugin-react-hooks@experimental`

Экспериментальные версии React могут содержать ошибки. Не используйте их в продакшене.

Эта функция доступна только внутри [React Server Components](/reference/rsc/use-client).

</Experimental>


<Intro>

`taintUniqueValue` позволяет предотвратить передачу уникальных значений, таких как пароли, ключи или токены, в Client Components.

```js
taintUniqueValue(errMessage, lifetime, value)
```

Чтобы предотвратить передачу объекта, содержащего конфиденциальные данные, см. [`taintObjectReference`](/reference/react/experimental_taintObjectReference).

</Intro>

<InlineToc />

---

## Справочник {/*reference*/}

### `taintUniqueValue(message, lifetime, value)` {/*taintuniquevalue*/}

Вызовите `taintUniqueValue` с паролем, токеном, ключом или хешем, чтобы зарегистрировать его в React как значение, которое не должно быть передано клиенту в исходном виде:

```js
import {experimental_taintUniqueValue} from 'react';

experimental_taintUniqueValue(
  'Do not pass secret keys to the client.',
  process,
  process.env.SECRET_KEY
);
```

[См. больше примеров ниже.](#usage)

#### Параметры {/*parameters*/}

* `message`: Сообщение, которое вы хотите отобразить, если `value` будет передано в Client Component. Это сообщение будет частью ошибки, которая будет выброшена, если `value` будет передано в Client Component.

* `lifetime`: Любой объект, указывающий, как долго `value` будет считаться "загрязнённым" (tainted). `value` будет заблокировано от отправки в любой Client Component, пока этот объект существует. Например, передача `globalThis` блокирует значение на время жизни приложения. `lifetime` обычно является объектом, свойства которого содержат `value`.

* `value`: Строка, bigint или TypedArray. `value` должно быть уникальной последовательностью символов или байтов с высокой энтропией, такой как криптографический токен, закрытый ключ, хеш или длинный пароль. `value` будет заблокировано от отправки в любой Client Component.

#### Возвращает {/*returns*/}

`experimental_taintUniqueValue` возвращает `undefined`.

#### Ограничения {/*caveats*/}

* Получение новых значений из "загрязнённых" значений может скомпрометировать защиту. Новые значения, созданные путём преобразования "загрязнённых" значений в верхний регистр, конкатенации "загрязнённых" строковых значений в более крупную строку, преобразования "загрязнённых" значений в base64, извлечения подстроки из "загрязнённых" значений и других подобных преобразований, не будут считаться "загрязнёнными", если вы явно не вызовете `taintUniqueValue` для этих вновь созданных значений.
* Не используйте `taintUniqueValue` для защиты значений с низкой энтропией, таких как PIN-коды или номера телефонов. Если любое значение в запросе контролируется злоумышленником, он сможет определить, какое значение "загрязнено", перечислив все возможные значения секрета.

---

## Использование {/*usage*/}

### Предотвращение передачи токена в Client Components {/*prevent-a-token-from-being-passed-to-client-components*/}

Чтобы гарантировать, что конфиденциальная информация, такая как пароли, токены сеанса или другие уникальные значения, случайно не передаются в Client Components, функция `taintUniqueValue` обеспечивает уровень защиты. Когда значение "загрязнено", любая попытка передать его в Client Component приведет к ошибке.

Аргумент `lifetime` определяет период времени, в течение которого значение остается "загрязнённым". Для значений, которые должны оставаться "загрязнёнными" неопределенно долго, в качестве аргумента `lifetime` могут выступать такие объекты, как [`globalThis`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/globalThis) или `process`. Эти объекты имеют время жизни, охватывающее весь период выполнения вашего приложения.

```js
import {experimental_taintUniqueValue} from 'react';

experimental_taintUniqueValue(
  'Do not pass a user password to the client.',
  globalThis,
  process.env.SECRET_KEY
);
```

Если время жизни "загрязнённого" значения связано с объектом, `lifetime` должен быть объектом, который инкапсулирует это значение. Это гарантирует, что "загрязнённое" значение останется защищённым в течение времени жизни инкапсулирующего объекта.

```js
import {experimental_taintUniqueValue} from 'react';

export async function getUser(id) {
  const user = await db`SELECT * FROM users WHERE id = ${id}`;
  experimental_taintUniqueValue(
    'Do not pass a user session token to the client.',
    user,
    user.session.token
  );
  return user;
}
```

В этом примере объект `user` выступает в качестве аргумента `lifetime`. Если этот объект будет сохранен в глобальном кеше или будет доступен другому запросу, токен сеанса останется "загрязнённым".

<Pitfall>

**Не полагайтесь исключительно на "загрязнение" для обеспечения безопасности.** "Загрязнение" значения не блокирует все возможные производные значения. Например, создание нового значения путём преобразования "загрязнённой" строки в верхний регистр не "загрязняет" новое значение.


```js
import {experimental_taintUniqueValue} from 'react';

const password = 'correct horse battery staple';

experimental_taintUniqueValue(
  'Do not pass the password to the client.',
  globalThis,
  password
);

const uppercasePassword = password.toUpperCase() // `uppercasePassword` не "загрязнён"
```

В этом примере константа `password` "загрязнена". Затем `password` используется для создания нового значения `uppercasePassword` путём вызова метода `toUpperCase` для `password`. Вновь созданное значение `uppercasePassword` не "загрязнено".

Другие подобные способы получения новых значений из "загрязнённых" значений, такие как конкатенация в более крупную строку, преобразование в base64 или возврат подстроки, создают незагрязнённые значения.

"Загрязнение" защищает только от простых ошибок, таких как явная передача секретных значений клиенту. Ошибки при вызове `taintUniqueValue`, такие как использование глобального хранилища вне React, без соответствующего объекта времени жизни, могут привести к тому, что "загрязнённое" значение станет незагрязнённым. "Загрязнение" — это уровень защиты; безопасное приложение будет иметь несколько уровней защиты, хорошо спроектированные API и шаблоны изоляции.

</Pitfall>

<DeepDive>

#### Использование `server-only` и `taintUniqueValue` для предотвращения утечки секретов {/*using-server-only-and-taintuniquevalue-to-prevent-leaking-secrets*/}

Если вы используете среду Server Components, имеющую доступ к закрытым ключам или паролям, таким как пароли баз данных, вы должны быть осторожны, чтобы не передавать их в Client Component.

```js
export async function Dashboard(props) {
  // НЕ ДЕЛАЙТЕ ТАК
  return <Overview password={process.env.API_PASSWORD} />;
}
```

```js
"use client";

import {useEffect} from '...'

export async function Overview({ password }) {
  useEffect(() => {
    const headers = { Authorization: password };
    fetch(url, { headers }).then(...);
  }, [password]);
  ...
}
```

Этот пример приведет к утечке секретного API-токена клиенту. Если этот API-токен может быть использован для доступа к данным, к которым данный пользователь не должен иметь доступа, это может привести к утечке данных.

[comment]: <> (TODO: Добавить ссылку на документацию `server-only`, как только она будет написана)

В идеале, секреты, подобные этому, должны быть абстрагированы в отдельный вспомогательный файл, который может быть импортирован только доверенными утилитами данных на сервере. Вспомогательный файл может быть даже помечен как [`server-only`](https://www.npmjs.com/package/server-only), чтобы гарантировать, что этот файл не будет импортирован на клиенте.

```js
import "server-only";

export function fetchAPI(url) {
  const headers = { Authorization: process.env.API_PASSWORD };
  return fetch(url, { headers });
}
```

Иногда при рефакторинге случаются ошибки, и не все ваши коллеги могут об этом знать.
Чтобы защититься от таких ошибок в будущем, мы можем "загрязнить" фактический пароль:

```js
import "server-only";
import {experimental_taintUniqueValue} from 'react';

experimental_taintUniqueValue(
  'Do not pass the API token password to the client. ' +
    'Instead do all fetches on the server.'
  process,
  process.env.API_PASSWORD
);
```

Теперь всякий раз, когда кто-то попытается передать этот пароль в Client Component или отправить пароль в Client Component с помощью Server Function, будет выброшена ошибка с сообщением, которое вы определили при вызове `taintUniqueValue`.

</DeepDive>

---