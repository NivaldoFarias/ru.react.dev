---
title: "Представляем react.dev"
author: Дэн Абрамов и Рэйчел Наборс
date: 2023/03/16
description: Сегодня мы рады представить react.dev, новый дом для React и его документации. В этом посте мы хотим провести вас по новому сайту.
---

16 марта 2023 г. [Дэн Абрамов](https://bsky.app/profile/danabra.mov) и [Рэйчел Наборс](https://twitter.com/rachelnabors)

---

<Intro>

Сегодня мы рады представить [react.dev](https://react.dev), новый дом для React и его документации. В этом посте мы хотим провести вас по новому сайту.

</Intro>

---

## Вкратце {/*tldr*/}

* Новый сайт React ([react.dev](https://react.dev)) обучает современному React с использованием функциональных компонентов и хуков.
* Мы включили диаграммы, иллюстрации, задания и более 600 новых интерактивных примеров.
* Предыдущий сайт документации React теперь переехал на [legacy.reactjs.org](https://legacy.reactjs.org).

## Новый сайт, новый домен, новая главная страница {/*new-site-new-domain-new-homepage*/}

Прежде всего, немного организационных моментов.

Чтобы отпраздновать запуск новой документации и, что более важно, четко разделить старый и новый контент, мы переехали на более короткий домен [react.dev](https://react.dev). Старый домен [reactjs.org](https://reactjs.org) теперь будет перенаправлять сюда.

Старая документация React теперь заархивирована по адресу [legacy.reactjs.org](https://legacy.reactjs.org). Все существующие ссылки на старый контент будут автоматически перенаправляться туда, чтобы избежать "сломанных ссылок", но устаревший сайт больше не будет получать обновлений.

Хотите верьте, хотите нет, но React скоро исполнится десять лет. В годах JavaScript это как целое столетие! Мы [обновили главную страницу React](https://react.dev), чтобы отразить, почему мы считаем React отличным способом создания пользовательских интерфейсов сегодня, и обновили руководства по началу работы, чтобы более заметно упомянуть современные фреймворки на базе React.

Если вы еще не видели новую главную страницу, загляните!

## Полный переход на современный React с хуками {/*going-all-in-on-modern-react-with-hooks*/}

Когда мы выпустили React Hooks в 2018 году, документация по хукам предполагала, что читатель знаком с классовыми компонентами. Это помогло сообществу очень быстро принять хуки, но со временем старая документация перестала обслуживать новых читателей. Новым читателям приходилось изучать React дважды: сначала с классовыми компонентами, а затем еще раз с хуками.

**Новая документация обучает React с хуками с самого начала.** Документация разделена на два основных раздела:

* **[Изучение React](/learn)** — это самостоятельный курс, который обучает React с нуля.
* **[Справочник по API](/reference)** предоставляет подробности и примеры использования каждого API React.

Давайте подробнее рассмотрим, что вы найдете в каждом разделе.

<Note>

Существует еще несколько редких случаев использования классовых компонентов, для которых еще нет эквивалента на хуках. Классовые компоненты по-прежнему поддерживаются и документированы в разделе [Устаревший API](/reference/react/legacy) нового сайта.

</Note>

## Быстрый старт {/*quick-start*/}

Раздел "Изучение" начинается со страницы [Быстрый старт](/learn). Это краткий вводный тур по React. Он знакомит с синтаксисом таких концепций, как компоненты, пропсы и состояние, но не вдается в подробности их использования.

Если вы предпочитаете учиться на практике, мы рекомендуем следующим делом ознакомиться с [Руководством по созданию крестиков-ноликов](/learn/tutorial-tic-tac-toe). Оно проведет вас через процесс создания небольшой игры с помощью React, обучая навыкам, которые вы будете использовать каждый день. Вот что вы построите:

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

Мы также хотели бы выделить [Мышление в терминах React](/learn/thinking-in-react) — это руководство, которое помогло многим из нас "щелкнуть" с React. **Мы обновили оба этих классических руководства для использования функциональных компонентов и хуков**, так что они так же хороши, как новые.

<Note>

Приведенный выше пример — это *песочница*. Мы добавили множество песочниц — более 600! — повсюду на сайте. Вы можете редактировать любую песочницу или нажать "Fork" в правом верхнем углу, чтобы открыть ее в отдельной вкладке. Песочницы позволяют быстро экспериментировать с API React, исследовать свои идеи и проверять свое понимание.

</Note>

## Изучайте React шаг за шагом {/*learn-react-step-by-step*/}

Мы хотим, чтобы у каждого человека в мире была равная возможность бесплатно изучать React самостоятельно.

Именно поэтому раздел «Изучение» организован как самостоятельный курс, разделённый на главы. Первые две главы описывают основы React. Если вы новичок в React или хотите освежить знания, начните здесь:

- **[Описание пользовательского интерфейса](/learn/describing-the-ui)** научит вас отображать информацию с помощью компонентов.
- **[Добавление интерактивности](/learn/adding-interactivity)** научит вас обновлять экран в ответ на действия пользователя.

Следующие две главы более продвинутые и дадут вам более глубокое понимание сложных моментов:

- **[Управление состоянием](/learn/managing-state)** научит вас организовывать логику по мере усложнения вашего приложения.
- **[Лазейки](/learn/escape-hatches)** научит вас, как можно «выйти» из React и когда это имеет наибольший смысл.

Каждая глава состоит из нескольких связанных страниц. Большинство этих страниц обучают конкретному навыку или технике, например, [Написание разметки с помощью JSX](/learn/writing-markup-with-jsx), [Обновление объектов в состоянии](/learn/updating-objects-in-state) или [Обмен состоянием между компонентами](/learn/sharing-state-between-components). Некоторые страницы посвящены объяснению идеи, например, [Рендер и коммит](/learn/render-and-commit) или [Состояние как снимок](/learn/state-as-a-snapshot). А есть несколько, например, [Возможно, вам не нужен эффект](/learn/you-might-not-need-an-effect), которые делятся нашими предложениями, основанными на нашем многолетнем опыте.

Вам не обязательно читать эти главы последовательно. У кого есть на это время?! Но вы можете. Страницы в разделе «Изучение» опираются только на концепции, представленные в предыдущих страницах. Если вы хотите читать как книгу, дерзайте!

### Проверяйте своё понимание с помощью заданий {/*check-your-understanding-with-challenges*/}

Большинство страниц в разделе «Изучение» заканчиваются несколькими заданиями для проверки вашего понимания. Например, вот несколько заданий со страницы о [Условном рендеринге](/learn/conditional-rendering#challenges).

Вам не обязательно решать их прямо сейчас! Если только вы *действительно* не хотите.

<Challenges noTitle={true}>

#### Отображение значка для незавершённых элементов с помощью `? :` {/*show-an-icon-for-incomplete-items-with--*/}

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
      <h1>Список вещей Салли Райд</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Космический скафандр" 
        />
        <Item 
          isPacked={true} 
          name="Шлем с золотым листом" 
        />
        <Item 
          isPacked={false} 
          name="Фотография Тэм" 
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
      <h1>Список вещей Салли Райд</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Космический скафандр" 
        />
        <Item 
          isPacked={true} 
          name="Шлем с золотым листом" 
        />
        <Item 
          isPacked={false} 
          name="Фотография Тэм" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

</Solution>

#### Отображение важности элемента с помощью `&&` {/*show-the-item-importance-with-*/}

В этом примере каждый `Item` получает числовой проп `importance`. Используйте оператор `&&`, чтобы отобразить «_(Важность: X)_» курсивом, но только для элементов с ненулевой важностью. Ваш список элементов должен выглядеть так:

* Космический скафандр _(Важность: 9)_
* Шлем с золотым листом
* Фотография Тэм _(Важность: 6)_

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
      <h1>Список вещей Салли Райд</h1>
      <ul>
        <Item 
          importance={9} 
          name="Космический скафандр" 
        />
        <Item 
          importance={0} 
          name="Шлем с золотым листом" 
        />
        <Item 
          importance={6} 
          name="Фотография Тэм" 
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
        <i>(Важность: {importance})</i>
      }
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Список вещей Салли Райд</h1>
      <ul>
        <Item 
          importance={9} 
          name="Космический скафандр" 
        />
        <Item 
          importance={0} 
          name="Шлем с золотым листом" 
        />
        <Item 
          importance={6} 
          name="Фотография Тэм" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

Обратите внимание, что вы должны написать `importance > 0 && ...`, а не `importance && ...`, чтобы при значении `importance` равном `0` не отображалось `0` в качестве результата!

В этом решении используются два отдельных условия для вставки пробела между именем и меткой важности. В качестве альтернативы вы можете использовать фрагмент с ведущим пробелом: `importance > 0 && <> <i>...</i></>` или добавить пробел непосредственно внутрь `<i>`: `importance > 0 && <i> ...</i>`.

</Solution>

</Challenges>

Обратите внимание на кнопку «Показать решение» в левом нижнем углу. Она полезна, если вы хотите проверить себя!

### Развивайте интуицию с помощью диаграмм и иллюстраций {/*build-an-intuition-with-diagrams-and-illustrations*/}

Когда мы не могли понять, как объяснить что-то только с помощью кода и слов, мы добавляли диаграммы, которые помогают развить интуицию. Например, вот одна из диаграмм из раздела [Сохранение и сброс состояния](/learn/preserving-and-resetting-state):

<Diagram name="preserving_state_diff_same_pt1" height={350} width={794} alt="Диаграмма с тремя секциями, со стрелкой, переходящей между каждой секцией. Первая секция содержит React-компонент с меткой 'div' и единственным дочерним элементом с меткой 'section', который имеет единственный дочерний элемент с меткой 'Counter', содержащий пузырь состояния с меткой 'count' и значением 3. Средняя секция имеет тот же родительский 'div', но дочерние компоненты теперь удалены, что обозначено жёлтым изображением 'доказательства'. Третья секция снова имеет тот же родительский 'div', теперь с новым дочерним элементом с меткой 'div', выделенным жёлтым цветом, также с новым дочерним элементом с меткой 'Counter', содержащим пузырь состояния с меткой 'count' и значением 0, все выделено жёлтым цветом.">

Когда `section` меняется на `div`, `section` удаляется, а новый `div` добавляется

</Diagram>

Вы также увидите некоторые иллюстрации в документации — вот одна из них, изображающая [браузер, отрисовывающий экран](/learn/render-and-commit#epilogue-browser-paint):

<Illustration alt="Браузер рисует 'натюрморт с карточным элементом'." src="/images/docs/illustrations/i_browser-paint.png" />

Мы подтвердили с поставщиками браузеров, что это изображение на 100% научно точно.

## Новый, подробный справочник API {/*a-new-detailed-api-reference*/}

В [Справочнике API](/reference/react) каждый API React теперь имеет свою страницу. Это включает все типы API:

- Встроенные хуки, такие как [`useState`](/reference/react/useState).
- Встроенные компоненты, такие как [`<Suspense>`](/reference/react/Suspense).
- Встроенные браузерные компоненты, такие как [`<input>`](/reference/react-dom/components/input).
- API, ориентированные на фреймворки, такие как [`renderToPipeableStream`](/reference/react-dom/server/renderToReadableStream).
- Другие API React, такие как [`memo`](/reference/react/memo).

Вы заметите, что каждая страница API разделена как минимум на два сегмента: *Справочник* и *Использование*.

[Справочник](/reference/react/useState#reference) описывает формальную сигнатуру API, перечисляя его аргументы и возвращаемые значения. Он краток, но может показаться немного абстрактным, если вы не знакомы с этим API. Он описывает, что делает API, но не как его использовать.

[Использование](/reference/react/useState#usage) показывает, почему и как вы будете использовать этот API на практике, как мог бы объяснить коллега или друг. Он показывает **канонические сценарии использования каждого API командой React.** Мы добавили фрагменты кода, выделенные цветом, примеры использования различных API вместе и рецепты, которые вы можете скопировать и вставить:

<Recipes titleText="Базовые примеры useState" titleId="examples-basic">

#### Счетчик (число) {/*counter-number*/}

В этом примере переменная состояния `count` хранит число. Нажатие кнопки увеличивает его.

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
      Вы нажали меня {count} раз
    </button>
  );
}
```

</Sandpack>

<Solution />

#### Текстовое поле (строка) {/*text-field-string*/}

В этом примере переменная состояния `text` хранит строку. Когда вы печатаете, `handleChange` считывает последнее введенное значение из браузерного DOM-элемента ввода и вызывает `setText` для обновления состояния. Это позволяет отображать текущий `text` ниже.

<Sandpack>

```js
import { useState } from 'react';

export default function MyInput() {
  const [text, setText] = useState('привет');

  function handleChange(e) {
    setText(e.target.value);
  }

  return (
    <>
      <input value={text} onChange={handleChange} />
      <p>Вы напечатали: {text}</p>
      <button onClick={() => setText('привет')}>
        Сбросить
      </button>
    </>
  );
}
```

</Sandpack>

<Solution />

#### Флажок (булево значение) {/*checkbox-boolean*/}

В этом примере переменная состояния `liked` хранит булево значение. Когда вы нажимаете на ввод, `setLiked` обновляет переменную состояния `liked` в зависимости от того, установлен ли флажок браузерного ввода. Переменная `liked` используется для отображения текста под флажком.

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
        Мне это понравилось
      </label>
      <p>Вы {liked ? 'понравилось' : 'не понравилось'} это.</p>
    </>
  );
}
```

</Sandpack>

<Solution />

#### Форма (две переменные) {/*form-two-variables*/}

Вы можете объявить несколько переменных состояния в одном компоненте. Каждая переменная состояния полностью независима.

<Sandpack>

```js
import { useState } from 'react';

export default function Form() {
  const [name, setName] = useState('Тейлор');
  const [age, setAge] = useState(42);

  return (
    <>
      <input
        value={name}
        onChange={e => setName(e.target.value)}
      />
      <button onClick={() => setAge(age + 1)}>
        Увеличить возраст
      </button>
      <p>Привет, {name}. Тебе {age}.</p>
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

Мы надеемся, что такой подход сделает справочник API полезным не только для поиска аргументов, но и для того, чтобы увидеть все различные вещи, которые вы можете делать с любым данным API, и как он связан с другими.

## Что дальше? {/*whats-next*/}

На этом наш небольшой тур подошёл к концу! Осмотрите новый сайт, посмотрите, что вам нравится, а что нет, и продолжайте делиться своим мнением в нашем [трекере задач](https://github.com/reactjs/react.dev/issues).

Мы признаём, что этот проект занял много времени. Мы хотели поддерживать высокий уровень качества, которого заслуживает сообщество React. При написании этой документации и создании всех примеров мы обнаружили ошибки в наших собственных объяснениях, ошибки в React и даже пробелы в дизайне React, над устранением которых мы сейчас работаем. Мы надеемся, что новая документация поможет нам в будущем поддерживать более высокий уровень качества самого React.

Мы услышали многие ваши просьбы расширить контент и функциональность сайта, например:

- Предоставить версию TypeScript для всех примеров;
- Создать обновлённые руководства по производительности, тестированию и доступности;
- Документировать React Server Components независимо от фреймворков, которые их поддерживают;
- Работать с нашим международным сообществом над переводом новой документации;
- Добавить недостающие функции на новый сайт (например, RSS для этого блога).

Теперь, когда [react.dev](https://react.dev/) запущен, мы сможем переключить наше внимание с «догоняющих» сторонних образовательных ресурсов по React на добавление новой информации и дальнейшее улучшение нашего нового сайта.

Мы считаем, что никогда ещё не было лучшего времени для изучения React.

## Кто над этим работал? {/*who-worked-on-this*/}

В команде React проектом руководила [Rachel Nabors](https://twitter.com/rachelnabors/) (она же предоставила иллюстрации), а [Dan Abramov](https://bsky.app/profile/danabra.mov) разработал учебную программу. Они также совместно написали большую часть контента.

Конечно, ни один крупный проект не обходится без помощи со стороны. Нам есть кого поблагодарить!

[Sylwia Vargas](https://twitter.com/SylwiaVargas) переработала наши примеры, чтобы они выходили за рамки «foo/bar/baz» и котят, и включали учёных, художников и города со всего мира. [Maggie Appleton](https://twitter.com/Mappletons) превратила наши наброски в понятную систему диаграмм.

Спасибо [David McCabe](https://twitter.com/mcc_abe), [Sophie Alpert](https://twitter.com/sophiebits), [Rick Hanlon](https://twitter.com/rickhanlonii), [Andrew Clark](https://twitter.com/acdlite) и [Matt Carroll](https://twitter.com/mattcarrollcode) за дополнительный вклад в написание текстов. Мы также хотели бы поблагодарить [Natalia Tepluhina](https://twitter.com/n_tepluhina) и [Sebastian Markbåge](https://twitter.com/sebmarkbage) за их идеи и отзывы.

Спасибо [Dan Lebowitz](https://twitter.com/lebo) за дизайн сайта и [Razvan Gradinar](https://dribbble.com/GradinarRazvan) за дизайн песочницы.

В области разработки благодарим [Jared Palmer](https://twitter.com/jaredpalmer) за разработку прототипа. Спасибо [Dane Grant](https://twitter.com/danecando) и [Dustin Goodman](https://twitter.com/dustinsgoodman) из [ThisDotLabs](https://www.thisdot.co/) за поддержку в разработке пользовательского интерфейса. Спасибо [Ives van Hoorne](https://twitter.com/CompuIves), [Alex Moldovan](https://twitter.com/alexnmoldovan), [Jasper De Moor](https://twitter.com/JasperDeMoor) и [Danilo Woznica](https://twitter.com/danilowoz) из [CodeSandbox](https://codesandbox.io/) за их работу по интеграции песочницы. Спасибо [Rick Hanlon](https://twitter.com/rickhanlonii) за разработку и дизайн отдельных элементов, доводку наших цветов и мелких деталей. Спасибо [Harish Kumar](https://www.strek.in/) и [Luna Ruan](https://twitter.com/lunaruan) за добавление новых функций на сайт и помощь в его поддержке.

Огромное спасибо тем, кто добровольно выделил время для участия в программе альфа- и бета-тестирования. Ваш энтузиазм и бесценные отзывы помогли нам сформировать эту документацию. Особая благодарность нашему бета-тестеру [Debbie O'Brien](https://twitter.com/debs_obrien), которая выступила с докладом о своём опыте использования документации React на React Conf 2021.

Наконец, спасибо сообществу React за то, что оно послужило источником вдохновения для этой работы. Вы — причина, по которой мы это делаем, и мы надеемся, что новая документация поможет вам использовать React для создания любого пользовательского интерфейса, который вы захотите.