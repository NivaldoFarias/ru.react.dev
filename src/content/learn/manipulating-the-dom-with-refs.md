---
title: 'Манипулирование DOM с помощью Refs'
---

<Intro>

React автоматически обновляет [DOM](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction), чтобы он соответствовал вашему выводу рендеринга, поэтому ваши компоненты нечасто будут нуждаться в его изменении. Однако иногда вам может понадобиться доступ к DOM-элементам, управляемым React — например, чтобы сфокусироваться на узле, прокрутить его или измерить его размер и положение. В React нет встроенного способа сделать это, поэтому вам понадобится *ref* для DOM-узла.

</Intro>

<YouWillLearn>

- Как получить доступ к DOM-узлу, управляемому React, с помощью атрибута `ref`
- Как атрибут JSX `ref` связан с хуком `useRef`
- Как получить доступ к DOM-узлу другого компонента
- В каких случаях безопасно изменять DOM, управляемый React

</YouWillLearn>

## Получение ref для узла {/*getting-a-ref-to-the-node*/}

Чтобы получить доступ к DOM-узлу, управляемому React, сначала импортируйте хук `useRef`:

```js
import { useRef } from 'react';
```

Затем используйте его для объявления ref внутри вашего компонента:

```js
const myRef = useRef(null);
```

Наконец, передайте ваш ref в качестве атрибута `ref` в JSX-тег, для которого вы хотите получить DOM-узел:

```js
<div ref={myRef}>
```

Хук `useRef` возвращает объект с одним свойством `current`. Изначально `myRef.current` будет `null`. Когда React создаст DOM-узел для этого `<div>`, React поместит ссылку на этот узел в `myRef.current`. Затем вы сможете получить доступ к этому DOM-узлу из ваших [обработчиков событий](/learn/responding-to-events) и использовать встроенные [API браузера](https://developer.mozilla.org/docs/Web/API/Element), определенные в нем.

```js
// Вы можете использовать любые API браузера, например:
myRef.current.scrollIntoView();
```

### Пример: Фокусировка на текстовом поле ввода {/*example-focusing-a-text-input*/}

В этом примере нажатие на кнопку сфокусирует поле ввода:

<Sandpack>

```js
import { useRef } from 'react';

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <input ref={inputRef} />
      <button onClick={handleClick}>
        Focus the input
      </button>
    </>
  );
}
```

</Sandpack>

Чтобы реализовать это:

1. Объявите `inputRef` с помощью хука `useRef`.
2. Передайте его как `<input ref={inputRef}>`. Это говорит React **поместить DOM-узел этого `<input>` в `inputRef.current`.**
3. В функции `handleClick` прочитайте DOM-узел ввода из `inputRef.current` и вызовите на нем [`focus()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/focus) с помощью `inputRef.current.focus()`.
4. Передайте обработчик события `handleClick` в `<button>` с помощью `onClick`.

Хотя манипулирование DOM является наиболее распространенным сценарием использования refs, хук `useRef` может использоваться для хранения других вещей вне React, таких как идентификаторы таймеров. Подобно состоянию, refs сохраняются между рендерами. Refs похожи на переменные состояния, которые не вызывают повторных рендеров при их установке. Прочтите о refs в разделе [Ссылки на значения с помощью Refs.](/learn/referencing-values-with-refs)

### Пример: Прокрутка к элементу {/*example-scrolling-to-an-element*/}

В компоненте может быть более одного ref. В этом примере представлен карусель из трех изображений. Каждая кнопка центрирует изображение, вызывая метод браузера [`scrollIntoView()`](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView) для соответствующего DOM-узла:

<Sandpack>

```js
import { useRef } from 'react';

export default function CatFriends() {
  const firstCatRef = useRef(null);
  const secondCatRef = useRef(null);
  const thirdCatRef = useRef(null);

  function handleScrollToFirstCat() {
    firstCatRef.current.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center'
    });
  }

  function handleScrollToSecondCat() {
    secondCatRef.current.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center'
    });
  }

  function handleScrollToThirdCat() {
    thirdCatRef.current.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center'
    });
  }

  return (
    <>
      <nav>
        <button onClick={handleScrollToFirstCat}>
          Neo
        </button>
        <button onClick={handleScrollToSecondCat}>
          Millie
        </button>
        <button onClick={handleScrollToThirdCat}>
          Bella
        </button>
      </nav>
      <div>
        <ul>
          <li>
            <img
              src="https://placecats.com/neo/300/200"
              alt="Neo"
              ref={firstCatRef}
            />
          </li>
          <li>
            <img
              src="https://placecats.com/millie/200/200"
              alt="Millie"
              ref={secondCatRef}
            />
          </li>
          <li>
            <img
              src="https://placecats.com/bella/199/200"
              alt="Bella"
              ref={thirdCatRef}
            />
          </li>
        </ul>
      </div>
    </>
  );
}
```

```css
div {
  width: 100%;
  overflow: hidden;
}

nav {
  text-align: center;
}

button {
  margin: .25rem;
}

ul,
li {
  list-style: none;
  white-space: nowrap;
}

li {
  display: inline;
  padding: 0.5rem;
}
```

</Sandpack>

<DeepDive>

#### Как управлять списком refs с помощью callback-функции для ref {/*how-to-manage-a-list-of-refs-using-a-ref-callback*/}

В приведенных выше примерах количество refs заранее определено. Однако иногда вам может понадобиться ref для каждого элемента в списке, и вы не знаете, сколько их будет. Что-то вроде этого **не сработает**:

```js
<ul>
  {items.map((item) => {
    // Не работает!
    const ref = useRef(null);
    return <li ref={ref} />;
  })}
</ul>
```

Это потому, что **хуки можно вызывать только на верхнем уровне вашего компонента.** Вы не можете вызывать `useRef` в цикле, в условии или внутри вызова `map()`.

Один из возможных обходных путей — получить один ref для родительского элемента, а затем использовать методы манипулирования DOM, такие как [`querySelectorAll`](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelectorAll), чтобы «найти» отдельные дочерние узлы. Однако это хрупко и может сломаться, если ваша структура DOM изменится.

Другое решение — **передать функцию в атрибут `ref`.** Это называется [`ref` callback](/reference/react-dom/components/common#ref-callback). React будет вызывать вашу callback-функцию для ref с DOM-узлом, когда придет время установить ref, и с `null`, когда придет время его очистить. Это позволяет вам поддерживать собственный массив или [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) и получать доступ к любому ref по его индексу или некоторому идентификатору.

Этот пример показывает, как вы можете использовать этот подход для прокрутки к произвольному узлу в длинном списке:

<Sandpack>

```js
import { useRef, useState } from "react";

export default function CatFriends() {
  const itemsRef = useRef(null);
  const [catList, setCatList] = useState(setupCatList);

  function scrollToCat(cat) {
    const map = getMap();
    const node = map.get(cat);
    node.scrollIntoView({
      behavior: "smooth",
      block: "nearest",
      inline: "center",
    });
  }

  function getMap() {
    if (!itemsRef.current) {
      // Инициализация Map при первом использовании.
      itemsRef.current = new Map();
    }
    return itemsRef.current;
  }

  return (
    <>
      <nav>
        <button onClick={() => scrollToCat(catList[0])}>Neo</button>
        <button onClick={() => scrollToCat(catList[5])}>Millie</button>
        <button onClick={() => scrollToCat(catList[9])}>Bella</button>
      </nav>
      <div>
        <ul>
          {catList.map((cat) => (
            <li
              key={cat}
              ref={(node) => {
                const map = getMap();
                map.set(cat, node);

                return () => {
                  map.delete(cat);
                };
              }}
            >
              <img src={cat} />
            </li>
          ))}
        </ul>
      </div>
    </>
  );
}

function setupCatList() {
  const catList = [];
  for (let i = 0; i < 10; i++) {
    catList.push("https://loremflickr.com/320/240/cat?lock=" + i);
  }

  return catList;
}

```

```css
div {
  width: 100%;
  overflow: hidden;
}

nav {
  text-align: center;
}

button {
  margin: .25rem;
}

ul,
li {
  list-style: none;
  white-space: nowrap;
}

li {
  display: inline;
  padding: 0.5rem;
}
```

</Sandpack>

В этом примере `itemsRef` не хранит один DOM-узел. Вместо этого он хранит [Map](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Map) от идентификатора элемента к DOM-узлу. ([Refs могут хранить любые значения!](/learn/referencing-values-with-refs)) [`ref` callback](/reference/react-dom/components/common#ref-callback) для каждого элемента списка заботится об обновлении Map:

```js
<li
  key={cat.id}
  ref={node => {
    const map = getMap();
    // Добавление в Map
    map.set(cat, node);

    return () => {
      // Удаление из Map
      map.delete(cat);
    };
  }}
>
```

Это позволяет вам позже считывать отдельные DOM-узлы из Map.

<Note>

Когда включен Strict Mode, callback-функции для refs будут запускаться дважды в режиме разработки.

Узнайте больше о том, [как это помогает находить ошибки](/reference/react/StrictMode#fixing-bugs-found-by-re-running-ref-callbacks-in-development) в callback-функциях refs.

</Note>

</DeepDive>

## Доступ к DOM-узлам другого компонента {/*accessing-another-components-dom-nodes*/}

<Pitfall>
Refs — это «запасной выход». Ручное манипулирование DOM-узлами _другого_ компонента может сделать ваш код хрупким.
</Pitfall>

Вы можете передавать refs из родительского компонента в дочерние [так же, как и любые другие пропсы](/learn/passing-props-to-a-component).

```js {3-4,9}
import { useRef } from 'react';

function MyInput({ ref }) {
  return <input ref={ref} />;
}

function MyForm() {
  const inputRef = useRef(null);
  return <MyInput ref={inputRef} />
}
```

В приведенном выше примере ref создается в родительском компоненте `MyForm` и передается в дочерний компонент `MyInput`. Затем `MyInput` передает ref в `<input>`. Поскольку `<input>` является [встроенным компонентом](/reference/react-dom/components/common), React устанавливает свойство `.current` ref в DOM-элемент `<input>`.

`inputRef`, созданный в `MyForm`, теперь указывает на DOM-элемент `<input>`, возвращаемый `MyInput`. Обработчик клика, созданный в `MyForm`, может получить доступ к `inputRef` и вызвать `focus()`, чтобы установить фокус на `<input>`.

<Sandpack>

```js
import { useRef } from 'react';

function MyInput({ ref }) {
  return <input ref={ref} />;
}

export default function MyForm() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <MyInput ref={inputRef} />
      <button onClick={handleClick}>
        Focus the input
      </button>
    </>
  );
}
```

</Sandpack>

<DeepDive>

#### Предоставление подмножества API с помощью императивного обработчика {/*exposing-a-subset-of-the-api-with-an-imperative-handle*/}

В приведенном выше примере ref, переданный в `MyInput`, передается дальше оригинальному DOM-элементу ввода. Это позволяет родительскому компоненту вызывать на нем `focus()`. Однако это также позволяет родительскому компоненту делать что-то еще — например, изменять его CSS-стили. В редких случаях вы можете захотеть ограничить раскрываемую функциональность. Вы можете сделать это с помощью [`useImperativeHandle`](/reference/react/useImperativeHandle):

<Sandpack>

```js
import { useRef, useImperativeHandle } from "react";

function MyInput({ ref }) {
  const realInputRef = useRef(null);
  useImperativeHandle(ref, () => ({
    // Предоставляем только focus и ничего больше
    focus() {
      realInputRef.current.focus();
    },
  }));
  return <input ref={realInputRef} />;
};

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <MyInput ref={inputRef} />
      <button onClick={handleClick}>Focus the input</button>
    </>
  );
}
```

</Sandpack>

Здесь `realInputRef` внутри `MyInput` хранит фактический DOM-узел ввода. Однако [`useImperativeHandle`](/reference/react/useImperativeHandle) инструктирует React предоставить ваш собственный специальный объект в качестве значения ref родительскому компоненту. Таким образом, `inputRef.current` внутри компонента `Form` будет иметь только метод `focus`. В этом случае "обработчик" ref — это не DOM-узел, а пользовательский объект, который вы создаете внутри вызова [`useImperativeHandle`](/reference/react/useImperativeHandle).

</DeepDive>

## Когда React устанавливает рефы {/*when-react-attaches-the-refs*/}

В React каждое обновление делится на [две фазы](/learn/render-and-commit#step-3-react-commits-changes-to-the-dom):

* Во время **рендера** React вызывает ваши компоненты, чтобы определить, что должно быть на экране.
* Во время **коммита** React применяет изменения к DOM.

В целом, вы [не хотите](/learn/referencing-values-with-refs#best-practices-for-refs) обращаться к рефам во время рендеринга. Это касается и рефов, хранящих DOM-узлы. Во время первого рендера DOM-узлы ещё не созданы, поэтому `ref.current` будет `null`. А во время рендеринга обновлений DOM-узлы ещё не обновлены. Так что читать их слишком рано.

React устанавливает `ref.current` во время коммита. Перед обновлением DOM React устанавливает соответствующие значения `ref.current` в `null`. После обновления DOM React немедленно устанавливает их в соответствующие DOM-узлы.

**Обычно вы обращаетесь к рефам из обработчиков событий.** Если вы хотите что-то сделать с рефом, но нет конкретного события, в котором это можно сделать, вам может понадобиться эффект. Мы обсудим эффекты на следующих страницах.

<DeepDive>

#### Синхронное выполнение обновлений состояния с помощью flushSync {/*flushing-state-updates-synchronously-with-flush-sync*/}

Рассмотрим такой код, который добавляет новую задачу и прокручивает экран вниз к последнему дочернему элементу списка. Обратите внимание, как по какой-то причине он всегда прокручивается к задаче, которая была *непосредственно перед* последней добавленной:

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function TodoList() {
  const listRef = useRef(null);
  const [text, setText] = useState('');
  const [todos, setTodos] = useState(
    initialTodos
  );

  function handleAdd() {
    const newTodo = { id: nextId++, text: text };
    setText('');
    setTodos([ ...todos, newTodo]);
    listRef.current.lastChild.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest'
    });
  }

  return (
    <>
      <button onClick={handleAdd}>
        Add
      </button>
      <input
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <ul ref={listRef}>
        {todos.map(todo => (
          <li key={todo.id}>{todo.text}</li>
        ))}
      </ul>
    </>
  );
}

let nextId = 0;
let initialTodos = [];
for (let i = 0; i < 20; i++) {
  initialTodos.push({
    id: nextId++,
    text: 'Todo #' + (i + 1)
  });
}
```

</Sandpack>

Проблема заключается в этих двух строках:

```js
setTodos([ ...todos, newTodo]);
listRef.current.lastChild.scrollIntoView();
```

В React [обновления состояния ставятся в очередь](/learn/queueing-a-state-update). Обычно это то, что вам нужно. Однако здесь это вызывает проблему, потому что `setTodos` не обновляет DOM немедленно. Поэтому к моменту прокрутки списка к последнему элементу задача ещё не добавлена. Вот почему прокрутка всегда "отстаёт" на один элемент.

Чтобы исправить эту проблему, вы можете принудительно обновить ("сбросить") DOM синхронно. Для этого импортируйте `flushSync` из `react-dom` и **оберните обновление состояния** в вызов `flushSync`:

```js
flushSync(() => {
  setTodos([ ...todos, newTodo]);
});
listRef.current.lastChild.scrollIntoView();
```

Это даст React указание синхронно обновить DOM сразу после выполнения кода, обёрнутого в `flushSync`. В результате последняя задача уже будет в DOM к моменту, когда вы попытаетесь прокрутить к ней:

<Sandpack>

```js
import { useState, useRef } from 'react';
import { flushSync } from 'react-dom';

export default function TodoList() {
  const listRef = useRef(null);
  const [text, setText] = useState('');
  const [todos, setTodos] = useState(
    initialTodos
  );

  function handleAdd() {
    const newTodo = { id: nextId++, text: text };
    flushSync(() => {
      setText('');
      setTodos([ ...todos, newTodo]);
    });
    listRef.current.lastChild.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest'
    });
  }

  return (
    <>
      <button onClick={handleAdd}>
        Add
      </button>
      <input
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <ul ref={listRef}>
        {todos.map(todo => (
          <li key={todo.id}>{todo.text}</li>
        ))}
      </ul>
    </>
  );
}

let nextId = 0;
let initialTodos = [];
for (let i = 0; i < 20; i++) {
  initialTodos.push({
    id: nextId++,
    text: 'Todo #' + (i + 1)
  });
}
```

</Sandpack>

</DeepDive>

## Лучшие практики для работы с DOM с помощью refs {/*best-practices-for-dom-manipulation-with-refs*/}

Refs — это «лазейка». Вам следует использовать их только тогда, когда вам нужно «выйти за пределы React». Типичные примеры включают управление фокусом, положением прокрутки или вызов браузерных API, которые React не предоставляет.

Если вы будете придерживаться неразрушающих действий, таких как установка фокуса и прокрутка, вы не столкнётесь с проблемами. Однако, если вы попытаетесь **изменить** DOM вручную, вы рискуете вступить в конфликт с изменениями, которые вносит React.

Чтобы проиллюстрировать эту проблему, этот пример включает приветственное сообщение и две кнопки. Первая кнопка переключает своё присутствие с помощью [условного рендеринга](/learn/conditional-rendering) и [состояния](/learn/state-a-components-memory), как вы обычно делаете в React. Вторая кнопка использует [`remove()` DOM API](https://developer.mozilla.org/en-US/docs/Web/API/Element/remove) для принудительного удаления элемента из DOM вне контроля React.

Попробуйте несколько раз нажать «Toggle with setState». Сообщение должно исчезнуть и появиться снова. Затем нажмите «Remove from the DOM». Это принудительно удалит его. Наконец, нажмите «Toggle with setState»:

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function Counter() {
  const [show, setShow] = useState(true);
  const ref = useRef(null);

  return (
    <div>
      <button
        onClick={() => {
          setShow(!show);
        }}>
        Toggle with setState
      </button>
      <button
        onClick={() => {
          ref.current.remove();
        }}>
        Remove from the DOM
      </button>
      {show && <p ref={ref}>Hello world</p>}
    </div>
  );
}
```

```css
p,
button {
  display: block;
  margin: 10px;
}
```

</Sandpack>

После того как вы вручную удалили DOM-элемент, попытка использовать `setState` для его повторного отображения приведёт к сбою. Это связано с тем, что вы изменили DOM, и React не знает, как правильно продолжать им управлять.

**Избегайте изменения DOM-узлов, управляемых React.** Изменение, добавление дочерних элементов или удаление дочерних элементов из элементов, управляемых React, может привести к несогласованным визуальным результатам или сбоям, подобным описанному выше.

Однако это не означает, что вы не можете этого делать вообще. Это требует осторожности. **Вы можете безопасно изменять части DOM, которые React _не имеет причин_ обновлять.** Например, если какой-то `<div>` всегда пуст в JSX, у React не будет причин трогать список его дочерних элементов. Следовательно, безопасно добавлять или удалять элементы вручную.

<Recap>

- Refs — это общее понятие, но чаще всего вы будете использовать их для хранения DOM-элементов.
- Вы указываете React поместить DOM-узел в `myRef.current`, передавая `<div ref={myRef}>`.
- Обычно вы будете использовать refs для неразрушающих действий, таких как установка фокуса, прокрутка или измерение DOM-элементов.
- Компонент не раскрывает свои DOM-узлы по умолчанию. Вы можете разрешить раскрытие DOM-узла, используя проп `ref`.
- Избегайте изменения DOM-узлов, управляемых React.
- Если вы всё же изменяете DOM-узлы, управляемые React, изменяйте части, которые React не имеет причин обновлять.

</Recap>



<Challenges>

#### Воспроизведение и пауза видео {/*play-and-pause-the-video*/}

В этом примере кнопка переключает переменную состояния, чтобы переключаться между состояниями воспроизведения и паузы. Однако для фактического воспроизведения или приостановки видео простого переключения состояния недостаточно. Вам также нужно вызвать [`play()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/play) и [`pause()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/pause) для DOM-элемента `<video>`. Добавьте к нему ref и заставьте кнопку работать.

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function VideoPlayer() {
  const [isPlaying, setIsPlaying] = useState(false);

  function handleClick() {
    const nextIsPlaying = !isPlaying;
    setIsPlaying(nextIsPlaying);
  }

  return (
    <>
      <button onClick={handleClick}>
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      <video width="250">
        <source
          src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
          type="video/mp4"
        />
      </video>
    </>
  )
}
```

```css
button { display: block; margin-bottom: 20px; }
```

</Sandpack>

Для дополнительного усложнения синхронизируйте кнопку "Play" с состоянием воспроизведения видео, даже если пользователь щелкнет правой кнопкой мыши по видео и запустит его с помощью встроенных элементов управления медиа браузера. Для этого вам может понадобиться прослушивать события `onPlay` и `onPause` на видео.

<Solution>

Объявите ref и поместите его в элемент `<video>`. Затем вызывайте `ref.current.play()` и `ref.current.pause()` в обработчике событий в зависимости от следующего состояния.

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function VideoPlayer() {
  const [isPlaying, setIsPlaying] = useState(false);
  const ref = useRef(null);

  function handleClick() {
    const nextIsPlaying = !isPlaying;
    setIsPlaying(nextIsPlaying);

    if (nextIsPlaying) {
      ref.current.play();
    } else {
      ref.current.pause();
    }
  }

  return (
    <>
      <button onClick={handleClick}>
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      <video
        width="250"
        ref={ref}
        onPlay={() => setIsPlaying(true)}
        onPause={() => setIsPlaying(false)}
      >
        <source
          src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
          type="video/mp4"
        />
      </video>
    </>
  )
}
```

```css
button { display: block; margin-bottom: 20px; }
```

</Sandpack>

Чтобы обрабатывать встроенные элементы управления браузера, вы можете добавить обработчики `onPlay` и `onPause` к элементу `<video>`, а затем вызывать `setIsPlaying` из них. Таким образом, если пользователь запустит видео с помощью элементов управления браузера, состояние будет соответствующим образом скорректировано.

</Solution>

#### Фокусировка поля поиска {/*focus-the-search-field*/}

Сделайте так, чтобы при нажатии кнопки "Search" фокус перемещался в поле ввода.

<Sandpack>

```js
export default function Page() {
  return (
    <>
      <nav>
        <button>Search</button>
      </nav>
      <input
        placeholder="Looking for something?"
      />
    </>
  );
}
```

```css
button { display: block; margin-bottom: 10px; }
```

</Sandpack>

<Solution>

Добавьте ref к полю ввода и вызовите `focus()` для DOM-узла, чтобы установить на него фокус:

<Sandpack>

```js
import { useRef } from 'react';

export default function Page() {
  const inputRef = useRef(null);
  return (
    <>
      <nav>
        <button onClick={() => {
          inputRef.current.focus();
        }}>
          Search
        </button>
      </nav>
      <input
        ref={inputRef}
        placeholder="Looking for something?"
      />
    </>
  );
}
```

```css
button { display: block; margin-bottom: 10px; }
```

</Sandpack>

</Solution>

#### Прокрутка карусели изображений {/*scrolling-an-image-carousel*/}

Эта карусель изображений имеет кнопку "Next", которая переключает активное изображение. Сделайте так, чтобы галерея прокручивалась горизонтально к активному изображению при нажатии. Вам нужно будет вызвать [`scrollIntoView()`](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView) для DOM-узла активного изображения:

```js
node.scrollIntoView({
  behavior: 'smooth',
  block: 'nearest',
  inline: 'center'
});
```

<Hint>

Вам не нужен ref для каждого изображения для этого упражнения. Достаточно иметь ref для текущего активного изображения или для самого списка. Используйте `flushSync`, чтобы убедиться, что DOM обновлён *перед* прокруткой.

</Hint>

<Sandpack>

```js
import { useState } from 'react';

export default function CatFriends() {
  const [index, setIndex] = useState(0);
  return (
    <>
      <nav>
        <button onClick={() => {
          if (index < catList.length - 1) {
            setIndex(index + 1);
          } else {
            setIndex(0);
          }
        }}>
          Next
        </button>
      </nav>
      <div>
        <ul>
          {catList.map((cat, i) => (
            <li key={cat.id}>
              <img
                className={
                  index === i ?
                    'active' :
                    ''
                }
                src={cat.imageUrl}
                alt={'Cat #' + cat.id}
              />
            </li>
          ))}
        </ul>
      </div>
    </>
  );
}

const catList = [];
for (let i = 0; i < 10; i++) {
  catList.push({
    id: i,
    imageUrl: 'https://loremflickr.com/250/200/cat?lock=' + i
  });
}

```

```css
div {
  width: 100%;
  overflow: hidden;
}

nav {
  text-align: center;
}

button {
  margin: .25rem;
}

ul,
li {
  list-style: none;
  white-space: nowrap;
}

li {
  display: inline;
  padding: 0.5rem;
}

img {
  padding: 10px;
  margin: -10px;
  transition: background 0.2s linear;
}

.active {
  background: rgba(0, 100, 150, 0.4);
}
```

</Sandpack>

<Solution>

Вы можете объявить `selectedRef` и затем передавать его условно только текущему изображению:

```js
<li ref={index === i ? selectedRef : null}>
```

Когда `index === i`, что означает, что изображение является выбранным, `<li>` получит `selectedRef`. React позаботится о том, чтобы `selectedRef.current` всегда указывал на правильный DOM-узел.

Обратите внимание, что вызов `flushSync` необходим для принудительного обновления DOM React перед прокруткой. В противном случае `selectedRef.current` всегда будет указывать на ранее выбранный элемент.

<Sandpack>

```js
import { useRef, useState } from 'react';
import { flushSync } from 'react-dom';

export default function CatFriends() {
  const selectedRef = useRef(null);
  const [index, setIndex] = useState(0);

  return (
    <>
      <nav>
        <button onClick={() => {
          flushSync(() => {
            if (index < catList.length - 1) {
              setIndex(index + 1);
            } else {
              setIndex(0);
            }
          });
          selectedRef.current.scrollIntoView({
            behavior: 'smooth',
            block: 'nearest',
            inline: 'center'
          });            
        }}>
          Next
        </button>
      </nav>
      <div>
        <ul>
          {catList.map((cat, i) => (
            <li
              key={cat.id}
              ref={index === i ?
                selectedRef :
                null
              }
            >
              <img
                className={
                  index === i ?
                    'active'
                    : ''
                }
                src={cat.imageUrl}
                alt={'Cat #' + cat.id}
              />
            </li>
          ))}
        </ul>
      </div>
    </>
  );
}

const catList = [];
for (let i = 0; i < 10; i++) {
  catList.push({
    id: i,
    imageUrl: 'https://loremflickr.com/250/200/cat?lock=' + i
  });
}

```

```css
div {
  width: 100%;
  overflow: hidden;
}

nav {
  text-align: center;
}

button {
  margin: .25rem;
}

ul,
li {
  list-style: none;
  white-space: nowrap;
}

li {
  display: inline;
  padding: 0.5rem;
}

img {
  padding: 10px;
  margin: -10px;
  transition: background 0.2s linear;
}

.active {
  background: rgba(0, 100, 150, 0.4);
}
```

</Sandpack>

</Solution>

#### Фокусировка поля поиска с помощью отдельных компонентов {/*focus-the-search-field-with-separate-components*/}

Сделайте так, чтобы при нажатии кнопки "Search" фокус перемещался в поле ввода. Обратите внимание, что каждый компонент определён в отдельном файле и не должен быть перемещён из него. Как их связать?

<Hint>

Вам нужно будет передать `ref` как проп, чтобы разрешить раскрытие DOM-узла из вашего собственного компонента, такого как `SearchInput`.

</Hint>

<Sandpack>

```js src/App.js
import SearchButton from './SearchButton.js';
import SearchInput from './SearchInput.js';

export default function Page() {
  return (
    <>
      <nav>
        <SearchButton />
      </nav>
      <SearchInput />
    </>
  );
}
```

```js src/SearchButton.js
export default function SearchButton() {
  return (
    <button>
      Search
    </button>
  );
}
```

```js src/SearchInput.js
export default function SearchInput() {
  return (
    <input
      placeholder="Looking for something?"
    />
  );
}
```

```css
button { display: block; margin-bottom: 10px; }
```

</Sandpack>

<Solution>

Вам нужно будет добавить проп `onClick` к `SearchButton` и сделать так, чтобы `SearchButton` передавал его браузерной кнопке `<button>`. Вы также передадите ref в `<SearchInput>`, который перенаправит его на реальный `<input>` и заполнит его. Наконец, в обработчике клика вы вызовете `focus` для DOM-узла, хранящегося внутри этого ref.

<Sandpack>

```js src/App.js
import { useRef } from 'react';
import SearchButton from './SearchButton.js';
import SearchInput from './SearchInput.js';

export default function Page() {
  const inputRef = useRef(null);
  return (
    <>
      <nav>
        <SearchButton onClick={() => {
          inputRef.current.focus();
        }} />
      </nav>
      <SearchInput ref={inputRef} />
    </>
  );
}
```

```js src/SearchButton.js
export default function SearchButton({ onClick }) {
  return (
    <button onClick={onClick}>
      Search
    </button>
  );
}
```

```js src/SearchInput.js
export default function SearchInput({ ref }) {
  return (
    <input
      ref={ref}
      placeholder="Looking for something?"
    />
  );
}
```

```css
button { display: block; margin-bottom: 10px; }
```

</Sandpack>

</Solution>

</Challenges>