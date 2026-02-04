---
title: Постановка в очередь нескольких обновлений состояния
---

<Intro>

Установка переменной состояния вызывает повторный рендеринг. Но иногда вам может понадобиться выполнить несколько операций со значением перед постановкой в очередь следующего рендеринга. Для этого полезно понять, как React группирует обновления состояния.

</Intro>

<YouWillLearn>

* Что такое "группировка" и как React использует её для обработки нескольких обновлений состояния
* Как применить несколько обновлений к одной и той же переменной состояния подряд

</YouWillLearn>

## React группирует обновления состояния {/*react-batches-state-updates*/}

Вы можете ожидать, что нажатие кнопки "+3" увеличит счётчик три раза, поскольку она трижды вызывает `setNumber(number + 1)`:

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 1);
        setNumber(number + 1);
        setNumber(number + 1);
      }}>+3</button>
    </>
  )
}
```

```css
button { display: inline-block; margin: 10px; font-size: 20px; }
h1 { display: inline-block; margin: 10px; width: 30px; text-align: center; }
```

</Sandpack>

Однако, как вы могли вспомнить из предыдущего раздела, [значения состояния каждого рендеринга фиксированы](/learn/state-as-a-snapshot#rendering-takes-a-snapshot-in-time), поэтому значение `number` внутри обработчика событий первого рендеринга всегда равно `0`, независимо от того, сколько раз вы вызываете `setNumber(1)`:

```js
setNumber(0 + 1);
setNumber(0 + 1);
setNumber(0 + 1);
```

Но здесь действует ещё один фактор. **React ждёт, пока весь код в обработчиках событий не будет выполнен, прежде чем обрабатывать ваши обновления состояния.** Вот почему повторный рендеринг происходит только *после* всех этих вызовов `setNumber()`.

Это может напомнить вам официанта, принимающего заказ в ресторане. Официант не бежит на кухню при упоминании вашего первого блюда! Вместо этого он позволяет вам закончить заказ, внести в него изменения и даже принять заказы от других людей за столом.

<Illustration src="/images/docs/illustrations/i_react-batching.png"  alt="Элегантный курсор в ресторане несколько раз делает заказ у React, играющего роль официанта. После того как она несколько раз вызывает setState(), официант записывает последнее запрошенное ею как её финальный заказ." />

Это позволяет вам обновлять несколько переменных состояния — даже из нескольких компонентов — без запуска слишком большого количества [повторных рендерингов](/learn/render-and-commit#re-renders-when-state-updates). Но это также означает, что пользовательский интерфейс не будет обновлён до тех пор, пока ваш обработчик событий и весь код в нём не завершатся. Это поведение, также известное как **группировка**, делает ваше React-приложение намного быстрее. Оно также позволяет избежать путаницы с "незаконченными" рендерингами, когда обновлены только некоторые переменные.

**React не группирует обновления между *несколькими* намеренными событиями, такими как клики** — каждый клик обрабатывается отдельно. Будьте уверены, что React выполняет группировку только тогда, когда это безопасно. Это гарантирует, что, например, если первый клик по кнопке отключает форму, второй клик не отправит её снова.

## Многократное обновление одного и того же состояния перед следующим рендером {/*updating-the-same-state-multiple-times-before-the-next-render*/}

Это редкий сценарий, но если вы хотите обновить одну и ту же переменную состояния несколько раз перед следующим рендером, вместо передачи *следующего значения состояния* как `setNumber(number + 1)`, вы можете передать *функцию*, которая вычисляет следующее состояние на основе предыдущего в очереди, например `setNumber(n => n + 1)`. Это способ сказать React "сделай что-нибудь со значением состояния", а не просто заменить его.

Попробуйте увеличить счётчик сейчас:

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(n => n + 1);
        setNumber(n => n + 1);
        setNumber(n => n + 1);
      }}>+3</button>
    </>
  )
}
```

```css
button { display: inline-block; margin: 10px; font-size: 20px; }
h1 { display: inline-block; margin: 10px; width: 30px; text-align: center; }
```

</Sandpack>

Здесь `n => n + 1` называется **функцией обновления**. Когда вы передаёте её установщику состояния:

1. React ставит эту функцию в очередь для обработки после выполнения всего остального кода в обработчике события.
2. Во время следующего рендера React проходит по очереди и выдаёт вам окончательное обновлённое состояние.

```js
setNumber(n => n + 1);
setNumber(n => n + 1);
setNumber(n => n + 1);
```

Вот как React обрабатывает эти строки кода при выполнении обработчика события:

1. `setNumber(n => n + 1)`: `n => n + 1` — это функция. React добавляет её в очередь.
1. `setNumber(n => n + 1)`: `n => n + 1` — это функция. React добавляет её в очередь.
1. `setNumber(n => n + 1)`: `n => n + 1` — это функция. React добавляет её в очередь.

Когда вы вызываете `useState` во время следующего рендера, React проходит по очереди. Предыдущее состояние `number` было `0`, поэтому именно это React передаёт первой функции обновления в качестве аргумента `n`. Затем React берёт возвращаемое значение вашей предыдущей функции обновления и передаёт его следующей функции обновления как `n`, и так далее:

|   очередное обновление | `n` | возвращает |
|--------------|---------|-----|
| `n => n + 1` | `0` | `0 + 1 = 1` |
| `n => n + 1` | `1` | `1 + 1 = 2` |
| `n => n + 1` | `2` | `2 + 1 = 3` |

React сохраняет `3` как окончательный результат и возвращает его из `useState`.

Именно поэтому нажатие на "+3" в примере выше корректно увеличивает значение на 3.
### Что происходит, если вы обновляете состояние после его замены {/*what-happens-if-you-update-state-after-replacing-it*/}

А как насчёт этого обработчика события? Как вы думаете, каким будет `number` при следующем рендере?

```js
<button onClick={() => {
  setNumber(number + 5);
  setNumber(n => n + 1);
}}>
```

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 5);
        setNumber(n => n + 1);
      }}>Увеличить число</button>
    </>
  )
}
```

```css
button { display: inline-block; margin: 10px; font-size: 20px; }
h1 { display: inline-block; margin: 10px; width: 30px; text-align: center; }
```

</Sandpack>

Вот что этот обработчик события говорит React сделать:

1. `setNumber(number + 5)`: `number` равно `0`, поэтому `setNumber(0 + 5)`. React добавляет в очередь "заменить на `5`".
2. `setNumber(n => n + 1)`: `n => n + 1` — это функция обновления. React добавляет *эту функцию* в очередь.

Во время следующего рендера React проходит по очереди состояний:

|   очередное обновление | `n` | возвращает |
|--------------|---------|-----|
| "заменить на `5`" | `0` (не используется) | `5` |
| `n => n + 1` | `5` | `5 + 1 = 6` |

React сохраняет `6` как окончательный результат и возвращает его из `useState`.

<Note>

Вы могли заметить, что `setState(5)` на самом деле работает как `setState(n => 5)`, но `n` не используется!

</Note>

### Что происходит, если вы заменяете состояние после его обновления {/*what-happens-if-you-replace-state-after-updating-it*/}

Давайте рассмотрим ещё один пример. Как вы думаете, каким будет `number` при следующем рендере?

```js
<button onClick={() => {
  setNumber(number + 5);
  setNumber(n => n + 1);
  setNumber(42);
}}>
```

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 5);
        setNumber(n => n + 1);
        setNumber(42);
      }}>Увеличить число</button>
    </>
  )
}
```

```css
button { display: inline-block; margin: 10px; font-size: 20px; }
h1 { display: inline-block; margin: 10px; width: 30px; text-align: center; }
```

</Sandpack>

Вот как React обрабатывает эти строки кода при выполнении этого обработчика события:

1. `setNumber(number + 5)`: `number` равно `0`, поэтому `setNumber(0 + 5)`. React добавляет в очередь "заменить на `5`".
2. `setNumber(n => n + 1)`: `n => n + 1` — это функция обновления. React добавляет *эту функцию* в очередь.
3. `setNumber(42)`: React добавляет в очередь "заменить на `42`".

Во время следующего рендера React проходит по очереди состояний:

|   очередное обновление | `n` | возвращает |
|--------------|---------|-----|
| "заменить на `5`" | `0` (не используется) | `5` |
| `n => n + 1` | `5` | `5 + 1 = 6` |
| "заменить на `42`" | `6` (не используется) | `42` |

Затем React сохраняет `42` как окончательный результат и возвращает его из `useState`.

Таким образом, вот как вы можете думать о том, что вы передаёте установщику состояния `setNumber`:

* **Функция обновления** (например, `n => n + 1`) добавляется в очередь.
* **Любое другое значение** (например, число `5`) добавляет в очередь "заменить на `5`", игнорируя всё, что уже находится в очереди.

После завершения обработчика события React вызовет повторный рендер. Во время повторного рендера React обработает очередь. Функции обновления выполняются во время рендеринга, поэтому **функции обновления должны быть [чистыми](/learn/keeping-components-pure)** и только *возвращать* результат. Не пытайтесь устанавливать состояние внутри них или выполнять другие побочные эффекты. В Strict Mode React будет запускать каждую функцию обновления дважды (но отбрасывать второй результат), чтобы помочь вам найти ошибки.

### Соглашения об именовании {/*naming-conventions*/}

Часто имя аргумента функции обновления называют по первым буквам соответствующей переменной состояния:

```js
setEnabled(e => !e);
setLastName(ln => ln.reverse());
setFriendCount(fc => fc * 2);
```

Если вы предпочитаете более многословный код, другое распространённое соглашение — повторить полное имя переменной состояния, например `setEnabled(enabled => !enabled)`, или использовать префикс, например `setEnabled(prevEnabled => !prevEnabled)`.

<Recap>

* Установка состояния не изменяет переменную в текущем рендере, но запрашивает новый рендер.
* React обрабатывает обновления состояния после завершения обработчиков событий. Это называется группировкой.
* Чтобы несколько раз обновить состояние за одно событие, можно использовать функцию обновления `setNumber(n => n + 1)`.

</Recap>



<Challenges>

#### Исправьте счётчик запросов {/*fix-a-request-counter*/}

Вы работаете над приложением арт-маркетплейса, которое позволяет пользователю одновременно отправлять несколько заказов на арт-объект. Каждый раз, когда пользователь нажимает кнопку "Купить", счётчик "В ожидании" должен увеличиваться на единицу. Через три секунды счётчик "В ожидании" должен уменьшиться, а счётчик "Завершено" — увеличиться.

Однако счётчик "В ожидании" ведёт себя не так, как ожидалось. Когда вы нажимаете "Купить", он уменьшается до `-1` (что не должно быть возможно!). И если быстро нажать дважды, оба счётчика ведут себя непредсказуемо.

Почему это происходит? Исправьте оба счётчика.

<Sandpack>

```js
import { useState } from 'react';

export default function RequestTracker() {
  const [pending, setPending] = useState(0);
  const [completed, setCompleted] = useState(0);

  async function handleClick() {
    setPending(pending + 1);
    await delay(3000);
    setPending(pending - 1);
    setCompleted(completed + 1);
  }

  return (
    <>
      <h3>
        Pending: {pending}
      </h3>
      <h3>
        Completed: {completed}
      </h3>
      <button onClick={handleClick}>
        Buy     
      </button>
    </>
  );
}

function delay(ms) {
  return new Promise(resolve => {
    setTimeout(resolve, ms);
  });
}
```

</Sandpack>

<Solution>

Внутри обработчика события `handleClick` значения `pending` и `completed` соответствуют тем, которые были на момент события клика. Для первого рендера `pending` было `0`, поэтому `setPending(pending - 1)` становится `setPending(-1)`, что неверно. Поскольку вы хотите *увеличить* или *уменьшить* счётчики, а не установить их в конкретное значение, определённое во время клика, вы можете вместо этого передать функции обновления:

<Sandpack>

```js
import { useState } from 'react';

export default function RequestTracker() {
  const [pending, setPending] = useState(0);
  const [completed, setCompleted] = useState(0);

  async function handleClick() {
    setPending(p => p + 1);
    await delay(3000);
    setPending(p => p - 1);
    setCompleted(c => c + 1);
  }

  return (
    <>
      <h3>
        Pending: {pending}
      </h3>
      <h3>
        Completed: {completed}
      </h3>
      <button onClick={handleClick}>
        Buy     
      </button>
    </>
  );
}

function delay(ms) {
  return new Promise(resolve => {
    setTimeout(resolve, ms);
  });
}
```

</Sandpack>

Это гарантирует, что при увеличении или уменьшении счётчика вы делаете это по отношению к его *последнему* состоянию, а не к тому, которое было на момент клика.

</Solution>

#### Реализуйте очередь состояний самостоятельно {/*implement-the-state-queue-yourself*/}

В этом задании вы перепишете крошечную часть React с нуля! Это не так сложно, как кажется.

Прокрутите предварительный просмотр песочницы. Обратите внимание, что он показывает **четыре тестовых случая**. Они соответствуют примерам, которые вы видели ранее на этой странице. Ваша задача — реализовать функцию `getFinalState`, чтобы она возвращала правильный результат для каждого из этих случаев. Если вы реализуете её правильно, все четыре теста пройдут.

Вы получите два аргумента: `baseState` — начальное состояние (например, `0`), а `queue` — массив, содержащий смесь чисел (например, `5`) и функций обновления (например, `n => n + 1`) в порядке их добавления.

Ваша задача — вернуть конечное состояние, как показано в таблицах на этой странице!

<Hint>

Если вы застряли, начните с этой структуры кода:

```js
export function getFinalState(baseState, queue) {
  let finalState = baseState;

  for (let update of queue) {
    if (typeof update === 'function') {
      // TODO: применить функцию обновления
    } else {
      // TODO: заменить состояние
    }
  }

  return finalState;
}
```

Заполните недостающие строки!

</Hint>

<Sandpack>

```js src/processQueue.js active
export function getFinalState(baseState, queue) {
  let finalState = baseState;

  // TODO: сделать что-нибудь с очередью...

  return finalState;
}
```

```js src/App.js
import { getFinalState } from './processQueue.js';

function increment(n) {
  return n + 1;
}
increment.toString = () => 'n => n+1';

export default function App() {
  return (
    <>
      <TestCase
        baseState={0}
        queue={[1, 1, 1]}
        expected={1}
      />
      <hr />
      <TestCase
        baseState={0}
        queue={[
          increment,
          increment,
          increment
        ]}
        expected={3}
      />
      <hr />
      <TestCase
        baseState={0}
        queue={[
          5,
          increment,
        ]}
        expected={6}
      />
      <hr />
      <TestCase
        baseState={0}
        queue={[
          5,
          increment,
          42,
        ]}
        expected={42}
      />
    </>
  );
}

function TestCase({
  baseState,
  queue,
  expected
}) {
  const actual = getFinalState(baseState, queue);
  return (
    <>
      <p>Base state: <b>{baseState}</b></p>
      <p>Queue: <b>[{queue.join(', ')}]</b></p>
      <p>Expected result: <b>{expected}</b></p>
      <p style={{
        color: actual === expected ?
          'green' :
          'red'
      }}>
        Your result: <b>{actual}</b>
        {' '}
        ({actual === expected ?
          'correct' :
          'wrong'
        })
      </p>
    </>
  );
}
```

</Sandpack>

<Solution>

Это именно тот алгоритм, который описан на этой странице и который использует React для вычисления конечного состояния:

<Sandpack>

```js src/processQueue.js active
export function getFinalState(baseState, queue) {
  let finalState = baseState;

  for (let update of queue) {
    if (typeof update === 'function') {
      // Применить функцию обновления.
      finalState = update(finalState);
    } else {
      // Заменить следующее состояние.
      finalState = update;
    }
  }

  return finalState;
}
```

```js src/App.js
import { getFinalState } from './processQueue.js';

function increment(n) {
  return n + 1;
}
increment.toString = () => 'n => n+1';

export default function App() {
  return (
    <>
      <TestCase
        baseState={0}
        queue={[1, 1, 1]}
        expected={1}
      />
      <hr />
      <TestCase
        baseState={0}
        queue={[
          increment,
          increment,
          increment
        ]}
        expected={3}
      />
      <hr />
      <TestCase
        baseState={0}
        queue={[
          5,
          increment,
        ]}
        expected={6}
      />
      <hr />
      <TestCase
        baseState={0}
        queue={[
          5,
          increment,
          42,
        ]}
        expected={42}
      />
    </>
  );
}

function TestCase({
  baseState,
  queue,
  expected
}) {
  const actual = getFinalState(baseState, queue);
  return (
    <>
      <p>Base state: <b>{baseState}</b></p>
      <p>Queue: <b>[{queue.join(', ')}]</b></p>
      <p>Expected result: <b>{expected}</b></p>
      <p style={{
        color: actual === expected ?
          'green' :
          'red'
      }}>
        Your result: <b>{actual}</b>
        {' '}
        ({actual === expected ?
          'correct' :
          'wrong'
        })
      </p>
    </>
  );
}
```

</Sandpack>

Теперь вы знаете, как работает эта часть React!

</Solution>

</Challenges>