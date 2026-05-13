---
title: "'use client'"
titleForTitleTag: "Директива `'use client'`"
---

<RSC>

`'use client'` используется с [React Server Components](/reference/rsc/server-components).

</RSC>

<Intro>

`'use client'` позволяет указать, какой код выполняется на клиенте.

</Intro>

<InlineToc />

---


## Ссылка {/*reference*/}

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

Когда файл, помеченный как `'use client'`, импортируется из серверного компонента, [совместимые бандлеры](/learn/start-a-new-react-project#bleeding-edge-react-frameworks) будут рассматривать импорт модуля как границу между кодом, выполняемым на сервере, и кодом, выполняемым на клиенте.

Поскольку `formatDate` и `Button` являются зависимостями `RichTextEditor`, они также будут оцениваться на клиенте, независимо от того, содержат ли их модули директиву `'use client'`. Обратите внимание, что один модуль может оцениваться на сервере при импорте из серверного кода и на клиенте при импорте из клиентского кода.

#### Предостережения {/*caveats*/}

*   `'use client'` должна быть в самом начале файла, над любыми импортами или другим кодом (комментарии допустимы). Они должны быть написаны в одинарных или двойных кавычках, но не в обратных кавычках.
*   Когда модуль с `'use client'` импортируется из другого модуля, отображаемого на клиенте, директива не имеет никакого эффекта.
*   Когда модуль компонента содержит директиву `'use client'`, любое использование этого компонента гарантированно является клиентским компонентом. Однако компонент все равно может оцениваться на клиенте, даже если он не содержит директивы `'use client'`.
    *   Использование компонента считается клиентским компонентом, если он определен в модуле с директивой `'use client'` или когда он является транзитивной зависимостью модуля, содержащего директиву `'use client'`. В противном случае это серверный компонент.
*   Код, помеченный для клиентской оценки, не ограничивается компонентами. Весь код, который является частью поддерева клиентского модуля, отправляется и выполняется клиентом.
*   Когда модуль, оцениваемый сервером, импортирует значения из модуля `'use client'`, значения должны быть либо компонентом React, либо [поддерживаемыми сериализуемыми значениями пропсов](#passing-props-from-server-to-client-components), чтобы быть переданными клиентскому компоненту. Любой другой вариант использования вызовет исключение.

### Как `'use client'` помечает клиентский код {/*how-use-client-marks-client-code*/}

В приложении React компоненты часто разделяются на отдельные файлы или [модули](/learn/importing-and-exporting-components#exporting-and-importing-a-component).

Для приложений, использующих React Server Components, приложение по умолчанию отображается на сервере. `'use client'` вводит границу между сервером и клиентом в [дереве зависимостей модулей](/learn/understanding-your-ui-as-a-tree#the-module-dependency-tree), эффективно создавая поддерево клиентских модулей.

Чтобы лучше проиллюстрировать это, рассмотрим следующее приложение React Server Components.

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

В дереве зависимостей модулей этого примера приложения директива `'use client'` в `InspirationGenerator.js` помечает этот модуль и все его транзитивные зависимости как клиентские модули. Поддерево, начинающееся с `InspirationGenerator.js`, теперь помечено как клиентские модули.

<Diagram name="use_client_module_dependency" height={250} width={545} alt="Граф-дерево, где верхний узел представляет модуль 'App.js'. 'App.js' имеет три дочерних элемента: 'Copyright.js', 'FancyText.js' и 'InspirationGenerator.js'. 'InspirationGenerator.js' имеет два дочерних элемента: 'FancyText.js' и 'inspirations.js'. Узлы под 'InspirationGenerator.js' и включая его имеют желтый фон, чтобы обозначить, что этот подграф отображается на клиенте из-за директивы 'use client' в 'InspirationGenerator.js'.">
`'use client'` сегментирует дерево зависимостей модулей приложения React Server Components, помечая `InspirationGenerator.js` и все его зависимости как отображаемые на клиенте.
</Diagram>

Во время рендеринга фреймворк будет отображать корневой компонент на сервере и продолжать работу через [дерево рендеринга](/learn/understanding-your-ui-as-a-tree#the-render-tree), отказываясь от оценки любого кода, импортированного из клиентского кода.

Затем часть дерева рендеринга, отображаемая на сервере, отправляется клиенту. Клиент, с загруженным клиентским кодом, затем завершает рендеринг остальной части дерева.

<Diagram name="use_client_render_tree" height={250} width={500} alt="Граф-дерево, где каждый узел представляет компонент и его дочерние элементы как дочерние компоненты. Узел верхнего уровня помечен как 'App', и у него есть два дочерних компонента 'InspirationGenerator' и 'FancyText'. 'InspirationGenerator' имеет два дочерних компонента: 'FancyText' и 'Copyright'. И 'InspirationGenerator', и его дочерний компонент 'FancyText' помечены для отображения на клиенте.">
Дерево рендеринга для приложения React Server Components. `InspirationGenerator` и его дочерний компонент `FancyText` — это компоненты, экспортированные из клиентского кода и считающиеся клиентскими компонентами.
</Diagram>

Мы вводим следующие определения:

*   **Клиентские компоненты** — это компоненты в дереве рендеринга, которые отображаются на клиенте.
*   **Серверные компоненты** — это компоненты в дереве рендеринга, которые отображаются на сервере.

Работая с примером приложения, `App`, `FancyText` и `Copyright` отображаются на сервере и считаются серверными компонентами. Поскольку `InspirationGenerator.js` и его транзитивные зависимости помечены как клиентский код, компонент `InspirationGenerator` и его дочерний компонент `FancyText` являются клиентскими компонентами.

<DeepDive>
#### Как `FancyText` является одновременно серверным и клиентским компонентом? {/*how-is-fancytext-both-a-server-and-a-client-component*/}

По приведенным выше определениям компонент `FancyText` является одновременно серверным и клиентским компонентом, как это возможно?

Во-первых, давайте уточним, что термин «компонент» не очень точен. Вот только два способа понимания «компонента»:

1.  «Компонент» может относиться к **определению компонента**. В большинстве случаев это будет функция.

    ```js
    // Это определение компонента
    function MyComponent() {
      return <p>My Component</p>
    }
    ```

2.  «Компонент» также может относиться к **использованию компонента** его определения.
    ```js
    import MyComponent from './MyComponent';

    function App() {
      // Это использование компонента
      return <MyComponent />;
    }
    ```

Часто неточность не важна при объяснении концепций, но в данном случае это так.

Когда мы говорим о серверных или клиентских компонентах, мы имеем в виду использование компонентов.

*   Если компонент определен в модуле с директивой `'use client'` или компонент импортируется и вызывается в клиентском компоненте, то использование компонента является клиентским компонентом.
*   В противном случае использование компонента является серверным компонентом.

<Diagram name="use_client_render_tree" height={150} width={450} alt="Граф-дерево, где каждый узел представляет компонент и его дочерние элементы как дочерние компоненты. Узел верхнего уровня помечен как 'App', и у него есть два дочерних компонента 'InspirationGenerator' и 'FancyText'. 'InspirationGenerator' имеет два дочерних компонента: 'FancyText' и 'Copyright'. И 'InspirationGenerator', и его дочерний компонент 'FancyText' помечены для отображения на клиенте.">Дерево рендеринга иллюстрирует использование компонентов.</Diagram>

Возвращаясь к вопросу о `FancyText`, мы видим, что определение компонента _не_ содержит директиву `'use client'`, и у него есть два использования.

Использование `FancyText` в качестве дочернего элемента `App` помечает это использование как серверный компонент. Когда `FancyText` импортируется и вызывается в `InspirationGenerator`, это использование `FancyText` является клиентским компонентом, поскольку `InspirationGenerator` содержит директиву `'use client'`.

Это означает, что определение компонента для `FancyText` будет оцениваться как на сервере, так и загружаться клиентом для отображения его использования в клиентском компоненте.

</DeepDive>

<DeepDive>

#### Почему `Copyright` является серверным компонентом? {/*why-is-copyright-a-server-component*/}

Поскольку `Copyright` отображается как дочерний элемент клиентского компонента `InspirationGenerator`, вы можете удивиться, что это серверный компонент.

Напомним, что `'use client'` определяет границу между серверным и клиентским кодом в _дереве зависимостей модулей_, а не в дереве рендеринга.

<Diagram name="use_client_module_dependency" height={200} width={500} alt="Граф-дерево, где верхний узел представляет модуль 'App.js'. 'App.js' имеет три дочерних элемента: 'Copyright.js', 'FancyText.js' и 'InspirationGenerator.js'. 'InspirationGenerator.js' имеет два дочерних элемента: 'FancyText.js' и 'inspirations.js'. Узлы под 'InspirationGenerator.js' и включая его имеют желтый фон, чтобы обозначить, что этот подграф отображается на клиенте из-за директивы 'use client' в 'InspirationGenerator.js'.">
`'use client'` определяет границу между серверным и клиентским кодом в дереве зависимостей модулей.
</Diagram>

В дереве зависимостей модулей мы видим, что `App.js` импортирует и вызывает `Copyright` из модуля `Copyright.js`. Поскольку `Copyright.js` не содержит директиву `'use client'`, использование компонента отображается на сервере. `App` отображается на сервере, так как это корневой компонент.

Клиентские компоненты могут отображать серверные компоненты, потому что вы можете передавать JSX в качестве пропсов. В этом случае `InspirationGenerator` получает `Copyright` в качестве [children](/learn/passing-props-to-a-component#passing-jsx-as-children). Однако модуль `InspirationGenerator` никогда напрямую не импортирует модуль `Copyright` и не вызывает компонент, все это делается `App`. Фактически, компонент `Copyright` полностью выполняется до того, как `InspirationGenerator` начнет рендеринг.

Вывод заключается в том, что родительско-дочерние отношения рендеринга между компонентами не гарантируют одинаковую среду рендеринга.

</DeepDive>

### Когда использовать `'use client'` {/*when-to-use-use-client*/}

С помощью `'use client'` вы можете определить, когда компоненты являются клиентскими компонентами. Поскольку серверные компоненты являются компонентами по умолчанию, вот краткий обзор преимуществ и ограничений серверных компонентов, чтобы определить, когда вам нужно пометить что-либо как отображаемое на клиенте.

Для простоты мы говорим о серверных компонентах, но те же принципы применимы ко всему коду в вашем приложении, который выполняется на сервере.

#### Преимущества серверных компонентов {/*advantages*/}

*   Серверные компоненты могут уменьшить объем кода, отправляемого и выполняемого клиентом. Только клиентские модули пакетируются и оцениваются клиентом.
*   Серверные компоненты выигрывают от работы на сервере. Они могут получить доступ к локальной файловой системе и могут испытывать низкую задержку при выборке данных и сетевых запросах.

#### Ограничения серверных компонентов {/*limitations*/}

*   Серверные компоненты не могут поддерживать взаимодействие, поскольку обработчики событий должны быть зарегистрированы и запущены клиентом.
    *   Например, обработчики событий, такие как `onClick`, могут быть определены только в клиентских компонентах.
*   Серверные компоненты не могут использовать большинство хуков.
    *   Когда серверные компоненты отображаются, их вывод по существу представляет собой список компонентов для отображения клиентом. Серверные компоненты не сохраняются в памяти после рендеринга и не могут иметь собственное состояние.

### Сериализуемые типы, возвращаемые серверными компонентами {/*serializable-types*/}

Как и в любом приложении React, родительские компоненты передают данные дочерним компонентам. Поскольку они отображаются в разных средах, передача данных от серверного компонента к клиентскому компоненту требует дополнительного рассмотрения.

Значения пропсов, переданные от серверного компонента к клиентскому компоненту, должны быть сериализуемыми.

Сериализуемые пропсы включают:

*   Примитивы
    *   [string](https://developer.mozilla.org/en-US/docs/Glossary/String)
    *   [number](https://developer.mozilla.org/en-US/docs/Glossary/Number)
    *   [bigint](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt)
    *   [boolean](https://developer.mozilla.org/en-US/docs/Glossary/Boolean)
    *   [undefined](https://developer.mozilla.org/en-US/docs/Glossary/Undefined)
    *   [null](https://developer.mozilla.org/en-US/docs/Glossary/Null)
    *   [symbol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol), только символы, зарегистрированные в глобальном реестре Symbol через [`Symbol.for`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/for)
*   Итерируемые объекты, содержащие сериализуемые значения
    *   [String](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)
    *   [Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)
    *   [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)
    *   [Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)
    *   [TypedArray](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) и [ArrayBuffer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)
*   [Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)
*   Простые [объекты](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object): созданные с помощью [инициализаторов объектов](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer), со сериализуемыми свойствами
*   Функции, являющиеся [серверными функциями](/reference/rsc/server-functions)
*   Элементы клиентских или серверных компонентов (JSX)
*   [Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)

В частности, это не поддерживается:

*   [Функции](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function), которые не экспортируются из клиентских модулей или не помечены с помощью [`'use server'`](/reference/rsc/use-server)
*   [Классы](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Classes_in_JavaScript)
*   Объекты, являющиеся экземплярами любого класса (кроме встроенных, упомянутых выше) или объекты с [нулевым прототипом](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object#null-prototype_objects)
*   Символы, не зарегистрированные глобально, например, `Symbol('my new symbol')`



## Использование {/*usage*/}

### Создание с интерактивностью и состоянием {/*building-with-interactivity-and-state*/}

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
      <h2>Count Value: {countValue}</h2>
      <button onClick={increment}>+1</button>
      <button onClick={decrement}>-1</button>
    </>
  );
}
```

</Sandpack>

Поскольку `Counter` требует как хук `useState`, так и обработчики событий для увеличения или уменьшения значения, этот компонент должен быть клиентским компонентом и потребует директиву `'use client'` вверху.

В отличие от этого, компонент, который отображает UI без взаимодействия, не должен быть клиентским компонентом.

```js
import { readFile } from 'node:fs/promises';
import Counter from './Counter';

export default async function CounterContainer() {
  const initialValue = await readFile('/path/to/counter_value');
  return <Counter initialValue={initialValue} />
}
```

Например, родительский компонент `Counter`, `CounterContainer`, не требует `'use client'`, поскольку он не является интерактивным и не использует состояние. Кроме того, `CounterContainer` должен быть серверным компонентом, поскольку он читает из локальной файловой системы на сервере, что возможно только в серверном компоненте.

Есть также компоненты, которые не используют ни серверные, ни клиентские функции и могут быть агностичны к тому, где они отображаются. В нашем предыдущем примере `FancyText` является одним из таких компонентов.

```js
export default function FancyText({title, text}) {
  return title
    ? <h1 className='fancy title'>{text}</h1>
    : <h3 className='fancy cursive'>{text}</h3>
}
```

В этом случае мы не добавляем директиву `'use client'`, в результате чего _вывод_ `FancyText` (а не его исходный код) отправляется в браузер при ссылке из серверного компонента. Как продемонстрировано в предыдущем примере приложения Inspirations, `FancyText` используется как серверный, так и клиентский компонент, в зависимости от того, где он импортируется и используется.

Но если HTML-вывод `FancyText` был большим по сравнению с его исходным кодом (включая зависимости), было бы более эффективно всегда принудительно делать его клиентским компонентом. Компоненты, которые возвращают длинную строку пути SVG, являются одним из случаев, когда может быть более эффективно принудительно сделать компонент клиентским компонентом.

### Использование клиентских API {/*using-client-apis*/}

Ваше React-приложение может использовать клиентские API, такие как API браузера для веб-хранилища, манипулирования аудио и видео, а также аппаратного обеспечения устройства, среди [прочих](https://developer.mozilla.org/en-US/docs/Web/API).

В этом примере компонент использует [DOM API](https://developer.mozilla.org/ru/docs/Glossary/DOM) для управления элементом [`canvas`](https://developer.mozilla.org/ru/docs/Web/HTML/Element/canvas). Поскольку эти API доступны только в браузере, он должен быть помечен как клиентский компонент.

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

Часто в React-приложении вы будете использовать сторонние библиотеки для обработки общих шаблонов UI или логики.

Эти библиотеки могут полагаться на хуки компонентов или клиентские API. Сторонние компоненты, которые используют какие-либо из следующих API React, должны запускаться на клиенте:
* [createContext](/reference/react/createContext)
* Хуки [`react`](/reference/react/hooks) и [`react-dom`](/reference/react-dom/hooks), исключая [`use`](/reference/react/use) и [`useId`](/reference/react/useId)
* [forwardRef](/reference/react/forwardRef)
* [memo](/reference/react/memo)
* [startTransition](/reference/react/startTransition)
* Если они используют клиентские API, например, вставку DOM или представления нативных платформ

Если эти библиотеки были обновлены для совместимости с React Server Components, то они уже будут включать собственные маркеры `'use client'`, что позволит вам использовать их непосредственно из ваших Server Components. Если библиотека не была обновлена, или если компонент нуждается в пропсах, таких как обработчики событий, которые могут быть указаны только на клиенте, вам может потребоваться добавить свой собственный файл Client Component между сторонним Client Component и вашим Server Component, где вы хотите его использовать.

[TODO]: <> (Устранение неполадок - нужны варианты использования)