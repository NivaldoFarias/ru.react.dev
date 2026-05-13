---
title: "# Перевод на русский язык сайта reactjs.org


  Текущий прогресс: https://github.com/reactjs/ru.reactjs.org/issues/843


  Пожалуйста, используйте этот ишью, если вы хотите что-то перевести и узнать.


  ## Оглавление


  * [Перевод на русский язык сайта
  reactjs.org](#Перевод-на-русский-язык-сайта-reactjsorg)

  \  * [Оглавление](#Оглавление)

  \  * [Общие правила](#Общие-правила)

  \    * [Слово живое и мёртвое](#Слово-живое-и-мёртвое)

  \  * [Универсальное руководство по стилю](#Универсальное-руководство-по-стилю)

  \    * [Идентификаторы заголовков](#Идентификаторы-заголовков)

  \    * [Текст в блоках кода](#Текст-в-блоках-кода)

  \    * [Внешние ссылки](#Внешние-ссылки)

  \  * [Соглашение по переводу (глоссарий)](#Соглашение-по-переводу-глоссарий)


  ## Общие правила


  - _Вы_ и его производные пишите с маленькой буквы

  - Всегда используйте букву `ё` (это легко соблюдать с помощью [данного
  npm-пакета](https://github.com/hcodes/eyo))

  - Используйте тире вместо дефиса (оно должно быть намного длиннее, чем дефис).
  Тире: `—`, дефис: `-`

  - Переводите комментарии в примерах кода, но не содержимое

  - Используйте терминологию [словаря
  «Веб-стандартов»](https://github.com/web-standards-ru/dictionary), если нет
  исходного варианта в таблице ниже

  - Ссылки на ресурсы (MDN, Wikipedia) должны вести на русскую версию, если есть
  соответствующий перевод.

  - Иностранные имена переводите с указанием оригинального имени в скобках: _Дэн
  Абрамов (Dan Abramov)_. При этом обязательно проверьте уже существующий
  перевод имени в [словаре имён
  «Веб-стандартов»](https://github.com/web-standards-ru/dictionary/blob/master/\
  names.md)

  - Не переводите наименования компаний (Facebook, Airbnb), библиотек (Jest,
  Enzyme) и т.д.

  - Не переводите аббревиатуры (API, DOM, HOC, UI).

  - Исключайте отсылки на пол читателя (см. секцию 3 на [этой
  странице](http://www.un.org/ru/gender-inclusive-language/guidelines.shtml))

  - Названия статей и заголовки пишутся с одной заглавной буквы («Компоненты и
  пропсы», не «Компоненты и Пропсы»).


  ### Слово живое и мёртвое


  Прежде чем переводить, почитайте пару страниц из «Слова живого и мёртвого»
  Норы Галь. Хотя бы эти две:


  * [Берегись канцелярита!](http://www.vavilon.ru/noragal/slovo2.html)

  * [Словесная алгебра](http://www.vavilon.ru/noragal/slovo4.html)


  **По мере возможности, избегайте отглагольных существительных!**


  * **Деревянно:** *для открытия*

  * **Живо:** *чтобы открыть*


  **Также избегайте пассивного залога:**


  * **Деревянно:** *будет рассказано*

  * **Живо:** *мы узнаем*


  **Предпочитайте простые слова (за исключением терминологии ниже):**


  * **Деревянно:** *демонстрирует*

  * **Живо:** *показывает*


  Чтобы избежать кальки, можно брать смысл предложения, а потом перефразировать
  так, как объясняли бы старшему коллеге. Если предложение звучит нелепо вслух,
  то его надо переписать. Например:


  **Оригинал:** *React has been designed from the start for gradual adoption,
  and you can use as little or as much React as you need.*


  **Мёртвая калька:** *React был спроектирован с самого начала для постепенного
  внедрения, и вы можете использовать столько React, сколько вам нужно.*


  **Нормальный перевод:** *Чтобы внедрить React, не надо ничего переписывать.
  Его можно использовать как для маленькой кнопки, так и для целого приложения.*


  Небольшие вольности в переводе допускаются, но только если они помогают
  передать смысл. Перевод не должен быть ни деревянным, ни фамильярным. Истина
  где-то посередине.


  ## Универсальное руководство по стилю


  Описанные ниже правила пришли [из
  репозитория](https://github.com/reactjs/reactjs.org-translation/blob/master/s\
  tyle-guide.md), предписывающего стиль общий для **всех** языков, на которые
  переводится документация.


  ### Идентификаторы заголовков


  Каждый заголовок имеет явный идентификатор:


  ```md

  ## Try React {#try-react}

  ```


  **НЕ** переводите такие идентификаторы! Они используются для навигации.
  Изменение идентификатора неизбежно приведёт к поломке, если на документ
  ссылкаются извне, например так:


  ```md

  Прочтите [главу для начинающих](/getting-started#try-react), чтобы узнать
  больше.

  ```


  ✅ Можно:


  ```md

  ## Попробуйте React {#try-react}

  ```


  ❌ НЕЛЬЗЯ:


  ```md

  ## Попробуйте React {#попробуйте-react}

  ```


  Это сломает ссылку, приведённую в примере выше.


  ### Текст в блоках кода


  Не переводите текст в блоках кода, кроме комментариев. А вот комментарии и
  текст в строках следует переводить, если они не относятся к коду.


  Пример:

  ```js

  // Example

  const element = <h1>Hello, world</h1>;

  ReactDOM.render(element, document.getElementById('root'));

  ```


  ✅ МОЖНО:


  ```js

  // Пример

  const element = <h1>Hello, world</h1>;

  ReactDOM.render(element, document.getElementById('root'));

  ```


  ✅ ТОЖЕ МОЖНО:


  ```js

  // Пример

  const element = <h1>Здравствуй, мир!</h1>;

  ReactDOM.render(element, document.getElementById('root'));

  ```


  ❌ НЕЛЬЗЯ:


  ```js

  // Пример

  const element = <h1>Здравствуй, мир!</h1>;

  // \"root\" ссылается на идентификатор элемента.

  // Не переводите его!

  ReactDOM.render(element, document.getElementById('корень'));

  ```


  ❌ СОВСЕМ НЕЛЬЗЯ:


  ```js

  // Пример

  пост элемент = <з1>Здравствуй, мир!</з1>;

  РеактДОМ.отрендери(элемент, документ.получиЭлементПоАйди('корень'));

  ```


  ### Внешние ссылки


  Если внешняя ссылка ведёт к статье на [MDN] или [Wikipedia], и там существует
  статья на русском языке, исправьте ссылку, чтобы она указывала на русский
  вариант, даже если он неполный или требует улучшения.


  [MDN]: https://developer.mozilla.org/en-US/

  [Wikipedia]: https://en.wikipedia.org/wiki/Main_Page


  Пример:


  ```md

  React elements are
  [immutable](https://en.wikipedia.org/wiki/Immutable_object).

  ```


  ✅ OK:


  ```md

  Элементы React
  [иммутабельны](https://ru.wikipedia.org/wiki/%D0%9D%D0%B5%D0%B8%D0%B7%D0%BC%D\
  0%B5%D0%BD%D1%8F%D0%B5%D0%BC%D1%8B%D0%B9_%D0%BE%D0%B1%D1%8A%D0%B5%D0%BA%D1%82\
  ).

  ```


  Для ссылок, у которых нету языковых эквивалентов (Stack Overflow, видео на
  YouTube, и т. д.), просто оставьте оригинальный вариант как есть.


  ## Соглашение по переводу (глоссарий)


  **Пожалуйста, поддерживайте глоссарий в алфавитном порядке.**


  | Оригинальный термин | Перевод |

  | ------------------ | ---------- |

  | API reference | справочник API |

  | array | массив |

  | arrow function | стрелочная функция |

  | attribute | атрибут |

  | batch | группа обновлений |

  | batching | группировка |

  | bind | привязка |

  | bug | баг, дефект |

  | bundler | бандлер |

  | cache | кеш |

  | callback | колбэк |

  | camelCase | *camelCase* |

  | child | дочерний |

  | child component | дочерний компонент |

  | class component | классовый компонент |

  | cleanup | сброс (*напр., сбрасываемый эффект, сбросить эффект, сбрасывающая
  функция*) |

  | code splitting | разделение кода |

  | codemod | codemod-скрипт |

  | commit | фиксация |

  | concurrent mode | конкурентный режим |

  | context | контекст |

  | (un)controlled component | (не)управляемый компонент |

  | cross-cutting concerns | cквозная функциональность |

  | custom | пользовательский (*напр., пользовательские хуки*) |

  | debugging | отладка |

  | destructuring | деструктуризация |

  | development | разработка |

  | development mode | режим разработки |

  | developer tools | инструменты разработки |

  | display name | отображаемое имя |

  | DOM container | DOM-контейнер |

  | effect | эффект |

  | error | ошибка |

  | error boundary | предохранитель |

  | escape hatch | лазейка (*напр., императивные лазейки*) |

  | fallback | запасной |

  | feature | возможность (*менее предпочтительный синоним: функциональность*) |

  | folder | папка |

  | framework | фреймворк |

  | function component | функциональный компонент |

  | helper | вспомогательный(ая); *напр., helper function — вспомогательная
  функция* |

  | Higher-Order Component(s) | Компонент(ы) высшего порядка |

  | hook | хук *(с маленькой буквы)* *(напр., хук эффекта, хук контекста)* |

  | incapsulation | инкапсуляция |

  | incapsulated | инкапсулированный |

  | key | ключ |

  | lazy initialization | ленивая инициализация |

  | library | библиотека |

  | lifecycle | жизненный цикл |

  | lifecycle method | метод жизненного цикла |

  | lifting state up | подъём состояния, поднимать состояние |

  | local state | внутреннее состояние |

  | lowercase | нижний регистр |

  | mixin | примесь |

  | mock | фиктивный |

  | (im)mutable | (им)мутабельный |

  | (im)mutability | (им)мутабельность |

  | (un)mount | (раз)монтирование |

  | mutate | мутировать |

  | native | нативный |

  | Note | Примечание |

  | online | онлайн |

  | online playground | онлайн-песочница |

  | paint | отрисовка, перерисовка |

  | persistent | персистентность, персистентный |

  | phase | этап (*напр., этап рендеринга, этап фиксации*) |

  | prop | проп *(ед. ч.)* |

  | props | пропсы *(мн. ч.)* |

  | production | продакшен |

  | production mode | продакшен-режим |

  | profiling | профилирование |

  | Profiler | Profiler *(если имеется в виду компонент Profiler)*;
  профилировщик *(если имеется в виду инструмент для профилирования)*

  | React | React |

  | React element | React-элемент, элемент React |

  | reconciliation | согласование |

  | reducer | редюсер |

  | ref | реф *(ед. ч.)* |

  | refs | рефы *(мн. ч.)* |

  | ref forwarding | перенаправление рефа |

  | render(ing) | рендер, рендерить, отрендерить |

  | renderer | рендерер (*напр., поверхностный рендерер*) |

  | re-render(ing) | (по контексту) рендер, повторный рендер, последующий
  рендер, отрендерить снова |

  | render prop | рендер-проп *(ед. ч.)* |

  | render props | рендер-пропсы *(мн. ч.)* |

  | reuse | повторное использование |

  | (previous/next) section | (предыдущая/следующая) глава *(только в разделе
  Основные понятия)* |

  | Set of interactions | Множество «взаимодействий»

  | shallow | поверхностное (сравнение, равенство), поверхностный (рендеринг) |

  | side effect | побочный эффект |

  | snapshot | снимок |

  | state | состояние |

  | stateful component | компонент с состоянием |

  | stateless component | компонент без состояния |

  | suspense | задержка |

  | tag | тег |

  | template literals | шаблонные строки |

  | Tip | Совет |

  | TLDR | Вкратце |

  | Try it on CodePen | Посмотреть на CodePen |

  | tutorial | введение |

  | unidirectional data flow | однонаправленный поток данных |

  | UI | UI |

  | update | обновление |

  | user interface | интерфейс (добавляйте «пользовательский» только если по
  контексту непонятно) |

  | Warning | Предупреждение |"
author: Ricky Hanlon
date: 2024/04/25
description: Улучшения, добавленные в React 19, требуют некоторых критических
  изменений, но мы постарались сделать обновление максимально плавным, и мы не
  ожидаем, что эти изменения повлияют на большинство приложений. В этой статье
  мы расскажем вам, как обновить приложения и библиотеки до React 19.
---
```
April 25, 2024 от [Рики Хэнлон](https://twitter.com/rickhanlonii)

---

<Intro>

Улучшения, добавленные в React 19, требуют некоторых критических изменений, но мы постарались сделать обновление максимально плавным, и мы не ожидаем, что эти изменения повлияют на большинство приложений.

</Intro>

<Note>

#### Также был опубликован React 18.3 {/*react-18-3*/}

Чтобы упростить обновление до React 19, мы опубликовали релиз `react@18.3`, который идентичен 18.2, но добавляет предупреждения для устаревших API и других изменений, необходимых для React 19.

Мы рекомендуем сначала обновиться до React 18.3, чтобы выявить какие-либо проблемы, прежде чем обновляться до React 19.

Список изменений в 18.3 см. в [Заметках о выпуске](https://github.com/facebook/react/blob/main/CHANGELOG.md#1830-april-25-2024).

</Note>

В этой статье мы расскажем вам о шагах по обновлению до React 19:

- [Установка](#installing)
- [Codemods](#codemods)
- [Критические изменения](#breaking-changes)
- [Новые устаревшие функции](#new-deprecations)
- [Заметные изменения](#notable-changes)
- [Изменения TypeScript](#typescript-changes)
- [Журнал изменений](#changelog)

Если вы хотите помочь нам протестировать React 19, выполните шаги, описанные в этом руководстве по обновлению, и [сообщите о любых возникших проблемах](https://github.com/facebook/react/issues/new?assignees=&labels=React+19&projects=&template=19.md&title=%5BReact+19%5D). Список новых функций, добавленных в React 19, см. в [статье о выпуске React 19](/blog/2024/12/05/react-19).

---
## Установка {/*installing*/}

<Note>

#### Теперь требуется новый JSX-трансформатор {/*new-jsx-transform-is-now-required*/}

Мы представили [новый JSX-трансформатор](https://legacy.reactjs.org/blog/2020/09/22/introducing-the-new-jsx-transform.html) в 2020 году, чтобы улучшить размер бандла и использовать JSX без импорта React. В React 19 мы добавляем дополнительные улучшения, такие как использование ref в качестве пропа и улучшения скорости JSX, которые требуют нового трансформатора.

Если новый трансформатор не включен, вы увидите это предупреждение:

<ConsoleBlockMulti>

<ConsoleLogLine level="error">

Ваше приложение (или одна из его зависимостей) использует устаревший JSX-трансформатор. Обновитесь до современного JSX-трансформатора для повышения производительности: https://react.dev/link/new-jsx-transform

</ConsoleLogLine>

</ConsoleBlockMulti>

Мы ожидаем, что большинство приложений не будут затронуты, так как трансформатор уже включен в большинстве сред. Инструкции по ручному обновлению см. в [анонсе](https://legacy.reactjs.org/blog/2020/09/22/introducing-the-new-jsx-transform.html).

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
```

```markdown
## РАЗРУШАЮЩИЕ ИЗМЕНЕНИЯ {/*breaking-changes*/}

### Ошибки в render больше не перебрасываются {/*errors-in-render-are-not-re-thrown*/}

В предыдущих версиях React ошибки, возникающие во время рендеринга, перехватывались и перебрасывались повторно. В DEV мы также записывали в `console.error`, что приводило к дублированию журналов ошибок.

В React 19 мы [улучшили обработку ошибок](/blog/2024/04/25/react-19#error-handling), чтобы уменьшить дублирование, не перебрасывая их повторно:

- **Неперехваченные ошибки**: Ошибки, которые не перехватываются Error Boundary, сообщаются в `window.reportError`.
- **Перехваченные ошибки**: Ошибки, которые перехватываются Error Boundary, сообщаются в `console.error`.

Это изменение не должно повлиять на большинство приложений, но если ваш отчет об ошибках в продакшене зависит от повторного перебрасывания ошибок, вам может потребоваться обновить обработку ошибок. Для поддержки этого мы добавили новые методы в `createRoot` и `hydrateRoot` для пользовательской обработки ошибок:

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

---

### Удалены устаревшие API React {/*removed-deprecated-react-apis*/}

#### Удалено: `propTypes` и `defaultProps` для функций {/*removed-proptypes-and-defaultprops*/}
`PropTypes` были объявлены устаревшими в [апреле 2017 года (v15.5.0)](https://legacy.reactjs.org/blog/2017/04/07/react-v15.5.0.html#new-deprecation-warnings).

В React 19 мы удаляем проверки `propType` из пакета React, и их использование будет молча игнорироваться. Если вы используете `propTypes`, мы рекомендуем перейти на TypeScript или другое решение для проверки типов.

Мы также удаляем `defaultProps` из функциональных компонентов вместо параметров по умолчанию ES6. Компоненты классов будут продолжать поддерживать `defaultProps`, так как альтернативы ES6 нет.

```js
// До
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
// После
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

Legacy Context был объявлен устаревшим в [октябре 2018 года (v16.6.0)](https://legacy.reactjs.org/blog/2018/10/23/react-v-16-6.html).

Legacy Context был доступен только в компонентах классов с использованием API `contextTypes` и `getChildContext` и был заменен на `contextType` из-за тонких ошибок, которые было легко пропустить. В React 19 мы удаляем Legacy Context, чтобы сделать React немного меньше и быстрее.

Если вы все еще используете Legacy Context в компонентах классов, вам нужно будет перейти на новый API `contextType`:

```js {5-11,19-21}
// До
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
// После
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
String refs были объявлены устаревшими в [марте 2018 года (v16.3.0)](https://legacy.reactjs.org/blog/2018/03/27/update-on-async-rendering.html).

Компоненты классов поддерживали string refs до того, как были заменены обратными вызовами ref из-за [нескольких недостатков](https://github.com/facebook/react/issues/1373). В React 19 мы удаляем string refs, чтобы сделать React проще и понятнее.

Если вы все еще используете string refs в компонентах классов, вам нужно будет перейти на обратные вызовы ref:

```js {4,8}
// До
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
// После
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

#### Удалено: фабрики шаблонов модулей {/*removed-module-pattern-factories*/}
Фабрики шаблонов модулей были объявлены устаревшими в [августе 2019 года (v16.9.0)](https://legacy.reactjs.org/blog/2019/08/08/react-v16.9.0.html#deprecating-module-pattern-factories).

Этот шаблон редко использовался, и его поддержка приводит к тому, что React становится немного больше и медленнее, чем необходимо. В React 19 мы удаляем поддержку фабрик шаблонов модулей, и вам нужно будет перейти на обычные функции:

```js
// До
function FactoryComponent() {
  return { render() { return <div />; } }
}
```

```js
// После
function FactoryComponent() {
  return <div />;
}
```

#### Удалено: `React.createFactory` {/*removed-createfactory*/}
`createFactory` был объявлен устаревшим в [феврале 2020 года (v16.13.0)](https://legacy.reactjs.org/blog/2020/02/26/react-v16.13.0.html#deprecating-createfactory).

Использование `createFactory` было распространено до широкой поддержки JSX, но сегодня оно редко используется и может быть заменено JSX. В React 19 мы удаляем `createFactory`, и вам нужно будет перейти на JSX:

```js
// До
import { createFactory } from 'react';

const button = createFactory('button');
```

```js
// После
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

Поверхностный рендеринг зависит от внутренних компонентов React и может помешать вам в будущих обновлениях. Мы рекомендуем перенести ваши тесты на [@testing-library/react](https://testing-library.com/docs/react-testing-library/intro/) или [@testing-library/react-native](https://testing-library.com/docs/react-native-testing-library/intro).

</Note>

### Удалены устаревшие API React DOM {/*removed-deprecated-react-dom-apis*/}

#### Удалено: `react-dom/test-utils` {/*removed-react-dom-test-utils*/}

Мы переместили `act` из `react-dom/test-utils` в пакет `react`:

<ConsoleBlockMulti>

<ConsoleLogLine level="error">

`ReactDOMTestUtils.act` устарел в пользу `React.act`. Импортируйте `act` из `react` вместо `react-dom/test-utils`. См. https://react.dev/warnings/react-dom-test-utils для получения дополнительной информации.

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

`ReactDOM.render` был объявлен устаревшим в [марте 2022 года (v18.0.0)](https://react.dev/blog/2022/03/08/react-18-upgrade-guide). В React 19 мы удаляем `ReactDOM.render`, и вам нужно будет перейти на использование [`ReactDOM.createRoot`](https://react.dev/reference/react-dom/client/createRoot):

```js
// До
import {render} from 'react-dom';
render(<App />, document.getElementById('root'));

// После
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

`ReactDOM.hydrate` был объявлен устаревшим в [марте 2022 года (v18.0.0)](https://react.dev/blog/2022/03/08/react-18-upgrade-guide). В React 19 мы удаляем `ReactDOM.hydrate`, и вам нужно будет перейти на использование [`ReactDOM.hydrateRoot`](https://react.dev/reference/react-dom/client/hydrateRoot),

```js
// До
import {hydrate} from 'react-dom';
hydrate(<App />, document.getElementById('root'));

// После
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

`ReactDOM.unmountComponentAtNode` был объявлен устаревшим в [марте 2022 года (v18.0.0)](https://react.dev/blog/2022/03/08/react-18-upgrade-guide). В React 19 вам нужно будет перейти на использование `root.unmount()`.

```js
// До
unmountComponentAtNode(document.getElementById('root'));

// После
root.unmount();
```

Для получения дополнительной информации см. `root.unmount()` для [`createRoot`](https://react.dev/reference/react-dom/client/createRoot#root-unmount) и [`hydrateRoot`](https://react.dev/reference/react-dom/client/hydrateRoot#root-unmount).

<Note>

Codemod `unmountComponentAtNode` в `root.unmount`:

```bash
npx codemod@latest react/19/replace-reactdom-render
```

</Note>

#### Удалено: `ReactDOM.findDOMNode` {/*removed-reactdom-finddomnode*/}

`ReactDOM.findDOMNode` был [объявлен устаревшим в октябре 2018 года (v16.6.0)](https://legacy.reactjs.org/blog/2018/10/23/react-v-16-6.html#deprecations-in-strictmode).

Мы удаляем `findDOMNode`, потому что это был устаревший способ обхода, который был медленным в исполнении, хрупким для рефакторинга, возвращал только первого дочернего элемента и нарушал уровни абстракции (подробнее [здесь](https://legacy.reactjs.org/docs/strict-mode.html#warning-about-deprecated-finddomnode-usage)). Вы можете заменить `ReactDOM.findDOMNode` на [DOM refs](/learn/manipulating-the-dom-with-refs):

```js
// До
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
// После
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

React 19 поддерживает [`ref` как проп](/blog/2024/04/25/react-19#ref-as-a-prop), поэтому мы объявляем устаревшим `element.ref` вместо `element.props.ref`.

Доступ к `element.ref` выдаст предупреждение:

<ConsoleBlockMulti>

<ConsoleLogLine level="error">

Доступ к element.ref больше не поддерживается. ref теперь является обычным пропом. Он будет удален из типа JSX Element в будущей версии.

</ConsoleLogLine>

</ConsoleBlockMulti>

### Устарело: `react-test-renderer` {/*deprecated-react-test-renderer*/}

Мы объявляем устаревшим `react-test-renderer`, потому что он реализует собственную среду рендерера, которая не соответствует среде, используемой пользователями, способствует тестированию деталей реализации и полагается на интроспекцию внутренних компонентов React.

Тестовый рендерер был создан до того, как появились более жизнеспособные стратегии тестирования, такие как [React Testing Library](https://testing-library.com), и теперь мы рекомендуем использовать современную библиотеку тестирования.

В React 19 `react-test-renderer` регистрирует предупреждение об устаревании и переключился на одновременный рендеринг. Мы рекомендуем перенести ваши тесты на [@testing-library/react](https://testing-library.com/docs/react-testing-library/intro/) или [@testing-library/react-native](https://testing-library.com/docs/react-native-testing-library/intro) для современного и хорошо поддерживаемого опыта тестирования.
```

## Заметные изменения {/*notable-changes*/}

### Изменения в StrictMode {/*strict-mode-improvements*/}

React 19 включает в себя несколько исправлений и улучшений для Strict Mode.

При двойном рендеринге в Strict Mode в режиме разработки `useMemo` и `useCallback` будут повторно использовать мемоизированные результаты с первого рендеринга во время второго рендеринга. Компоненты, которые уже совместимы со Strict Mode, не должны заметить разницы в поведении.

Как и во всех режимах Strict Mode, эти функции предназначены для упреждающего выявления ошибок в ваших компонентах во время разработки, чтобы вы могли исправить их до отправки в продакшен. Например, во время разработки Strict Mode будет дважды вызывать функции обратного вызова ref при первоначальном монтировании, чтобы смоделировать то, что происходит, когда смонтированный компонент заменяется резервным вариантом Suspense.

### Улучшения Suspense {/*improvements-to-suspense*/}

В React 19, когда компонент приостанавливается, React немедленно зафиксирует резервный вариант ближайшей границы Suspense, не дожидаясь рендеринга всего дерева соседних элементов. После фиксации резервного варианта React планирует другой рендеринг для приостановленных соседних элементов, чтобы «предварительно разогреть» ленивые запросы в остальной части дерева:

<Diagram name="prerender" height={162} width={1270} alt="Диаграмма, показывающая дерево из трех компонентов, один родительский компонент с меткой Accordion и два дочерних компонента с меткой Panel. Оба компонента Panel содержат isActive со значением false.">

Раньше, когда компонент приостанавливался, приостановленные соседние элементы рендерились, а затем фиксировался резервный вариант.

</Diagram>

<Diagram name="prewarm" height={162} width={1270} alt="Та же диаграмма, что и предыдущая, с выделенным значением isActive первого дочернего компонента Panel, указывающим на щелчок со значением isActive, установленным в true. Второй компонент Panel по-прежнему содержит значение false." >

В React 19, когда компонент приостанавливается, фиксируется резервный вариант, а затем рендерятся приостановленные соседние элементы.

</Diagram>

Это изменение означает, что резервные варианты Suspense отображаются быстрее, при этом по-прежнему разогревая ленивые запросы в приостановленном дереве.

### Сборки UMD удалены {/*umd-builds-removed*/}

UMD широко использовался в прошлом как удобный способ загрузки React без этапа сборки. Сейчас существуют современные альтернативы для загрузки модулей в виде скриптов в HTML-документах. Начиная с React 19, React больше не будет создавать сборки UMD, чтобы уменьшить сложность процесса тестирования и выпуска.

Чтобы загрузить React 19 с помощью тега script, мы рекомендуем использовать CDN на основе ESM, такой как [esm.sh](https://esm.sh/).

```html
<script type="module">
  import React from "https://esm.sh/react@19/?dev"
  import ReactDOMClient from "https://esm.sh/react-dom@19/client?dev"
  ...
</script>
```

### Библиотеки, зависящие от внутренних компонентов React, могут блокировать обновления {/*libraries-depending-on-react-internals-may-block-upgrades*/}

Этот выпуск включает изменения во внутренние компоненты React, которые могут повлиять на библиотеки, игнорирующие наши просьбы не использовать внутренние компоненты, такие как `SECRET_INTERNALS_DO_NOT_USE_OR_YOU_WILL_BE_FIRED`. Эти изменения необходимы для реализации улучшений в React 19 и не сломают библиотеки, которые следуют нашим рекомендациям.

Основываясь на нашей [Политике версионирования](https://react.dev/community/versioning-policy#what-counts-as-a-breaking-change), эти обновления не указаны как критические изменения, и мы не включаем документацию о том, как их обновить. Рекомендуется удалить любой код, который зависит от внутренних компонентов.

Чтобы отразить влияние использования внутренних компонентов, мы переименовали суффикс `SECRET_INTERNALS` в:

`_DO_NOT_USE_OR_WARN_USERS_THEY_CANNOT_UPGRADE`

В будущем мы будем более агрессивно блокировать доступ к внутренним компонентам из React, чтобы не поощрять их использование и гарантировать, что пользователи не будут заблокированы от обновления.

## Изменения в TypeScript {/*typescript-changes*/}

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

_Это изменение включено в предустановку codemod `react-19` как [`no-implicit-ref-callback-return
`](https://github.com/eps1lon/types-react-codemod/#no-implicit-ref-callback-return)._

Из-за введения функций очистки ref, возврат чего-либо другого из обратного вызова ref теперь будет отклонен TypeScript. Исправление обычно заключается в прекращении использования неявных возвратов:

```diff [[1, 1, "("], [1, 1, ")"], [2, 2, "{", 15], [2, 2, "}", 1]]
- <div ref={current => (instance = current)} />
+ <div ref={current => {instance = current}} />
```

Исходный код возвращал экземпляр `HTMLDivElement`, и TypeScript не знал бы, должна ли это быть функция очистки или нет.

### `useRef` требует аргумент {/*useref-requires-argument*/}

_Это изменение включено в предустановку codemod `react-19` как [`refobject-defaults`](https://github.com/eps1lon/types-react-codemod/#refobject-defaults)._

Давняя жалоба на то, как работают TypeScript и React, касалась `useRef`. Мы изменили типы, чтобы `useRef` теперь требовал аргумент. Это значительно упрощает его сигнатуру типа. Теперь он будет вести себя больше похоже на `createContext`.

```ts
// @ts-expect-error: Ожидается 1 аргумент, но не найдено ни одного
useRef();
// Проходит
useRef(undefined);
// @ts-expect-error: Ожидается 1 аргумент, но не найдено ни одного
createContext();
// Проходит
createContext(undefined);
```

Теперь это также означает, что все рефы изменяемы. Вы больше не столкнетесь с проблемой, когда вы не можете изменить ref, потому что вы инициализировали его с помощью `null`:

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

Ознакомьтесь с [[RFC] Make all refs mutable](https://github.com/DefinitelyTyped/DefinitelyTyped/pull/64772) для предыдущих обсуждений об этом изменении.

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

Вам это понадобится только в том случае, если у вас много устаревшего кода, полагающегося на небезопасный доступ к пропсам элемента. Интроспекция элемента существует только как лазейка, и вы должны явно указать, что ваш доступ к пропсам небезопасен, с помощью явного `any`.

### Пространство имен JSX в TypeScript {/*the-jsx-namespace-in-typescript*/}

Это изменение включено в предустановку codemod `react-19` как [`scoped-jsx`](https://github.com/eps1lon/types-react-codemod#scoped-jsx)

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

Точный спецификатор модуля зависит от среды выполнения JSX, которую вы указали в `compilerOptions` вашего `tsconfig.json`:

- Для `"jsx": "react-jsx"` это будет `react/jsx-runtime`.
- Для `"jsx": "react-jsxdev"` это будет `react/jsx-dev-runtime`.
- Для `"jsx": "react"` и `"jsx": "preserve"` это будет `react`.

### Улучшенные типы `useReducer` {/*better-usereducer-typings*/}

`useReducer` теперь имеет улучшенное выведение типов благодаря [@mfp22](https://github.com/mfp22).

Однако это потребовало критического изменения, при котором `useReducer` не принимает полный тип редуктора в качестве параметра типа, а вместо этого требует либо ни одного (и полагается на контекстную типизацию), либо требует как тип состояния, так и тип действия.

Новая лучшая практика — _не_ передавать аргументы типа в `useReducer`.
```diff
- useReducer<React.Reducer<State, Action>>(reducer)
+ useReducer(reducer)
```
Это может не работать в крайних случаях, когда вы можете явно указать тип состояния и действия, передав `Action` в кортеже:
```diff
- useReducer<React.Reducer<State, Action>>(reducer)
+ useReducer<State, [Action]>(reducer)
```
Если вы определяете редуктор встраиваемым способом, мы рекомендуем аннотировать параметры функции:
```diff
- useReducer<React.Reducer<State, Action>>((state, action) => state)
+ useReducer((state: State, action: Action) => state)
```
Это также то, что вам придется сделать, если вы переместите редуктор за пределы вызова `useReducer`:

```ts
const reducer = (state: State, action: Action) => state;
```

## Журнал изменений {/*changelog*/}

### Другие критические изменения {/*other-breaking-changes*/}

- **react-dom**: Ошибка для URL-адресов JavaScript в `src` и `href` [#26507](https://github.com/facebook/react/pull/26507)
- **react-dom**: Удалить `errorInfo.digest` из `onRecoverableError` [#28222](https://github.com/facebook/react/pull/28222)
- **react-dom**: Удалить `unstable_flushControlled` [#26397](https://github.com/facebook/react/pull/26397)
- **react-dom**: Удалить `unstable_createEventHandle` [#28271](https://github.com/facebook/react/pull/28271)
- **react-dom**: Удалить `unstable_renderSubtreeIntoContainer` [#28271](https://github.com/facebook/react/pull/28271)
- **react-dom**: Удалить `unstable_runWithPriority` [#28271](https://github.com/facebook/react/pull/28271)
- **react-is**: Удалить устаревшие методы из `react-is` [28224](https://github.com/facebook/react/pull/28224)

### Другие заметные изменения {/*other-notable-changes*/}

- **react**: Пакетные синхронные, значения по умолчанию и непрерывные дорожки [#25700](https://github.com/facebook/react/pull/25700)
- **react**: Не выполнять предварительный рендеринг соседних элементов приостановленного компонента [#26380](https://github.com/facebook/react/pull/26380)
- **react**: Обнаружение бесконечных циклов обновления, вызванных обновлениями фазы рендеринга [#26625](https://github.com/facebook/react/pull/26625)
- **react-dom**: Переходы в popstate теперь синхронны [#26025](https://github.com/facebook/react/pull/26025)
- **react-dom**: Удалить предупреждение об эффекте макета во время SSR [#26395](https://github.com/facebook/react/pull/26395)
- **react-dom**: Предупреждать и не устанавливать пустую строку для src/href (за исключением тегов привязки) [#28124](https://github.com/facebook/react/pull/28124)

Полный список изменений см. в [Журнале изменений](https://github.com/facebook/react/blob/main/CHANGELOG.md#1900-december-5-2024).

---

Благодарим [Andrew Clark](https://twitter.com/acdlite), [Eli White](https://twitter.com/Eli_White), [Jack Pope](https://github.com/jackpope), [Jan Kassens](https://github.com/kassens), [Josh Story](https://twitter.com/joshcstory), [Matt Carroll](https://twitter.com/mattcarrollcode), [Noah Lemen](https://twitter.com/noahlemen), [Sophie Alpert](https://twitter.com/sophiebits) и [Sebastian Silbermann](https://twitter.com/sebsilbermann) за рецензирование и редактирование этого поста.