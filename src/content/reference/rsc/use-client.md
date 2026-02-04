---
title: "'use client'"
titleForTitleTag: "'use client' directive"
---

<RSC>

`'use client'` используется с [компонентами сервера React](/reference/rsc/server-components).

</RSC>


<Intro>

`'use client'` позволяет вам помечать код, который выполняется на клиенте.

</Intro>

<InlineToc />

---

## Справочник {/*reference*/}

### `'use client'` {/*use-client*/}

Добавьте `'use client'` в начало файла, чтобы пометить модуль и его транзитивные зависимости как клиентский код.

```js {1}
'use client';

import { useState } from 'react';
import { formatDate } from './formatters';
import Button from './button';

export default function RichTextEditor({ timestamp, text }) {
  const date = formatDate(timestamp);
  // ...
  const editButton = <Button />;
  // ...
}
```

Когда файл, помеченный `'use client'`, импортируется из Server Component, [совместимые бандлеры](/learn/start-a-new-react-project#bleeding-edge-react-frameworks) будут рассматривать импорт модуля как границу между кодом, выполняемым на сервере, и кодом, выполняемым на клиенте.

Как зависимости `RichTextEditor`, `formatDate` и `Button` также будут вычисляться на клиенте, независимо от того, содержат ли их модули директиву `'use client'`. Обратите внимание, что один и тот же модуль может вычисляться на сервере при импорте из серверного кода и на клиенте при импорте из клиентского кода.

#### Оговорки {/*caveats*/}

* `'use client'` должен быть в самом начале файла, перед любыми импортами или другим кодом (комментарии допустимы). Он должен быть написан с использованием одинарных или двойных кавычек, но не обратных.
* Когда модуль `'use client'` импортируется из другого модуля, отрисовываемого на клиенте, директива не имеет эффекта.
* Когда модуль компонента содержит директиву `'use client'`, любое использование этого компонента гарантированно будет Client Component. Однако компонент может быть вычислен на клиенте, даже если у него нет директивы `'use client'`.
	* Использование компонента считается Client Component, если он определен в модуле с директивой `'use client'` или когда он является транзитивной зависимостью модуля, содержащего директиву `'use client'`. В противном случае это Server Component.
* Код, помеченный для клиентского вычисления, не ограничивается компонентами. Весь код, являющийся частью поддерева клиентских модулей, отправляется и выполняется клиентом.
* Когда модуль, вычисляемый на сервере, импортирует значения из модуля `'use client'`, эти значения должны быть либо React-компонентом, либо [поддерживаемыми сериализуемыми значениями пропсов](#passing-props-from-server-to-client-components), чтобы их можно было передать в Client Component. Любой другой сценарий использования вызовет исключение.

### Как `'use client'` помечает клиентский код {/*how-use-client-marks-client-code*/}

В приложении React компоненты часто разделяются на отдельные файлы или [модули](/learn/importing-and-exporting-components#exporting-and-importing-a-component).

Для приложений, использующих React Server Components, приложение по умолчанию отрисовывается на сервере. `'use client'` вводит границу между сервером и клиентом в [дереве зависимостей модулей](/learn/understanding-your-ui-as-a-tree#the-module-dependency-tree), фактически создавая поддерево клиентских модулей.

Чтобы лучше проиллюстрировать это, рассмотрим следующий пример приложения React Server Components.

<Sandpack>

```js src/App.js
import FancyText from './FancyText';
import InspirationGenerator from './InspirationGenerator';
import Copyright from './Copyright';

export default function App() {
  return (
    <>
      <FancyText title text="Get Inspired App" />
      <InspirationGenerator>
        <Copyright year={2004} />
      </InspirationGenerator>
    </>
  );
}

```

```js src/FancyText.js
export default function FancyText({title, text}) {
  return title
    ? <h1 className='fancy title'>{text}</h1>
    : <h3 className='fancy cursive'>{text}</h3>
}
```

```js src/InspirationGenerator.js
'use client';

import { useState } from 'react';
import inspirations from './inspirations';
import FancyText from './FancyText';

export default function InspirationGenerator({children}) {
  const [index, setIndex] = useState(0);
  const quote = inspirations[index];
  const next = () => setIndex((index + 1) % inspirations.length);

  return (
    <>
      <p>Your inspirational quote is:</p>
      <FancyText text={quote} />
      <button onClick={next}>Inspire me again</button>
      {children}
    </>
  );
}
```

```js src/Copyright.js
export default function Copyright({year}) {
  return <p className='small'>©️ {year}</p>;
}
```

```js src/inspirations.js
export default [
  "Don’t let yesterday take up too much of today.” — Will Rogers",
  "Ambition is putting a ladder against the sky.",
  "A joy that's shared is a joy made double.",
];
```

```css
.fancy {
  font-family: 'Georgia';
}
.title {
  color: #007AA3;
  text-decoration: underline;
}
.cursive {
  font-style: italic;
}
.small {
  font-size: 10px;
}
```

</Sandpack>

В дереве зависимостей модулей этого примера директива `'use client'` в `InspirationGenerator.js` помечает этот модуль и все его транзитивные зависимости как клиентские модули. Поддерево, начинающееся с `InspirationGenerator.js`, теперь помечено как клиентские модули.

<Diagram name="use_client_module_dependency" height={250} width={545} alt="График дерева с верхним узлом, представляющим модуль 'App.js'. 'App.js' имеет три дочерних элемента: 'Copyright.js', 'FancyText.js' и 'InspirationGenerator.js'. 'InspirationGenerator.js' имеет два дочерних элемента: 'FancyText.js' и 'inspirations.js'. Узлы под 'InspirationGenerator.js' и включая его имеют желтый фон, чтобы обозначить, что этот подграф отрисовывается на клиенте из-за директивы 'use client' в 'InspirationGenerator.js'.">
`'use client'` сегментирует дерево зависимостей модулей приложения React Server Components, помечая `InspirationGenerator.js` и все его зависимости как отрисовываемые на клиенте.
</Diagram>

Во время рендеринга фреймворк будет отрисовывать корневой компонент на сервере и продолжать через [дерево рендеринга](/learn/understanding-your-ui-as-a-tree#the-render-tree), отказываясь вычислять любой код, импортированный из помеченного клиентом кода.

Затем серверная часть дерева рендеринга отправляется клиенту. Клиент, с загруженным клиентским кодом, завершает рендеринг остальной части дерева.

<Diagram name="use_client_render_tree" height={250} width={500} alt="График дерева, где каждый узел представляет компонент и его дочерние элементы как дочерние компоненты. Верхний узел помечен как 'App', и у него есть два дочерних компонента: 'InspirationGenerator' и 'FancyText'. 'InspirationGenerator' имеет два дочерних компонента: 'FancyText' и 'Copyright'. И 'InspirationGenerator', и его дочерний компонент 'FancyText' помечены как отрисовываемые на клиенте.">
Дерево рендеринга для приложения React Server Components. `InspirationGenerator` и его дочерний компонент `FancyText` — это компоненты, экспортированные из помеченного клиентом кода и считающиеся Client Components.
</Diagram>

Мы вводим следующие определения:

* **Client Components** — это компоненты в дереве рендеринга, которые отрисовываются на клиенте.
* **Server Components** — это компоненты в дереве рендеринга, которые отрисовываются на сервере.

Работая с примером приложения, `App`, `FancyText` и `Copyright` — все отрисовываются на сервере и считаются Server Components. Поскольку `InspirationGenerator.js` и его транзитивные зависимости помечены как клиентский код, компонент `InspirationGenerator` и его дочерний компонент `FancyText` являются Client Components.

<DeepDive>
#### Как `FancyText` может быть одновременно Server и Client Component? {/*how-is-fancytext-both-a-server-and-a-client-component*/}

Согласно приведенным выше определениям, компонент `FancyText` является одновременно Server и Client Component, как это возможно?

Во-первых, давайте уточним, что термин "компонент" не очень точен. Вот лишь два способа понимания "компонента":

1. "Компонент" может относиться к **определению компонента**. В большинстве случаев это будет функция.

```js
// Это определение компонента
function MyComponent() {
  return <p>My Component</p>
}
```

2. "Компонент" также может относиться к **использованию компонента** его определения.
```js
import MyComponent from './MyComponent';

function App() {
  // Это использование компонента
  return <MyComponent />;
}
```

Часто неточность не важна при объяснении концепций, но в данном случае она имеет значение.

Когда мы говорим о Server или Client Components, мы имеем в виду использования компонентов.

* Если компонент определен в модуле с директивой `'use client'`, или компонент импортируется и вызывается в Client Component, то использование компонента является Client Component.
* В противном случае использование компонента является Server Component.


<Diagram name="use_client_render_tree" height={150} width={450} alt="График дерева, где каждый узел представляет компонент и его дочерние элементы как дочерние компоненты. Верхний узел помечен как 'App', и у него есть два дочерних компонента: 'InspirationGenerator' и 'FancyText'. 'InspirationGenerator' имеет два дочерних компонента: 'FancyText' и 'Copyright'. И 'InspirationGenerator', и его дочерний компонент 'FancyText' помечены как отрисовываемые на клиенте.">Дерево рендеринга иллюстрирует использование компонентов.</Diagram>

Возвращаясь к вопросу о `FancyText`, мы видим, что определение компонента _не_ содержит директивы `'use client'`, и у него есть два использования.

Использование `FancyText` как дочернего элемента `App` помечает это использование как Server Component. Когда `FancyText` импортируется и вызывается под `InspirationGenerator`, это использование `FancyText` является Client Component, поскольку `InspirationGenerator` содержит директиву `'use client'`.

Это означает, что определение компонента для `FancyText` будет вычислено на сервере, а также загружено клиентом для отрисовки его использования в качестве Client Component.

</DeepDive>

<DeepDive>

#### Почему `Copyright` является Server Component? {/*why-is-copyright-a-server-component*/}

Поскольку `Copyright` отрисовывается как дочерний элемент Client Component `InspirationGenerator`, вы можете быть удивлены, что это Server Component.

Помните, что `'use client'` определяет границу между серверным и клиентским кодом в _дереве зависимостей модулей_, а не в дереве рендеринга.

<Diagram name="use_client_module_dependency" height={200} width={500} alt="График дерева с верхним узлом, представляющим модуль 'App.js'. 'App.js' имеет три дочерних элемента: 'Copyright.js', 'FancyText.js' и 'InspirationGenerator.js'. 'InspirationGenerator.js' имеет два дочерних элемента: 'FancyText.js' и 'inspirations.js'. Узлы под 'InspirationGenerator.js' и включая его имеют желтый фон, чтобы обозначить, что этот подграф отрисовывается на клиенте из-за директивы 'use client' в 'InspirationGenerator.js'.">
`'use client'` определяет границу между серверным и клиентским кодом в дереве зависимостей модулей.
</Diagram>

В дереве зависимостей модулей мы видим, что `App.js` импортирует и вызывает `Copyright` из модуля `Copyright.js`. Поскольку `Copyright.js` не содержит директивы `'use client'`, использование компонента отрисовывается на сервере. `App` отрисовывается на сервере, так как это корневой компонент.

Client Components могут отрисовывать Server Components, потому что вы можете передавать JSX в качестве пропсов. В данном случае `InspirationGenerator` получает `Copyright` как [children](/learn/passing-props-to-a-component#passing-jsx-as-children). Однако модуль `InspirationGenerator` никогда напрямую не импортирует модуль `Copyright` и не вызывает компонент, все это делается `App`. Фактически, компонент `Copyright` полностью выполняется до того, как `InspirationGenerator` начнет рендеринг.

Вывод заключается в том, что родительско-дочерняя связь рендеринга между компонентами не гарантирует одинаковую среду рендеринга.

</DeepDive>

### Когда использовать `'use client'` {/*when-to-use-use-client*/}

С помощью `'use client'` вы можете определять, когда компоненты являются Client Components. Поскольку Server Components являются значением по умолчанию, вот краткий обзор преимуществ и ограничений Server Components, чтобы определить, когда вам нужно пометить что-то как отрисовываемое на клиенте.

Для простоты мы говорим о Server Components, но те же принципы применимы ко всему коду в вашем приложении, который выполняется на сервере.

#### Преимущества Server Components {/*advantages*/}
* Server Components могут уменьшить объем кода, отправляемого и выполняемого клиентом. Только клиентские модули упаковываются и вычисляются клиентом.
* Server Components выигрывают от выполнения на сервере. Они могут получить доступ к локальной файловой системе и могут испытывать низкую задержку при получении данных и сетевых запросах.

#### Ограничения Server Components {/*limitations*/}
* Server Components не могут поддерживать интерактивность, так как обработчики событий должны быть зарегистрированы и вызваны клиентом.
	* Например, обработчики событий, такие как `onClick`, могут быть определены только в Client Components.
* Server Components не могут использовать большинство хуков.
	* При рендеринге Server Components их вывод представляет собой список компонентов для отрисовки клиентом. Server Components не сохраняются в памяти после рендеринга и не могут иметь собственного состояния.

### Сериализуемые типы, возвращаемые Server Components {/*serializable-types*/}

Как и в любом приложении React, родительские компоненты передают данные дочерним компонентам. Поскольку они отрисовываются в разных средах, передача данных из Server Component в Client Component требует дополнительного рассмотрения.

Значения пропсов, передаваемые из Server Component в Client Component, должны быть сериализуемыми.

Сериализуемые пропсы включают:
* Примитивы
	* [строка](https://developer.mozilla.org/en-US/docs/Glossary/String)
	* [число](https://developer.mozilla.org/en-US/docs/Glossary/Number)
	* [bigint](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt)
	* [логическое значение](https://developer.mozilla.org/en-US/docs/Glossary/Boolean)
	* [undefined](https://developer.mozilla.org/en-US/docs/Glossary/Undefined)
	* [null](https://developer.mozilla.org/en-US/docs/Glossary/Null)
	* [символ](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol), только символы, зарегистрированные в глобальном реестре символов через [`Symbol.for`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/for)
* Итерируемые объекты, содержащие сериализуемые значения
	* [Строка](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)
	* [Массив](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)
	* [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)
	* [Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)
	* [TypedArray](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) и [ArrayBuffer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)
* [Дата](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)
* Простые [объекты](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object): созданные с помощью [инициализаторов объектов](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer), с сериализуемыми свойствами
* Функции, являющиеся [Server Functions](/reference/rsc/server-functions)
* Элементы Client или Server Component (JSX)
* [Промисы](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)

Примечательно, что не поддерживаются:
* [Функции](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function), не экспортированные из помеченных клиентом модулей или помеченные [`'use server'`](/reference/rsc/use-server)
* [Классы](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Classes_in_JavaScript)
* Объекты, являющиеся экземплярами любого класса (кроме упомянутых встроенных) или объекты с [нулевым прототипом](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object#null-prototype_objects)
* Символы, не зарегистрированные глобально, например `Symbol('my new symbol')`


## Использование {/*usage*/}

### Создание интерактивных компонентов с состоянием {/*building-with-interactivity-and-state*/}

<Sandpack>

```js src/App.js
'use client';

import { useState } from 'react';

export default function Counter({initialValue = 0}) {
  const [countValue, setCountValue] = useState(initialValue);
  const increment = () => setCountValue(countValue + 1);
  const decrement = () => setCountValue(countValue - 1);
  return (
    <>
      <h2>Значение счётчика: {countValue}</h2>
      <button onClick={increment}>+1</button>
      <button onClick={decrement}>-1</button>
    </>
  );
}
```

</Sandpack>

Поскольку `Counter` требует хук `useState` и обработчики событий для увеличения или уменьшения значения, этот компонент должен быть Клиентским Компонентом и требовать директиву `'use client'` в начале файла.

В отличие от него, компонент, который отображает UI без интерактивности, не будет требовать статуса Клиентского Компонента.

```js
import { readFile } from 'node:fs/promises';
import Counter from './Counter';

export default async function CounterContainer() {
  const initialValue = await readFile('/path/to/counter_value');
  return <Counter initialValue={initialValue} />
}
```

Например, родительский компонент `Counter`, `CounterContainer`, не требует `'use client'`, так как он не интерактивен и не использует состояние. Кроме того, `CounterContainer` должен быть Серверным Компонентом, поскольку он считывает данные с локальной файловой системы на сервере, что возможно только в Серверном Компоненте.

Существуют также компоненты, которые не используют никаких серверных или клиентских функций и могут быть универсальными, независимо от того, где они рендерятся. В нашем предыдущем примере `FancyText` является одним из таких компонентов.

```js
export default function FancyText({title, text}) {
  return title
    ? <h1 className='fancy title'>{text}</h1>
    : <h3 className='fancy cursive'>{text}</h3>
}
```

В этом случае мы не добавляем директиву `'use client'`, в результате чего _вывод_ `FancyText` (а не его исходный код) отправляется в браузер при ссылке из Серверного Компонента. Как показано в предыдущем примере приложения Inspirations, `FancyText` используется как Серверный или Клиентский Компонент, в зависимости от того, где он импортируется и используется.

Но если бы HTML-вывод `FancyText` был большим по сравнению с его исходным кодом (включая зависимости), было бы эффективнее принудительно сделать его Клиентским Компонентом. Компоненты, возвращающие длинную строку SVG-пути, являются одним из случаев, когда может быть эффективнее принудительно сделать компонент Клиентским Компонентом.

### Использование клиентских API {/*using-client-apis*/}

Ваше React-приложение может использовать клиентские API, такие как браузерные API для веб-хранилищ, манипулирования аудио и видео, а также аппаратного обеспечения устройства, среди [прочих](https://developer.mozilla.org/en-US/docs/Web/API).

В этом примере компонент использует [DOM API](https://developer.mozilla.org/en-US/docs/Glossary/DOM) для манипулирования элементом [`canvas`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/canvas). Поскольку эти API доступны только в браузере, компонент должен быть помечен как Клиентский Компонент.

```js
'use client';

import {useRef, useEffect} from 'react';

export default function Circle() {
  const ref = useRef(null);
  useLayoutEffect(() => {
    const canvas = ref.current;
    const context = canvas.getContext('2d');
    context.reset();
    context.beginPath();
    context.arc(100, 75, 50, 0, 2 * Math.PI);
    context.stroke();
  });
  return <canvas ref={ref} />;
}
```

### Использование сторонних библиотек {/*using-third-party-libraries*/}

Часто в React-приложениях вы используете сторонние библиотеки для обработки общих шаблонов UI или логики.

Эти библиотеки могут полагаться на хуки компонентов или клиентские API. Сторонние компоненты, использующие любой из следующих React API, должны выполняться на клиенте:
* [createContext](/reference/react/createContext)
* Хуки [`react`](/reference/react/hooks) и [`react-dom`](/reference/react-dom/hooks), за исключением [`use`](/reference/react/use) и [`useId`](/reference/react/useId)
* [forwardRef](/reference/react/forwardRef)
* [memo](/reference/react/memo)
* [startTransition](/reference/react/startTransition)
* Если они используют клиентские API, например, вставку DOM или нативные представления платформы.

Если эти библиотеки были обновлены для совместимости с React Server Components, они уже будут содержать собственные маркеры `'use client'`, что позволит вам использовать их напрямую из ваших Серверных Компонентов. Если библиотека не была обновлена, или если компоненту требуются пропсы, такие как обработчики событий, которые могут быть указаны только на клиенте, вам может потребоваться добавить собственный файл Клиентского Компонента между сторонним Клиентским Компонентом и вашим Серверным Компонентом, где вы хотите его использовать.

[TODO]: <> (Устранение неполадок - нужны примеры использования)