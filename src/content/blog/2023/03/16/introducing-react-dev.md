---
title: "Представляем react.dev"
author: Дэн Абрамов и Рэйчел Наборс
date: 2023/03/16
description: Сегодня мы рады запустить react.dev, новый дом для React и его документации. В этой статье мы хотели бы провести для вас экскурсию по новому сайту.
---

16 марта 2023 г. от [Дэна Абрамова](https://bsky.app/profile/danabra.mov) и [Рэйчел Наборс](https://twitter.com/rachelnabors)

---

<Intro>

Сегодня мы рады запустить [react.dev](https://react.dev), новый дом для React и его документации. В этой статье мы хотели бы провести для вас экскурсию по новому сайту.

</Intro>

---

## tl;dr {/*tldr*/}

* Новый сайт React ([react.dev](https://react.dev)) обучает современному React с функциональными компонентами и хуками.
* Мы включили диаграммы, иллюстрации, задачи и более 600 новых интерактивных примеров.
* Предыдущий сайт документации React теперь переехал на [legacy.reactjs.org](https://legacy.reactjs.org).

## Новый сайт, новый домен, новая домашняя страница {/*new-site-new-domain-new-homepage*/}

Сначала немного организационных моментов.

Чтобы отпраздновать запуск новой документации и, что более важно, четко разделить старый и новый контент, мы перешли на более короткий домен [react.dev](https://react.dev). Старый домен [reactjs.org](https://reactjs.org) теперь будет перенаправлять сюда.

Старая документация React теперь архивирована по адресу [legacy.reactjs.org](https://legacy.reactjs.org). Все существующие ссылки на старый контент будут автоматически перенаправляться туда, чтобы избежать «поломок в сети», но устаревший сайт не получит много обновлений.

Верите или нет, React скоро исполнится десять лет. По меркам JavaScript это целое столетие! Мы [обновили домашнюю страницу React](https://react.dev), чтобы отразить, почему мы считаем React отличным способом создания пользовательских интерфейсов сегодня, и обновили руководства по началу работы, чтобы более заметно упоминать современные фреймворки на основе React.

Если вы еще не видели новую домашнюю страницу, обязательно ознакомьтесь с ней!

## Полный переход на современный React с хуками {/*going-all-in-on-modern-react-with-hooks*/}

Когда мы выпустили хуки React в 2018 году, в документации по хукам предполагалось, что читатель знаком с классовыми компонентами. Это помогло сообществу очень быстро принять хуки, но через некоторое время старая документация перестала служить новым читателям. Новым читателям пришлось изучать React дважды: один раз с классовыми компонентами, а затем еще раз с хуками.

**Новая документация обучает React с хуками с самого начала.** Документация разделена на два основных раздела:

*   **[Изучение React](/learn)** — это самостоятельный курс, который обучает React с нуля.
*   **[Справочник по API](/reference)** предоставляет подробную информацию и примеры использования для каждого API React.

Давайте подробнее рассмотрим, что вы можете найти в каждом разделе.

<Note>

Существует еще несколько редких вариантов использования классовых компонентов, для которых еще нет эквивалента на основе хуков. Классовые компоненты по-прежнему поддерживаются и задокументированы в разделе [Устаревший API](/reference/react/legacy) нового сайта.

</Note>

## Быстрый старт {/*quick-start*/}

Раздел «Изучение» начинается со страницы [Быстрый старт](/learn). Это краткий вводный тур по React. Он знакомит с синтаксисом таких понятий, как компоненты, пропсы и состояние, но не вдается в подробности об их использовании.

Если вы любите учиться на практике, мы рекомендуем ознакомиться с [Руководством по Tic-Tac-Toe](/learn/tutorial-tic-tac-toe) далее. Оно проведет вас через создание небольшой игры с помощью React, обучая навыкам, которые вы будете использовать каждый день. Вот что вы построите:

<Sandpack>

```js src/App.js
import { useState } from 'react';

function Square({ value, onSquareClick }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}

function Board({ xIsNext, squares, onPlay }) {
  function handleClick(i) {
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    const nextSquares = squares.slice();
    if (xIsNext) {
      nextSquares[i] = 'X';
    } else {
      nextSquares[i] = 'O';
    }
    onPlay(nextSquares);
  }

  const winner = calculateWinner(squares);
  let status;
  if (winner) {
    status = 'Winner: ' + winner;
  } else {
    status = 'Next player: ' + (xIsNext ? 'X' : 'O');
  }

  return (
    <>
      <div className="status">{status}</div>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        <Square value={squares[1]} onSquareClick={() => handleClick(1)} />
        <Square value={squares[2]} onSquareClick={() => handleClick(2)} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} onSquareClick={() => handleClick(3)} />
        <Square value={squares[4]} onSquareClick={() => handleClick(4)} />
        <Square value={squares[5]} onSquareClick={() => handleClick(5)} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} onSquareClick={() => handleClick(6)} />
        <Square value={squares[7]} onSquareClick={() => handleClick(7)} />
        <Square value={squares[8]} onSquareClick={() => handleClick(8)} />
      </div>
    </>
  );
}

export default function Game() {
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const [currentMove, setCurrentMove] = useState(0);
  const xIsNext = currentMove % 2 === 0;
  const currentSquares = history[currentMove];

  function handlePlay(nextSquares) {
    const nextHistory = [...history.slice(0, currentMove + 1), nextSquares];
    setHistory(nextHistory);
    setCurrentMove(nextHistory.length - 1);
  }

  function jumpTo(nextMove) {
    setCurrentMove(nextMove);
  }

  const moves = history.map((squares, move) => {
    let description;
    if (move > 0) {
      description = 'Go to move #' + move;
    } else {
      description = 'Go to game start';
    }
    return (
      <li key={move}>
        <button onClick={() => jumpTo(move)}>{description}</button>
      </li>
    );
  });

  return (
    <div className="game">
      <div className="game-board">
        <Board xIsNext={xIsNext} squares={currentSquares} onPlay={handlePlay} />
      </div>
      <div className="game-info">
        <ol>{moves}</ol>
      </div>
    </div>
  );
}

function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
}
```

```css src/styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}
.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

Мы также хотели бы выделить [Thinking in React](/learn/thinking-in-react) — это руководство, которое заставило React «щелкнуть» для многих из нас. **Мы обновили оба этих классических руководства, чтобы использовать функциональные компоненты и хуки,** поэтому они как новые.

<Note>

Пример выше — это *песочница*. Мы добавили много песочниц — более 600! — по всему сайту. Вы можете отредактировать любую песочницу или нажать «Fork» в правом верхнем углу, чтобы открыть ее в отдельной вкладке. Песочницы позволяют быстро поиграть с API React, изучить свои идеи и проверить свое понимание.

</Note>


## Изучите React шаг за шагом {/*learn-react-step-by-step*/}

Мы хотим, чтобы у каждого человека в мире была равная возможность изучать React бесплатно самостоятельно.

Именно поэтому раздел «Изучение» организован как самостоятельный курс, разбитый на главы. Первые две главы описывают основы React. Если вы новичок в React или хотите освежить свои знания, начните здесь:

- **[Описание пользовательского интерфейса](/learn/describing-the-ui)** учит отображать информацию с помощью компонентов.
- **[Добавление интерактивности](/learn/adding-interactivity)** учит обновлять экран в ответ на действия пользователя.

Следующие две главы более продвинутые и дадут вам более глубокое понимание более сложных частей:

- **[Управление состоянием](/learn/managing-state)** учит организовывать вашу логику по мере роста сложности вашего приложения.
- **[Лазейки](/learn/escape-hatches)** учит, как можно «выйти за пределы» React и когда это имеет наибольший смысл.

Каждая глава состоит из нескольких связанных страниц. Большинство этих страниц учат конкретному навыку или технике — например, [Написание разметки с помощью JSX](/learn/writing-markup-with-jsx), [Обновление объектов в состоянии](/learn/updating-objects-in-state) или [Совместное использование состояния между компонентами](/learn/sharing-state-between-components). Некоторые страницы посвящены объяснению идеи — например, [Рендер и фиксация](/learn/render-and-commit) или [Состояние как снимок](/learn/state-as-a-snapshot). И есть несколько, таких как [Возможно, вам не нужен эффект](/learn/you-might-not-need-an-effect), которые делятся нашими предложениями, основанными на том, что мы узнали за эти годы.

Вам не нужно читать эти главы последовательно. У кого на это есть время?! Но вы можете. Страницы в разделе «Изучение» опираются только на концепции, представленные на предыдущих страницах. Если вы хотите прочитать это как книгу, вперед!

### Проверьте свое понимание с помощью задач {/*check-your-understanding-with-challenges*/}

Большинство страниц в разделе «Изучение» заканчиваются несколькими задачами для проверки вашего понимания. Например, вот несколько задач со страницы о [Условном рендеринге](/learn/conditional-rendering#challenges).

Вам не обязательно решать их прямо сейчас! Если только вы *действительно* этого не хотите.

<Challenges noTitle={true}>

#### Покажите значок для невыполненных элементов с помощью `? :` {/*show-an-icon-for-incomplete-items-with--*/}

Используйте условный оператор (`cond ? a : b`), чтобы отобразить ❌, если `isPacked` не равно `true`.

<Sandpack>

```js
function Item({ name, isPacked }) {
  return (
    <li className="item">
      {name} {isPacked && '✅'}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

<Solution>

<Sandpack>

```js
function Item({ name, isPacked }) {
  return (
    <li className="item">
      {name} {isPacked ? '✅' : '❌'}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

</Solution>

#### Покажите важность элемента с помощью `&&` {/*show-the-item-importance-with-*/}

В этом примере каждый `Item` получает числовой проп `importance`. Используйте оператор `&&`, чтобы отобразить «_(Важность: X)_» курсивом, но только для элементов, у которых важность не равна нулю. Ваш список элементов должен выглядеть так:

* Space suit _(Важность: 9)_
* Helmet with a golden leaf
* Photo of Tam _(Важность: 6)_

Не забудьте добавить пробел между двумя метками!

<Sandpack>

```js
function Item({ name, importance }) {
  return (
    <li className="item">
      {name}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          importance={9} 
          name="Space suit" 
        />
        <Item 
          importance={0} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          importance={6} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

<Solution>

Это должно сработать:

<Sandpack>

```js
function Item({ name, importance }) {
  return (
    <li className="item">
      {name}
      {importance > 0 && ' '}
      {importance > 0 &&
        <i>(Importance: {importance})</i>
      }
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          importance={9} 
          name="Space suit" 
        />
        <Item 
          importance={0} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          importance={6} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

Обратите внимание, что вы должны написать `importance > 0 && ...`, а не `importance && ...`, чтобы, если `importance` равно `0`, `0` не отображалось в результате!

В этом решении используются два отдельных условия для вставки пробела между именем и меткой важности. Альтернативно, вы можете использовать Fragment с начальным пробелом: `importance > 0 && <> <i>...</i></>` или добавить пробел непосредственно внутри `<i>`: `importance > 0 && <i> ...</i>`.

</Solution>

</Challenges>

Обратите внимание на кнопку «Показать решение» в левом нижнем углу. Это удобно, если вы хотите проверить себя!

### Создайте интуицию с помощью диаграмм и иллюстраций {/*build-an-intuition-with-diagrams-and-illustrations*/}

Когда мы не могли понять, как объяснить что-либо только с помощью кода и слов, мы добавили диаграммы, которые помогают обеспечить некоторую интуицию. Например, вот одна из диаграмм из [Сохранение и сброс состояния](/learn/preserving-and-resetting-state):

<Diagram name="preserving_state_diff_same_pt1" height={350} width={794} alt="Диаграмма с тремя разделами, со стрелкой, переходящей между каждым разделом. Первый раздел содержит компонент React с меткой 'div' с одним дочерним элементом с меткой 'section', который имеет один дочерний элемент с меткой 'Counter', содержащий пузырь состояния с меткой 'count' со значением 3. Средний раздел имеет тот же родительский элемент 'div', но дочерние компоненты теперь удалены, что обозначено желтым изображением 'proof'. Третий раздел снова имеет тот же родительский элемент 'div', теперь с новым дочерним элементом с меткой 'div', выделенным желтым цветом, также с новым дочерним элементом с меткой 'Counter', содержащим пузырь состояния с меткой 'count' со значением 0, все выделено желтым цветом.">

Когда `section` меняется на `div`, `section` удаляется, а новый `div` добавляется

</Diagram>

Вы также увидите некоторые иллюстрации в документации — вот одна из [браузера, рисующего экран](/learn/render-and-commit#epilogue-browser-paint):

<Illustration alt="Браузер рисует 'натюрморт с элементом карты'." src="/images/docs/illustrations/i_browser-paint.png" />

Мы подтвердили с поставщиками браузеров, что это изображение на 100% научно точное.

## Новый, подробный справочник по API {/*a-new-detailed-api-reference*/}

В [Справочнике по API](/reference/react), каждый API React теперь имеет выделенную страницу. Это включает в себя все виды API:

- Встроенные хуки, такие как [`useState`](/reference/react/useState).
- Встроенные компоненты, такие как [`<Suspense>`](/reference/react/Suspense).
- Встроенные компоненты браузера, такие как [`<input>`](/reference/react-dom/components/input).
- Ориентированные на фреймворк API, такие как [`renderToPipeableStream`](/reference/react-dom/server/renderToReadableStream).
- Другие API React, такие как [`memo`](/reference/react/memo).

Вы заметите, что каждая страница API разделена как минимум на два сегмента: *Справочник* и *Использование*.

[Справочник](/reference/react/useState#reference) описывает формальную сигнатуру API, перечисляя его аргументы и возвращаемые значения. Он лаконичен, но может показаться немного абстрактным, если вы не знакомы с этим API. Он описывает, что делает API, но не как его использовать.

[Использование](/reference/react/useState#usage) показывает, почему и как вы будете использовать этот API на практике, как мог бы объяснить коллега или друг. Он показывает **канонические сценарии того, как каждый API должен был использоваться командой React.** Мы добавили цветовые фрагменты, примеры совместного использования различных API и рецепты, которые вы можете скопировать и вставить:

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

В этом примере переменная состояния `text` содержит строку. Когда вы печатаете, `handleChange` считывает последнее значение ввода из элемента DOM ввода браузера и вызывает `setText` для обновления состояния. Это позволяет вам отображать текущий `text` ниже.

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

#### Флажок (логическое значение) {/*checkbox-boolean*/}

В этом примере переменная состояния `liked` содержит логическое значение. Когда вы нажимаете на ввод, `setLiked` обновляет переменную состояния `liked` с помощью того, установлен ли флажок браузера. Переменная `liked` используется для отображения текста под флажком.

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

Вы можете объявить более одной переменной состояния в одном компоненте. Каждая переменная состояния полностью независима.

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

Некоторые страницы API также включают [Устранение неполадок](/reference/react/useEffect#troubleshooting) (для распространенных проблем) и [Альтернативы](/reference/react-dom/findDOMNode#alternatives) (для устаревших API).

Мы надеемся, что этот подход сделает справочник по API полезным не только как способ поиска аргумента, но и как способ увидеть все различные вещи, которые вы можете сделать с любым данным API, и как он связан с другими.

## Что дальше? {/*whats-next*/}

На этом наш небольшой тур завершен! Осмотрите новый веб-сайт, посмотрите, что вам нравится или не нравится, и продолжайте присылать отзывы в наш [трекер проблем](https://github.com/reactjs/react.dev/issues).

Мы признаем, что этот проект занял много времени. Мы хотели поддерживать высокий уровень качества, которого заслуживает сообщество React. При написании этой документации и создании всех примеров мы обнаружили ошибки в некоторых наших собственных объяснениях, ошибки в React и даже пробелы в дизайне React, над которыми мы сейчас работаем. Мы надеемся, что новая документация поможет нам в будущем поддерживать более высокий уровень самого React.

Мы услышали много ваших запросов на расширение контента и функциональности веб-сайта, например:

- Предоставление версии TypeScript для всех примеров;
- Создание обновленных руководств по производительности, тестированию и доступности;
- Документирование React Server Components независимо от фреймворков, которые их поддерживают;
- Работа с нашим международным сообществом для перевода новой документации;
- Добавление недостающих функций на новый веб-сайт (например, RSS для этого блога).

Теперь, когда [react.dev](https://react.dev/) вышел, мы сможем переключить наше внимание с «догоняющего» сторонних образовательных ресурсов React на добавление новой информации и дальнейшее улучшение нашего нового веб-сайта.

Мы считаем, что сейчас лучшее время для изучения React.


## Кто работал над этим? {/*who-worked-on-this*/}

В команде React [Рейчел Наборс](https://twitter.com/rachelnabors/) руководила проектом (и предоставила иллюстрации), а [Дэн Абрамов](https://bsky.app/profile/danabra.mov) разработал учебную программу. Они также стали соавторами большей части контента.

Конечно, ни один проект такого масштаба не делается в одиночку. Нам есть кому сказать спасибо!

[Сильвия Варгас](https://twitter.com/SylwiaVargas) переработала наши примеры, чтобы выйти за рамки «foo/bar/baz» и котят, и представить ученых, художников и города со всего мира. [Мэгги Эпплтон](https://twitter.com/Mappletons) превратила наши каракули в понятную систему диаграмм.

Спасибо [Дэвиду Маккейбу](https://twitter.com/mcc_abe), [Софи Альперт](https://twitter.com/sophiebits), [Рику Хэнлону](https://twitter.com/rickhanlonii), [Эндрю Кларку](https://twitter.com/acdlite) и [Мэтту Кэрроллу](https://twitter.com/mattcarrollcode) за дополнительные материалы. Мы также хотели бы поблагодарить [Наталию Теплухину](https://twitter.com/n_tepluhina) и [Себастьяна Маркбэге](https://twitter.com/sebmarkbage) за их идеи и отзывы.

Спасибо [Дэну Лебовицу](https://twitter.com/lebo) за дизайн сайта и [Развану Градинару](https://dribbble.com/GradinarRazvan) за дизайн песочницы.

Что касается разработки, спасибо [Джареду Палмеру](https://twitter.com/jaredpalmer) за разработку прототипа. Спасибо [Дейну Гранту](https://twitter.com/danecando) и [Дастину Гудману](https://twitter.com/dustinsgoodman) из [ThisDotLabs](https://www.thisdot.co/) за поддержку в разработке пользовательского интерфейса. Спасибо [Ивесу ван Хорну](https://twitter.com/CompuIves), [Алексу Молдовану](https://twitter.com/alexnmoldovan), [Джасперу Де Мору](https://twitter.com/JasperDeMoor) и [Данило Вознице](https://twitter.com/danilowoz) из [CodeSandbox](https://codesandbox.io/) за их работу с интеграцией песочницы. Спасибо [Рику Хэнлону](https://twitter.com/rickhanlonii) за точечную разработку и работу над дизайном, доработку наших цветов и более мелких деталей. Спасибо [Харишу Кумару](https://www.strek.in/) и [Луне Руан](https://twitter.com/lunaruan) за добавление новых функций на сайт и помощь в его обслуживании.

Огромное спасибо людям, которые выделили свое время для участия в программе альфа- и бета-тестирования. Ваш энтузиазм и бесценные отзывы помогли нам сформировать эти документы. Отдельное спасибо нашему бета-тестеру [Дебби О'Брайен](https://twitter.com/debs_obrien), которая выступила с докладом о своем опыте использования документации React на React Conf 2021.

Наконец, спасибо сообществу React за вдохновение, стоящее за этими усилиями. Вы — причина, по которой мы это делаем, и мы надеемся, что новая документация поможет вам использовать React для создания любого пользовательского интерфейса, который вы хотите.