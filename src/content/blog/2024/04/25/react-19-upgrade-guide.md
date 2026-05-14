---
title: "Руководство по обновлению React 19"
author: Ricky Hanlon
date: 2024/04/25
description: Улучшения, добавленные в React 19, требуют некоторых критических
  изменений, но мы постарались сделать обновление максимально плавным, и мы не
  ожидаем, что эти изменения повлияют на большинство приложений. В этой статье
  мы расскажем вам, как обновить приложения и библиотеки до React 19.
---
25 апреля 2024 г. от [Рики Хэнлона](https://twitter.com/rickhanlonii)

---

<Intro>

Улучшения, добавленные в React 19, требуют некоторых критических изменений, но мы постарались сделать обновление максимально плавным, и мы не ожидаем, что эти изменения повлияют на большинство приложений.

</Intro>

<Note>

#### Также был опубликован React 18.3 {/*react-18-3*/}

Чтобы упростить обновление до React 19, мы опубликовали релиз `react@18.3`, который идентичен 18.2, но добавляет предупреждения об устаревших API и других изменениях, необходимых для React 19.

Мы рекомендуем сначала обновиться до React 18.3, чтобы выявить какие-либо проблемы, прежде чем обновляться до React 19.

Список изменений в 18.3 см. в [Заметках о выпуске](https://github.com/facebook/react/blob/main/CHANGELOG.md#1830-april-25-2024).

</Note>

В этой статье мы расскажем вам о шагах по обновлению до React 19:

- [Установка](#installing)
- [Codemods](#codemods)
- [Критические изменения](#breaking-changes)
- [Новые устаревания](#new-deprecations)
- [Заметные изменения](#notable-changes)
- [Изменения TypeScript](#typescript-changes)
- [Журнал изменений](#changelog)

Если вы хотите помочь нам протестировать React 19, выполните шаги, описанные в этом руководстве по обновлению, и [сообщите о любых проблемах](https://github.com/facebook/react/issues/new?assignees=&labels=React+19&projects=&template=19.md&title=%5BReact+19%5D), с которыми вы столкнетесь. Список новых функций, добавленных в React 19, см. в [статье о выпуске React 19](/blog/2024/12/05/react-19).

---

## Установка {/*installing*/}

<Note>

#### Теперь требуется новый JSX-трансформатор {/*new-jsx-transform-is-now-required*/}

Мы представили [новый JSX-трансформатор](https://legacy.reactjs.org/blog/2020/09/22/introducing-the-new-jsx-transform.html) в 2020 году, чтобы улучшить размер пакета и использовать JSX без импорта React. В React 19 мы добавляем дополнительные улучшения, такие как использование ref в качестве пропса и улучшения скорости JSX, которые требуют нового трансформатора.

Если новый трансформатор не включен, вы увидите следующее предупреждение:

<ConsoleBlockMulti>

<ConsoleLogLine level="error">

Ваше приложение (или одна из его зависимостей) использует устаревший JSX-трансформатор. Обновитесь до современного JSX-трансформатора для повышения производительности: https://react.dev/link/new-jsx-transform

</ConsoleLogLine>

</ConsoleBlockMulti>

Мы ожидаем, что большинство приложений не пострадают, поскольку трансформатор уже включен в большинстве сред. Инструкции по ручному обновлению см. в [анонсе](https://legacy.reactjs.org/blog/2020/09/22/introducing-the-new-jsx-transform.html).

</Note>

Чтобы установить последнюю версию React и React DOM:

```bash
npm install --save-exact react@^19.0.0 react-dom@^19.0.0
```

Или, если вы используете Yarn:

```bash
yarn add --exact react@^19.0.0 react-dom@^19.0.0
```

Если вы используете TypeScript, вам также необходимо обновить типы.
```bash
npm install --save-exact @types/react@^19.0.0 @types/react-dom@^19.0.0
```

Или, если вы используете Yarn:
```bash
yarn add --exact @types/react@^19.0.0 @types/react-dom@^19.0.0
```

Мы также включаем codemod для наиболее распространенных замен. См. [Изменения TypeScript](#typescript-changes) ниже.

## Codemods {/*codemods*/}

Чтобы помочь с обновлением, мы работали с командой [codemod.com](https://codemod.com), чтобы опубликовать codemods, которые автоматически обновят ваш код до многих новых API и шаблонов в React 19.

Все codemods доступны в репозитории [`react-codemod`](https://github.com/reactjs/react-codemod), и команда Codemod присоединилась к поддержке codemods. Чтобы запустить эти codemods, мы рекомендуем использовать команду `codemod` вместо `react-codemod`, потому что она работает быстрее, обрабатывает более сложные миграции кода и обеспечивает лучшую поддержку TypeScript.

<Note>

#### Запустите все codemods React 19 {/*run-all-react-19-codemods*/}

Запустите все codemods, перечисленные в этом руководстве, с помощью рецепта React 19 `codemod`:

```bash
npx codemod@latest react/19/migration-recipe
```

Это запустит следующие codemods из `react-codemod`:
- [`replace-reactdom-render`](https://github.com/reactjs/react-codemod?tab=readme-ov-file#replace-reactdom-render) 
- [`replace-string-ref`](https://github.com/reactjs/react-codemod?tab=readme-ov-file#replace-string-ref)
- [`replace-act-import`](https://github.com/reactjs/react-codemod?tab=readme-ov-file#replace-act-import)
- [`replace-use-form-state`](https://github.com/reactjs/react-codemod?tab=readme-ov-file#replace-use-form-state) 
- [`prop-types-typescript`](https://github.com/reactjs/react-codemod#react-proptypes-to-prop-types)

Это не включает изменения TypeScript. См. [Изменения TypeScript](#typescript-changes) ниже.

</Note>

Изменения, включающие codemod, включают приведенную ниже команду.

Список всех доступных codemods см. в репозитории [`react-codemod`](https://github.com/reactjs/react-codemod).

## Критические изменения {/*breaking-changes*/}

### Ошибки в render не перебрасываются повторно {/*errors-in-render-are-not-re-thrown*/}

В предыдущих версиях React ошибки, возникающие во время рендеринга, перехватывались и перебрасывались повторно. В DEV мы также записывали в `console.error`, что приводило к дублированию журналов ошибок.

В React 19 мы [улучшили обработку ошибок](/blog/2024/04/25/react-19#error-handling), чтобы уменьшить дублирование, не перебрасывая повторно:

- **Неперехваченные ошибки**: Ошибки, которые не перехватываются границей ошибок, сообщаются в `window.reportError`.
- **Перехваченные ошибки**: Ошибки, которые перехватываются границей ошибок, сообщаются в `console.error`.

Это изменение не должно повлиять на большинство приложений, но если ваш отчет об ошибках в продакшене зависит от повторного перебрасывания ошибок, вам может потребоваться обновить обработку ошибок. Чтобы поддержать это, мы добавили новые методы в `createRoot` и `hydrateRoot` для пользовательской обработки ошибок:

```js [[1, 2, "onUncaughtError"], [2, 5, "onCaughtError"]]
const root = createRoot(container, {
  onUncaughtError: (error, errorInfo) => {
    // ... log error report
  },
  onCaughtError: (error, errorInfo) => {
    // ... log error report
  }
});
```

Для получения дополнительной информации см. документацию по [`createRoot`](https://react.dev/reference/react-dom/client/createRoot) и [`hydrateRoot`](https://react.dev/reference/react-dom/client/hydrateRoot).

### Удалены устаревшие API React {/*removed-deprecated-react-apis*/}

#### Удалено: `propTypes` и `defaultProps` для функций {/*removed-proptypes-and-defaultprops*/}
`PropTypes` были устаревшими в [апреле 2017 г. (v15.5.0)](https://legacy.reactjs.org/blog/2017/04/07/react-v15.5.0.html#new-deprecation-warnings).

В React 19 мы удаляем проверки `propType` из пакета React, и их использование будет молча игнорироваться. Если вы используете `propTypes`, мы рекомендуем перейти на TypeScript или другое решение для проверки типов.

Мы также удаляем `defaultProps` из функциональных компонентов вместо параметров по умолчанию ES6. Классовые компоненты будут продолжать поддерживать `defaultProps`, так как альтернативы ES6 нет.

```js
// Before
import PropTypes from 'prop-types';

function Heading({text}) {
  return <h1>{text}</h1>;
}
Heading.propTypes = {
  text: PropTypes.string,
};
Heading.defaultProps = {
  text: 'Hello, world!',
};
```
```ts
// After
interface Props {
  text?: string;
}
function Heading({text = 'Hello, world!'}: Props) {
  return <h1>{text}</h1>;
}
```

<Note>

Codemod `propTypes` в TypeScript с помощью:

```bash
npx codemod@latest react/prop-types-typescript
```

</Note>

#### Удалено: Legacy Context с использованием `contextTypes` и `getChildContext` {/*removed-removing-legacy-context*/}

Legacy Context был устаревшим в [октябре 2018 г. (v16.6.0)](https://legacy.reactjs.org/blog/2018/10/23/react-v-16-6.html).

Legacy Context был доступен только в классовых компонентах с использованием API `contextTypes` и `getChildContext` и был заменен на `contextType` из-за незначительных ошибок, которые было легко пропустить. В React 19 мы удаляем Legacy Context, чтобы сделать React немного меньше и быстрее.

Если вы все еще используете Legacy Context в классовых компонентах, вам нужно будет перейти на новый API `contextType`:

```js {5-11,19-21}
// Before
import PropTypes from 'prop-types';

class Parent extends React.Component {
  static childContextTypes = {
    foo: PropTypes.string.isRequired,
  };

  getChildContext() {
    return { foo: 'bar' };
  }

  render() {
    return <Child />;
  }
}

class Child extends React.Component {
  static contextTypes = {
    foo: PropTypes.string.isRequired,
  };

  render() {
    return <div>{this.context.foo}</div>;
  }
}
```

```js {2,7,9,15}
// After
const FooContext = React.createContext();

class Parent extends React.Component {
  render() {
    return (
      <FooContext value='bar'>
        <Child />
      </FooContext>
    );
  }
}

class Child extends React.Component {
  static contextType = FooContext;

  render() {
    return <div>{this.context}</div>;
  }
}
```

#### Удалено: string refs {/*removed-string-refs*/}
String refs были устаревшими в [марте 2018 г. (v16.3.0)](https://legacy.reactjs.org/blog/2018/03/27/update-on-async-rendering.html).

Классовые компоненты поддерживали string refs до замены обратными вызовами ref из-за [нескольких недостатков](https://github.com/facebook/react/issues/1373). В React 19 мы удаляем string refs, чтобы сделать React проще и понятнее.

Если вы все еще используете string refs в классовых компонентах, вам нужно будет перейти на обратные вызовы ref:

```js {4,8}
// Before
class MyComponent extends React.Component {
  componentDidMount() {
    this.refs.input.focus();
  }

  render() {
    return <input ref='input' />;
  }
}
```

```js {4,8}
// After
class MyComponent extends React.Component {
  componentDidMount() {
    this.input.focus();
  }

  render() {
    return <input ref={input => this.input = input} />;
  }
}
```

<Note>

Codemod string refs с обратными вызовами `ref`:

```bash
npx codemod@latest react/19/replace-string-ref
```

</Note>

#### Удалено: фабрики шаблонов паттерна {/*removed-module-pattern-factories*/}
Фабрики шаблонов паттерна были устаревшими в [августе 2019 г. (v16.9.0)](https://legacy.reactjs.org/blog/2019/08/08/react-v16.9.0.html#deprecating-module-pattern-factories).

Этот шаблон редко использовался, и его поддержка приводит к тому, что React становится немного больше и медленнее, чем необходимо. В React 19 мы удаляем поддержку фабрик шаблонов паттерна, и вам нужно будет перейти на обычные функции:

```js
// Before
function FactoryComponent() {
  return { render() { return <div />; } }
}
```

```js
// After
function FactoryComponent() {
  return <div />;
}
```

#### Удалено: `React.createFactory` {/*removed-createfactory*/}
`createFactory` был устаревшим в [феврале 2020 г. (v16.13.0)](https://legacy.reactjs.org/blog/2020/02/26/react-v16.13.0.html#deprecating-createfactory).

Использование `createFactory` было распространено до широкой поддержки JSX, но сегодня оно редко используется и может быть заменено JSX. В React 19 мы удаляем `createFactory`, и вам нужно будет перейти на JSX:

```js
// Before
import { createFactory } from 'react';

const button = createFactory('button');
```

```js
// After
const button = <button />;
```

#### Удалено: `react-test-renderer/shallow` {/*removed-react-test-renderer-shallow*/}

В React 18 мы обновили `react-test-renderer/shallow` для повторного экспорта [react-shallow-renderer](https://github.com/enzymejs/react-shallow-renderer). В React 19 мы удаляем `react-test-render/shallow`, чтобы предпочесть установку пакета напрямую:

```bash
npm install react-shallow-renderer --save-dev
```
```diff
- import ShallowRenderer from 'react-test-renderer/shallow';
+ import ShallowRenderer from 'react-shallow-renderer';
```

<Note>

##### Пожалуйста, пересмотрите поверхностный рендеринг {/*please-reconsider-shallow-rendering*/}

Поверхностный рендеринг зависит от внутренних компонентов React и может заблокировать вам будущие обновления. Мы рекомендуем перенести ваши тесты в [@testing-library/react](https://testing-library.com/docs/react-testing-library/intro/) или [@testing-library/react-native](https://testing-library.com/docs/react-native-testing-library/intro).

</Note>

### Удалены устаревшие API React DOM {/*removed-deprecated-react-dom-apis*/}

#### Удалено: `react-dom/test-utils` {/*removed-react-dom-test-utils*/}

Мы переместили `act` из пакета `react-dom/test-utils` в пакет `react`:

<ConsoleBlockMulti>

<ConsoleLogLine level="error">

`ReactDOMTestUtils.act` устарел в пользу `React.act`. Импортируйте `act` из `react` вместо `react-dom/test-utils`. Дополнительную информацию см. на странице https://react.dev/warnings/react-dom-test-utils.

</ConsoleLogLine>

</ConsoleBlockMulti>

Чтобы исправить это предупреждение, вы можете импортировать `act` из `react`:

```diff
- import {act} from 'react-dom/test-utils'
+ import {act} from 'react';
```

Все остальные функции `test-utils` были удалены. Эти утилиты были нераспространены и слишком упрощали зависимость от низкоуровневых деталей реализации ваших компонентов и React. В React 19 эти функции будут выдавать ошибку при вызове, а их экспорты будут удалены в будущей версии.

См. [страницу предупреждения](https://react.dev/warnings/react-dom-test-utils) для получения альтернатив.

<Note>

Codemod `ReactDOMTestUtils.act` в `React.act`:

```bash
npx codemod@latest react/19/replace-act-import
```

</Note>

#### Удалено: `ReactDOM.render` {/*removed-reactdom-render*/}

`ReactDOM.render` был устаревшим в [марте 2022 г. (v18.0.0)](https://react.dev/blog/2022/03/08/react-18-upgrade-guide). В React 19 мы удаляем `ReactDOM.render`, и вам нужно будет перейти на использование [`ReactDOM.createRoot`](https://react.dev/reference/react-dom/client/createRoot):

```js
// Before
import {render} from 'react-dom';
render(<App />, document.getElementById('root'));

// After
import {createRoot} from 'react-dom/client';
const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

<Note>

Codemod `ReactDOM.render` в `ReactDOMClient.createRoot`:

```bash
npx codemod@latest react/19/replace-reactdom-render
```

</Note>

#### Удалено: `ReactDOM.hydrate` {/*removed-reactdom-hydrate*/}

`ReactDOM.hydrate` был устаревшим в [марте 2022 г. (v18.0.0)](https://react.dev/blog/2022/03/08/react-18-upgrade-guide). В React 19 мы удаляем `ReactDOM.hydrate`, вам нужно будет перейти на использование [`ReactDOM.hydrateRoot`](https://react.dev/reference/react-dom/client/hydrateRoot),

```js
// Before
import {hydrate} from 'react-dom';
hydrate(<App />, document.getElementById('root'));

// After
import {hydrateRoot} from 'react-dom/client';
hydrateRoot(document.getElementById('root'), <App />);
```

<Note>

Codemod `ReactDOM.hydrate` в `ReactDOMClient.hydrateRoot`:

```bash
npx codemod@latest react/19/replace-reactdom-render
```

</Note>

#### Удалено: `unmountComponentAtNode` {/*removed-unmountcomponentatnode*/}

`ReactDOM.unmountComponentAtNode` был устаревшим в [марте 2022 г. (v18.0.0)](https://react.dev/blog/2022/03/08/react-18-upgrade-guide). В React 19 вам нужно будет перейти на использование `root.unmount()`.

```js
// Before
unmountComponentAtNode(document.getElementById('root'));

// After
root.unmount();
```

Дополнительную информацию см. в разделе `root.unmount()` для [`createRoot`](https://react.dev/reference/react-dom/client/createRoot#root-unmount) и [`hydrateRoot`](https://react.dev/reference/react-dom/client/hydrateRoot#root-unmount).

<Note>

Codemod `unmountComponentAtNode` в `root.unmount`:

```bash
npx codemod@latest react/19/replace-reactdom-render
```

</Note>

#### Удалено: `ReactDOM.findDOMNode` {/*removed-reactdom-finddomnode*/}

`ReactDOM.findDOMNode` был [устаревшим в октябре 2018 г. (v16.6.0)](https://legacy.reactjs.org/blog/2018/10/23/react-v-16-6.html#deprecations-in-strictmode).

Мы удаляем `findDOMNode`, потому что это был устаревший способ обхода, который был медленным в исполнении, хрупким для рефакторинга, возвращал только первого дочернего элемента и нарушал уровни абстракции (подробнее [здесь](https://legacy.reactjs.org/docs/strict-mode.html#warning-about-deprecated-finddomnode-usage)). Вы можете заменить `ReactDOM.findDOMNode` на [DOM refs](/learn/manipulating-the-dom-with-refs):

```js
// Before
import {findDOMNode} from 'react-dom';

function AutoselectingInput() {
  useEffect(() => {
    const input = findDOMNode(this);
    input.select()
  }, []);

  return <input defaultValue="Hello" />;
}
```

```js
// After
function AutoselectingInput() {
  const ref = useRef(null);
  useEffect(() => {
    ref.current.select();
  }, []);

  return <input ref={ref} defaultValue="Hello" />
}
```

## Новые устаревания {/*new-deprecations*/}

### Устарело: `element.ref` {/*deprecated-element-ref*/}

React 19 поддерживает [`ref` как пропс](/blog/2024/04/25/react-19#ref-as-a-prop), поэтому мы устареваем `element.ref` вместо `element.props.ref`.

Доступ к `element.ref` выдаст предупреждение:

<ConsoleBlockMulti>

<ConsoleLogLine level="error">

Доступ к element.ref больше не поддерживается. ref теперь является обычным пропсом. Он будет удален из типа JSX Element в будущей версии.

</ConsoleLogLine>

</ConsoleBlockMulti>

### Устарело: `react-test-renderer` {/*deprecated-react-test-renderer*/}

Мы устареваем `react-test-renderer`, потому что он реализует собственную среду рендеринга, которая не соответствует среде, используемой пользователями, способствует тестированию деталей реализации и полагается на интроспекцию внутренних компонентов React.

Тестовый рендерер был создан до того, как появились более жизнеспособные стратегии тестирования, такие как [React Testing Library](https://testing-library.com), и теперь мы рекомендуем использовать современную библиотеку тестирования.

В React 19 `react-test-renderer` регистрирует предупреждение об устаревании и переключился на одновременный рендеринг. Мы рекомендуем перенести ваши тесты в [@testing-library/react](https://testing-library.com/docs/react-testing-library/intro/) или [@testing-library/react-native](https://testing-library.com/docs/react-native-testing-library/intro) для современного и хорошо поддерживаемого опыта тестирования.

## Заметные изменения {/*notable-changes*/}

### Изменения StrictMode {/*strict-mode-improvements*/}

React 19 включает в себя несколько исправлений и улучшений Strict Mode.

При двойном рендеринге в Strict Mode в режиме разработки `useMemo` и `useCallback` будут повторно использовать результаты мемоизации из первого рендеринга во время второго рендеринга. Компоненты, которые уже совместимы со Strict Mode, не должны замечать разницы в поведении.

Как и во всех поведениях Strict Mode, эти функции предназначены для упреждающего выявления ошибок в ваших компонентах во время разработки, чтобы вы могли исправить их до отправки в продакшен. Например, во время разработки Strict Mode будет дважды вызывать функции обратного вызова ref при первоначальном монтировании, чтобы имитировать то, что происходит, когда смонтированный компонент заменяется резервным вариантом Suspense.

### Улучшения Suspense {/*improvements-to-suspense*/}

В React 19, когда компонент приостанавливается, React немедленно зафиксирует резервный вариант ближайшей границы Suspense, не дожидаясь рендеринга всего дерева siblings. После фиксации резервного варианта React планирует другой рендеринг для приостановленных siblings, чтобы «предварительно разогреть» ленивые запросы в остальной части дерева:

<Diagram name="prerender" height={162} width={1270} alt="Диаграмма, показывающая дерево из трех компонентов, один родительский компонент с меткой Accordion и два дочерних компонента с меткой Panel. Оба компонента Panel содержат isActive со значением false.">

Раньше, когда компонент приостанавливался, приостановленные siblings рендерились, а затем фиксировался резервный вариант.

</Diagram>

<Diagram name="prewarm" height={162} width={1270} alt="Та же диаграмма, что и предыдущая, с выделенным значением isActive первого дочернего компонента Panel, указывающим на щелчок со значением isActive, установленным в true. Второй компонент Panel по-прежнему содержит значение false." >

В React 19, когда компонент приостанавливается, фиксируется резервный вариант, а затем рендерится приостановленные siblings.

</Diagram>

Это изменение означает, что резервные варианты Suspense отображаются быстрее, при этом по-прежнему разогревая ленивые запросы в приостановленном дереве.

### Удалены сборки UMD {/*umd-builds-removed*/}

UMD широко использовался в прошлом как удобный способ загрузки React без этапа сборки. Теперь есть современные альтернативы для загрузки модулей в виде скриптов в HTML-документах. Начиная с React 19, React больше не будет создавать сборки UMD, чтобы уменьшить сложность процесса тестирования и выпуска.

Чтобы загрузить React 19 с помощью тега script, мы рекомендуем использовать CDN на основе ESM, например [esm.sh](https://esm.sh/).

```html
<script type="module">
  import React from "https://esm.sh/react@19/?dev"
  import ReactDOMClient from "https://esm.sh/react-dom@19/client?dev"
  ...
</script>
```

### Библиотеки, зависящие от внутренних компонентов React, могут блокировать обновления {/*libraries-depending-on-react-internals-may-block-upgrades*/}

Этот выпуск включает изменения во внутренних компонентах React, которые могут повлиять на библиотеки, которые игнорируют наши просьбы не использовать внутренние компоненты, такие как `SECRET_INTERNALS_DO_NOT_USE_OR_YOU_WILL_BE_FIRED`. Эти изменения необходимы для реализации улучшений в React 19 и не нарушат работу библиотек, которые следуют нашим рекомендациям.

Основываясь на нашей [Политике версионирования](https://react.dev/community/versioning-policy#what-counts-as-a-breaking-change), эти обновления не указаны как критические изменения, и мы не включаем документацию о том, как их обновить. Рекомендуется удалить любой код, который зависит от внутренних компонентов.

Чтобы отразить влияние использования внутренних компонентов, мы переименовали суффикс `SECRET_INTERNALS` в:

`_DO_NOT_USE_OR_WARN_USERS_THEY_CANNOT_UPGRADE`

В будущем мы будем более агрессивно блокировать доступ к внутренним компонентам из React, чтобы не поощрять использование и гарантировать, что пользователи не будут заблокированы от обновления.

## Изменения TypeScript {/*typescript-changes*/}

### Удалены устаревшие типы TypeScript {/*removed-deprecated-typescript-types*/}

Мы очистили типы TypeScript на основе удаленных API в React 19. Некоторые из удаленных типов были перемещены в более релевантные пакеты, а другие больше не нужны для описания поведения React.

<Note>
Мы опубликовали [`types-react-codemod`](https://github.com/eps1lon/types-react-codemod/) для миграции большинства критических изменений, связанных с типами:

```bash
npx types-react-codemod@latest preset-19 ./path-to-app
```

Если у вас много небезопасного доступа к `element.props`, вы можете запустить этот дополнительный codemod:

```bash
npx types-react-codemod@latest react-element-default-any-props ./path-to-your-react-ts-files
```

</Note>

Ознакомьтесь с [`types-react-codemod`](https://github.com/eps1lon/types-react-codemod/) для списка поддерживаемых замен. Если вы считаете, что codemod отсутствует, его можно отслеживать в [списке отсутствующих codemods React 19](https://github.com/eps1lon/types-react-codemod/issues?q=is%3Aissue+is%3Aopen+sort%3Aupdated-desc+label%3A%22React+19%22+label%3Aenhancement).

### Требуется очистка `ref` {/*ref-cleanup-required*/}

_Это изменение включено в пресет codemod `react-19` как [`no-implicit-ref-callback-return
`](https://github.com/eps1lon/types-react-codemod/#no-implicit-ref-callback-return)._

Из-за введения функций очистки ref, возврат чего-либо еще из обратного вызова ref теперь будет отклонен TypeScript. Исправление обычно заключается в прекращении использования неявных возвратов:

```diff [[1, 1, "("], [1, 1, ")"], [2, 2, "{", 15], [2, 2, "}", 1]]
- <div ref={current => (instance = current)} />
+ <div ref={current => {instance = current}} />
```

Исходный код возвращал экземпляр `HTMLDivElement`, и TypeScript не знал бы, должна ли это быть функция очистки или нет.

### `useRef` требует аргумент {/*useref-requires-argument*/}

_Это изменение включено в пресет codemod `react-19` как [`refobject-defaults`](https://github.com/eps1lon/types-react-codemod/#refobject-defaults)._

Давняя жалоба на то, как работают TypeScript и React, была связана с `useRef`. Мы изменили типы, чтобы `useRef` теперь требовал аргумент. Это значительно упрощает его сигнатуру типа. Теперь он будет вести себя больше похоже на `createContext`.

```ts
// @ts-expect-error: Ожидается 1 аргумент, но ни одного не найдено
useRef();
// Переходит
useRef(undefined);
// @ts-expect-error: Ожидается 1 аргумент, но ни одного не найдено
createContext();
// Переходит
createContext(undefined);
```

Теперь это также означает, что все refs являются изменяемыми. Вы больше не столкнетесь с проблемой, когда вы не можете изменить ref, потому что вы инициализировали его с помощью `null`:

```ts
const ref = useRef<number>(null);

// Невозможно присвоить 'current', потому что это свойство только для чтения
ref.current = 1;
```

`MutableRef` теперь устарел в пользу одного типа `RefObject`, который `useRef` всегда будет возвращать:

```ts
interface RefObject<T> {
  current: T
}

declare function useRef<T>: RefObject<T>
```

`useRef` по-прежнему имеет удобную перегрузку для `useRef<T>(null)`, которая автоматически возвращает `RefObject<T | null>`. Чтобы облегчить миграцию из-за требуемого аргумента для `useRef`, была добавлена удобная перегрузка для `useRef(undefined)`, которая автоматически возвращает `RefObject<T | undefined>`.

Ознакомьтесь с [[RFC] Сделать все refs изменяемыми](https://github.com/DefinitelyTyped/DefinitelyTyped/pull/64772) для предыдущих обсуждений об этом изменении.

### Изменения в типе TypeScript `ReactElement` {/*changes-to-the-reactelement-typescript-type*/}

_Это изменение включено в codemod [`react-element-default-any-props`](https://github.com/eps1lon/types-react-codemod#react-element-default-any-props)._

`props` элементов React теперь по умолчанию имеют значение `unknown` вместо `any`, если элемент типизирован как `ReactElement`. Это не повлияет на вас, если вы передаете аргумент типа в `ReactElement`:

```ts
type Example2 = ReactElement<{ id: string }>["props"];
//   ^? { id: string }
```

Но если вы полагались на значение по умолчанию, теперь вам придется обрабатывать `unknown`:

```ts
type Example = ReactElement["props"];
//   ^? Раньше было 'any', теперь 'unknown'
```

Вам это понадобится только в том случае, если у вас много устаревшего кода, полагающегося на небезопасный доступ к пропсам элемента. Интроспекция элемента существует только как способ обхода, и вы должны явно указать, что ваш доступ к пропсам небезопасен, с помощью явного `any`.

### Пространство имен JSX в TypeScript {/*the-jsx-namespace-in-typescript*/}
Это изменение включено в пресет codemod `react-19` как [`scoped-jsx`](https://github.com/eps1lon/types-react-codemod#scoped-jsx)

Давняя просьба — удалить глобальное пространство имен `JSX` из наших типов в пользу `React.JSX`. Это помогает предотвратить загрязнение глобальных типов, что предотвращает конфликты между различными библиотеками пользовательского интерфейса, которые используют JSX.

Теперь вам нужно будет обернуть расширение модуля пространства имен JSX в `declare module "....":

```diff
// global.d.ts
+ declare module "react" {
    namespace JSX {
      interface IntrinsicElements {
        "my-element": {
          myElementProps: string;
        };
      }
    }
+ }
```

Точный спецификатор модуля зависит от среды выполнения JSX, которую вы указали в `compilerOptions` вашего `tsconfig.json