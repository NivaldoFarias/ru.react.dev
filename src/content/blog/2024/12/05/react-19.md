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
author: The React Team
date: 2024/12/05
description: React 19 теперь доступен в npm! В этой статье мы рассмотрим новые
  возможности React 19 и способы их использования.
---
```
## Перевод на русский язык сайта reactjs.org

5 декабря 2024 г. от [Команды React](/community/team)

---
<Note>

### React 19 теперь стабилен! {/*react-19-is-now-stable*/}

Дополнения с момента первоначальной публикации этого поста с React 19 RC в апреле:

- **Предварительный прогрев для приостановленных деревьев**: см. [Улучшения Suspense](/blog/2024/04/25/react-19-upgrade-guide#improvements-to-suspense).
- **Статические API React DOM**: см. [Новые статические API React DOM](#new-react-dom-static-apis).

_Дата этого поста была обновлена, чтобы отражать дату стабильного релиза._

</Note>

<Intro>

React v19 теперь доступен в npm!

</Intro>

В нашем [Руководстве по обновлению React 19](/blog/2024/04/25/react-19-upgrade-guide) мы поделились пошаговыми инструкциями по обновлению вашего приложения до React 19. В этом посте мы предоставим обзор новых возможностей React 19 и способов их внедрения.

- [Что нового в React 19](#whats-new-in-react-19)
- [Улучшения в React 19](#improvements-in-react-19)
- [Как выполнить обновление](#how-to-upgrade)

Список критических изменений см. в [Руководстве по обновлению](/blog/2024/04/25/react-19-upgrade-guide).

---

## Что нового в React 19 {/*whats-new-in-react-19*/}

### Действия {/*actions*/}

Распространенным вариантом использования в приложениях React является выполнение мутации данных, а затем обновление состояния в ответ. Например, когда пользователь отправляет форму для изменения своего имени, вы сделаете запрос к API, а затем обработаете ответ. Раньше вам нужно было вручную обрабатывать состояния ожидания, ошибки, оптимистичные обновления и последовательные запросы.

Например, вы можете обработать состояние ожидания и ошибки в `useState`:

```js
// До действий
function UpdateName({}) {
  const [name, setName] = useState("");
  const [error, setError] = useState(null);
  const [isPending, setIsPending] = useState(false);

  const handleSubmit = async () => {
    setIsPending(true);
    const error = await updateName(name);
    setIsPending(false);
    if (error) {
      setError(error);
      return;
    } 
    redirect("/path");
  };

  return (
    <div>
      <input value={name} onChange={(event) => setName(event.target.value)} />
      <button onClick={handleSubmit} disabled={isPending}>
        Update
      </button>
      {error && <p>{error}</p>}
    </div>
  );
}
```

В React 19 мы добавляем поддержку использования асинхронных функций в переходах для автоматической обработки состояний ожидания, ошибок, форм и оптимистичных обновлений.

Например, вы можете использовать `useTransition` для обработки состояния ожидания:

```js
// Использование состояния ожидания из действий
function UpdateName({}) {
  const [name, setName] = useState("");
  const [error, setError] = useState(null);
  const [isPending, startTransition] = useTransition();

  const handleSubmit = () => {
    startTransition(async () => {
      const error = await updateName(name);
      if (error) {
        setError(error);
        return;
      } 
      redirect("/path");
    })
  };

  return (
    <div>
      <input value={name} onChange={(event) => setName(event.target.value)} />
      <button onClick={handleSubmit} disabled={isPending}>
        Update
      </button>
      {error && <p>{error}</p>}
    </div>
  );
}
```

Асинхронный переход немедленно установит состояние `isPending` в true, выполнит асинхронные запросы и переключит `isPending` в false после любых переходов. Это позволяет вам поддерживать отзывчивость и интерактивность текущего пользовательского интерфейса во время изменения данных.

<Note>

#### По соглашению функции, использующие асинхронные переходы, называются «Действиями». {/*by-convention-functions-that-use-async-transitions-are-called-actions*/}

Действия автоматически управляют отправкой данных за вас:

- **Состояние ожидания**: Действия предоставляют состояние ожидания, которое начинается в начале запроса и автоматически сбрасывается при фиксации окончательного обновления состояния.
- **Оптимистичные обновления**: Действия поддерживают новый хук [`useOptimistic`](#new-hook-optimistic-updates), чтобы вы могли показывать пользователям мгновенную обратную связь во время отправки запросов.
- **Обработка ошибок**: Действия обеспечивают обработку ошибок, чтобы вы могли отображать границы ошибок при сбое запроса и автоматически возвращать оптимистичные обновления к их исходному значению.
- **Формы**: Элементы `<form>` теперь поддерживают передачу функций в свойства `action` и `formAction`. Передача функций в свойства `action` по умолчанию использует Действия и автоматически сбрасывает форму после отправки.

</Note>

Основываясь на Действиях, React 19 представляет [`useOptimistic`](#new-hook-optimistic-updates) для управления оптимистичными обновлениями и новый хук [`React.useActionState`](#new-hook-useactionstate) для обработки общих случаев для Действий. В `react-dom` мы добавляем [`<form>` Действия](#form-actions) для автоматического управления формами и [`useFormStatus`](#new-hook-useformstatus) для поддержки общих случаев для Действий в формах.

В React 19 приведенный выше пример можно упростить до:

```js
// Использование <form> Действий и useActionState
function ChangeName({ name, setName }) {
  const [error, submitAction, isPending] = useActionState(
    async (previousState, formData) => {
      const error = await updateName(formData.get("name"));
      if (error) {
        return error;
      }
      redirect("/path");
      return null;
    },
    null,
  );

  return (
    <form action={submitAction}>
      <input type="text" name="name" />
      <button type="submit" disabled={isPending}>Update</button>
      {error && <p>{error}</p>}
    </form>
  );
}
```

В следующем разделе мы разберем каждую из новых функций Действий в React 19.

### Новый хук: `useActionState` {/*new-hook-useactionstate*/}

Чтобы упростить общие случаи для Действий, мы добавили новый хук под названием `useActionState`:

```js
const [error, submitAction, isPending] = useActionState(
  async (previousState, newName) => {
    const error = await updateName(newName);
    if (error) {
      // Вы можете вернуть любой результат действия.
      // Здесь мы возвращаем только ошибку.
      return error;
    }

    // обработка успеха
    return null;
  },
  null,
);
```

`useActionState` принимает функцию ( «Действие») и возвращает обернутое Действие для вызова. Это работает, потому что Действия компонуются. Когда вызывается обернутое Действие, `useActionState` вернет последний результат Действия как `data` и состояние ожидания Действия как `pending`.

<Note>

`React.useActionState` ранее назывался `ReactDOM.useFormState` в выпусках Canary, но мы переименовали его и сделали `useFormState` устаревшим.

См. [#28491](https://github.com/facebook/react/pull/28491) для получения дополнительной информации.

</Note>

Для получения дополнительной информации см. документацию по [`useActionState`](/reference/react/useActionState).

### React DOM: `<form>` Действия {/*form-actions*/}

Действия также интегрированы с новыми функциями `<form>` React 19 для `react-dom`. Мы добавили поддержку передачи функций в качестве свойств `action` и `formAction` элементов `<form>`, `<input>` и `<button>`, чтобы автоматически отправлять формы с Действиями:

```js [[1,1,"actionFunction"]]
<form action={actionFunction}>
```

Когда `<form>` Действие завершается успешно, React автоматически сбрасывает форму для неуправляемых компонентов. Если вам нужно сбросить `<form>` вручную, вы можете вызвать новый API React DOM `requestFormReset`.

Для получения дополнительной информации см. документацию `react-dom` по [`<form>`](/reference/react-dom/components/form), [`<input>`](/reference/react-dom/components/input) и `<button>`.

### React DOM: Новый хук: `useFormStatus` {/*new-hook-useformstatus*/}

В системах проектирования обычно пишут компоненты проектирования, которым нужен доступ к информации о `<form>`, в которой они находятся, без передачи свойств вниз к компоненту. Это можно сделать через Context, но чтобы упростить общий случай, мы добавили новый хук `useFormStatus`:

```js [[1, 4, "pending"], [1, 5, "pending"]]
import {useFormStatus} from 'react-dom';

function DesignButton() {
  const {pending} = useFormStatus();
  return <button type="submit" disabled={pending} />
}
```

`useFormStatus` считывает состояние родительского `<form>`, как если бы форма была поставщиком Context.

Для получения дополнительной информации см. документацию `react-dom` по [`useFormStatus`](/reference/react-dom/hooks/useFormStatus).

### Новый хук: `useOptimistic` {/*new-hook-optimistic-updates*/}

Еще одним распространенным шаблоном пользовательского интерфейса при выполнении мутации данных является оптимистичное отображение окончательного состояния, пока выполняется асинхронный запрос. В React 19 мы добавляем новый хук под названием `useOptimistic`, чтобы упростить это:

```js {2,6,13,19}
function ChangeName({currentName, onUpdateName}) {
  const [optimisticName, setOptimisticName] = useOptimistic(currentName);

  const submitAction = async formData => {
    const newName = formData.get("name");
    setOptimisticName(newName);
    const updatedName = await updateName(newName);
    onUpdateName(updatedName);
  };

  return (
    <form action={submitAction}>
      <p>Your name is: {optimisticName}</p>
      <p>
        <label>Change Name:</label>
        <input
          type="text"
          name="name"
          disabled={currentName !== optimisticName}
        />
      </p>
    </form>
  );
}
```

Хук `useOptimistic` немедленно отобразит `optimisticName`, пока выполняется запрос `updateName`. Когда обновление завершится или произойдет ошибка, React автоматически переключится обратно на значение `currentName`.

Для получения дополнительной информации см. документацию по [`useOptimistic`](/reference/react/useOptimistic).

### Новый API: `use` {/*new-feature-use*/}

В React 19 мы представляем новый API для чтения ресурсов в рендере: `use`.

Например, вы можете прочитать promise с помощью `use`, и React приостановится, пока promise не разрешится:

```js {1,5}
import {use} from 'react';

function Comments({commentsPromise}) {
  // `use` приостановится, пока promise не разрешится.
  const comments = use(commentsPromise);
  return comments.map(comment => <p key={comment.id}>{comment}</p>);
}

function Page({commentsPromise}) {
  // Когда `use` приостанавливается в Comments,
  // будет показана эта граница Suspense.
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Comments commentsPromise={commentsPromise} />
    </Suspense>
  )
}
```

<Note>

#### `use` не поддерживает promise, созданные в рендере. {/*use-does-not-support-promises-created-in-render*/}

Если вы попытаетесь передать promise, созданный в рендере, в `use`, React выдаст предупреждение:

<ConsoleBlockMulti>

<ConsoleLogLine level="error">

Компонент был приостановлен некэшированным promise. Создание promise внутри клиентского компонента или хука пока не поддерживается, за исключением случаев, когда используется библиотека или фреймворк, совместимый с Suspense.

</ConsoleLogLine>

</ConsoleBlockMulti>

Чтобы исправить это, вам нужно передать promise из библиотеки или фреймворка с поддержкой Suspense, который поддерживает кэширование для promise. В будущем мы планируем выпустить функции, упрощающие кэширование promise в рендере.

</Note>

Вы также можете читать контекст с помощью `use`, что позволяет вам условно читать Context, например, после ранних возвратов:

```js {1,11}
import {use} from 'react';
import ThemeContext from './ThemeContext'

function Heading({children}) {
  if (children == null) {
    return null;
  }
  
  // Это не будет работать с useContext
  // из-за раннего возврата.
  const theme = use(ThemeContext);
  return (
    <h1 style={{color: theme.color}}>
      {children}
    </h1>
  );
}
```

API `use` можно вызывать только в рендере, как и хуки. В отличие от хуков, `use` можно вызывать условно. В будущем мы планируем поддерживать больше способов потребления ресурсов в рендере с помощью `use`.

Для получения дополнительной информации см. документацию по [`use`](/reference/react/use).

## Новые статические API React DOM {/*new-react-dom-static-apis*/}

Мы добавили два новых API в `react-dom/static` для генерации статических сайтов:
- [`prerender`](/reference/react-dom/static/prerender)
- [`prerenderToNodeStream`](/reference/react-dom/static/prerenderToNodeStream)

Эти новые API улучшают `renderToString`, ожидая загрузки данных для генерации статического HTML. Они разработаны для работы со потоковыми средами, такими как Node.js Streams и Web Streams. Например, в среде Web Stream вы можете предварительно отобразить дерево React в статический HTML с помощью `prerender`:

```js
import { prerender } from 'react-dom/static';

async function handler(request) {
  const {prelude} = await prerender(<App />, {
    bootstrapScripts: ['/main.js']
  });
  return new Response(prelude, {
    headers: { 'content-type': 'text/html' },
  });
}
```

API prerender будут ждать загрузки всех данных, прежде чем вернуть статический HTML-поток. Потоки можно преобразовать в строки или отправить с потоковым ответом. Они не поддерживают потоковую передачу контента по мере его загрузки, что поддерживается существующими [API рендеринга на стороне сервера React DOM](/reference/react-dom/server).

Для получения дополнительной информации см. [Статические API React DOM](/reference/react-dom/static).
```

## React Server Components {/*react-server-components*/}

### Server Components {/*server-components*/}

Server Components — это новая опция, которая позволяет рендерить компоненты заранее, до бандлинга, в среде, отдельной от вашего клиентского приложения или SSR-сервера. Эта отдельная среда — это «сервер» в React Server Components. Server Components могут запускаться один раз во время сборки на вашем CI-сервере, или они могут запускаться для каждого запроса с использованием веб-сервера.

React 19 включает в себя все функции React Server Components, включенные из канала Canary. Это означает, что библиотеки, поставляемые с Server Components, теперь могут ориентироваться на React 19 в качестве зависимостей peer с [условием экспорта](https://github.com/reactjs/rfcs/blob/main/text/0227-server-module-conventions.md#react-server-conditional-exports) `react-server` для использования во фреймворках, которые поддерживают [Full-stack React Architecture](/learn/start-a-new-react-project#which-features-make-up-the-react-teams-full-stack-architecture-vision).

<Note>

#### Как мне создать поддержку Server Components? {/*how-do-i-build-support-for-server-components*/}

Хотя React Server Components в React 19 стабильны и не будут ломаться между минорными версиями, базовые API, используемые для реализации бандлера или фреймворка React Server Components, не соответствуют semver и могут ломаться между минорами в React 19.x.

Чтобы поддержать React Server Components в качестве бандлера или фреймворка, мы рекомендуем привязаться к определенной версии React или использовать Canary-релиз. Мы продолжим работать с бандлерами и фреймворками, чтобы стабилизировать API, используемые для реализации React Server Components в будущем.

</Note>

Подробнее см. документацию по [React Server Components](/reference/rsc/server-components).

### Server Actions {/*server-actions*/}

Server Actions позволяют Client Components вызывать асинхронные функции, выполняемые на сервере.

Когда Server Action определяется с помощью директивы `"use server"`, ваш фреймворк автоматически создаст ссылку на серверную функцию и передаст эту ссылку в Client Component. Когда эта функция вызывается на клиенте, React отправит запрос на сервер для выполнения функции и вернет результат.

<Note>

#### Нет директивы для Server Components. {/*there-is-no-directive-for-server-components*/}

Распространенное заблуждение состоит в том, что Server Components обозначаются как `"use server"`, но для Server Components нет директивы. Директива `"use server"` используется для Server Actions.

Для получения дополнительной информации см. документацию по [Директивам](/reference/rsc/directives).

</Note>

Server Actions могут быть созданы в Server Components и переданы в качестве пропсов в Client Components, или их можно импортировать и использовать в Client Components.

Подробнее см. документацию по [React Server Actions](/reference/rsc/server-actions).


```markdown
## Улучшения в React 19 {/*improvements-in-react-19*/}

### `ref` как проп {/*ref-as-a-prop*/}

Начиная с React 19, теперь можно получить доступ к `ref` как к пропу для функциональных компонентов:

```js [[1, 1, "ref"], [1, 2, "ref", 45], [1, 6, "ref", 14]]
function MyInput({placeholder, ref}) {
  return <input placeholder={placeholder} ref={ref} />
}

//...
<MyInput ref={ref} />
```

Новым функциональным компонентам больше не потребуется `forwardRef`, и мы опубликуем codemod для автоматического обновления ваших компонентов для использования нового пропа `ref`. В будущих версиях мы **устаревший** и удалим `forwardRef`.

<Note>

`refs`, переданные классам, не передаются как пропсы, поскольку они ссылаются на экземпляр компонента.

</Note>

### Различия для ошибок гидратации {/*diffs-for-hydration-errors*/}

Мы также улучшили отчеты об ошибках для ошибок гидратации в `react-dom`. Например, вместо регистрации нескольких ошибок в DEV без какой-либо информации о несоответствии:

<ConsoleBlockMulti>

<ConsoleLogLine level="error">

Warning: Text content did not match. Server: "Server" Client: "Client"
{'  '}at span
{'  '}at App

</ConsoleLogLine>

<ConsoleLogLine level="error">

Warning: An error occurred during hydration. The server HTML was replaced with client content in \<div\>.

</ConsoleLogLine>

<ConsoleLogLine level="error">

Warning: Text content did not match. Server: "Server" Client: "Client"
{'  '}at span
{'  '}at App

</ConsoleLogLine>

<ConsoleLogLine level="error">

Warning: An error occurred during hydration. The server HTML was replaced with client content in \<div\>.

</ConsoleLogLine>

<ConsoleLogLine level="error">

Uncaught Error: Text content does not match server-rendered HTML.
{'  '}at checkForUnmatchedText
{'  '}...

</ConsoleLogLine>

</ConsoleBlockMulti>

Теперь мы регистрируем одно сообщение с различием несоответствия:

<ConsoleBlockMulti>

<ConsoleLogLine level="error">

Uncaught Error: Hydration failed because the server rendered HTML didn't match the client. As a result this tree will be regenerated on the client. This can happen if an SSR-ed Client Component used:{'\n'}
\- A server/client branch `if (typeof window !== 'undefined')`.
\- Variable input such as `Date.now()` or `Math.random()` which changes each time it's called.
\- Date formatting in a user's locale which doesn't match the server.
\- External changing data without sending a snapshot of it along with the HTML.
\- Invalid HTML tag nesting.{'\n'}
It can also happen if the client has a browser extension installed which messes with the HTML before React loaded.{'\n'}
https://react.dev/link/hydration-mismatch {'\n'}
{'  '}\<App\>
{'    '}\<span\>
{'+    '}Client
{'-    '}Server{'\n'}
{'  '}at throwOnHydrationMismatch
{'  '}...

</ConsoleLogLine>

</ConsoleBlockMulti>

### `<Context>` как провайдер {/*context-as-a-provider*/}

В React 19 можно рендерить `<Context>` как провайдер вместо `<Context.Provider>`:

```js {5,7}
const ThemeContext = createContext('');

function App({children}) {
  return (
    <ThemeContext value="dark">
      {children}
    </ThemeContext>
  );  
}
```

Новые провайдеры Context могут использовать `<Context>`, и мы опубликуем codemod для преобразования существующих провайдеров. В будущих версиях мы **устаревший** `<Context.Provider>`.

### Функции очистки для refs {/*cleanup-functions-for-refs*/}

Теперь мы поддерживаем возврат функции очистки из колбэков `ref`:

```js {7-9}
<input
  ref={(ref) => {
    // ref создан

    // НОВОЕ: вернуть функцию очистки для сброса
    // ref, когда элемент удаляется из DOM.
    return () => {
      // очистка ref
    };
  }}
/>
```

Когда компонент размонтируется, React вызовет функцию очистки, возвращенную из колбэка `ref`. Это работает для DOM-рефов, рефов к классовым компонентам и `useImperativeHandle`.

<Note>

Ранее React вызывал функции `ref` с `null` при размонтировании компонента. Если ваш `ref` возвращает функцию очистки, React теперь пропустит этот шаг.

В будущих версиях мы **устаревший** вызов рефов с `null` при размонтировании компонентов.

</Note>

Из-за введения функций очистки ref, возврат чего-либо другого из колбэка `ref` теперь будет отклонен TypeScript. Исправление обычно заключается в прекращении использования неявных возвратов, например:

```diff [[1, 1, "("], [1, 1, ")"], [2, 2, "{", 15], [2, 2, "}", 1]]
- <div ref={current => (instance = current)} />
+ <div ref={current => {instance = current}} />
```

Исходный код возвращал экземпляр `HTMLDivElement`, и TypeScript не знал бы, _предполагалось_ ли это быть функцией очистки или вы не хотели возвращать функцию очистки.

Вы можете codemod этот шаблон с помощью [`no-implicit-ref-callback-return`](https://github.com/eps1lon/types-react-codemod/#no-implicit-ref-callback-return).

### `useDeferredValue` начальное значение {/*use-deferred-value-initial-value*/}

Мы добавили опцию `initialValue` в `useDeferredValue`:

```js [[1, 1, "deferredValue"], [1, 4, "deferredValue"], [2, 4, "''"]]
function Search({deferredValue}) {
  // При начальном рендере значение равно ''.
  // Затем запланирован повторный рендер с deferredValue.
  const value = useDeferredValue(deferredValue, '');
  
  return (
    <Results query={value} />
  );
}
````

Когда <CodeStep step={2}>initialValue</CodeStep> предоставлено, `useDeferredValue` вернет его как `value` для начального рендера компонента и запланирует повторный рендер в фоновом режиме с возвращенным <CodeStep step={1}>deferredValue</CodeStep>.

Подробнее см. [`useDeferredValue`](/reference/react/useDeferredValue).

### Поддержка метаданных документа {/*support-for-metadata-tags*/}

В HTML теги метаданных документа, такие как `<title>`, `<link>` и `<meta>`, зарезервированы для размещения в разделе `<head>` документа. В React компонент, который определяет, какие метаданные подходят для приложения, может находиться очень далеко от места, где вы рендерите `<head>`, или React вообще не рендерит `<head>`. В прошлом эти элементы нужно было вставлять вручную в эффект или с помощью таких библиотек, как [`react-helmet`](https://github.com/nfl/react-helmet), и требовали тщательной обработки при рендеринге приложения React на сервере.

В React 19 мы добавляем поддержку нативного рендеринга тегов метаданных документа в компонентах:

```js {5-8}
function BlogPost({post}) {
  return (
    <article>
      <h1>{post.title}</h1>
      <title>{post.title}</title>
      <meta name="author" content="Josh" />
      <link rel="author" href="https://twitter.com/joshcstory/" />
      <meta name="keywords" content={post.keywords} />
      <p>
        Eee equals em-see-squared...
      </p>
    </article>
  );
}
```

Когда React рендерит этот компонент, он увидит теги `<title>`, `<link>` и `<meta>` и автоматически переместит их в раздел `<head>` документа. Поддерживая эти теги метаданных нативно, мы можем гарантировать, что они будут работать с приложениями только для клиентов, потоковой передачей SSR и серверными компонентами.

<Note>

#### Вам все равно может понадобиться библиотека метаданных {/*you-may-still-want-a-metadata-library*/}

Для простых случаев использования рендеринг метаданных документа в виде тегов может быть подходящим, но библиотеки могут предлагать более мощные функции, такие как переопределение общих метаданных конкретными метаданными на основе текущего маршрута. Эти функции упрощают поддержку тегов метаданных для фреймворков и библиотек, таких как [`react-helmet`](https://github.com/nfl/react-helmet), вместо их замены.

</Note>

Для получения дополнительной информации см. документацию по [`<title>`](/reference/react-dom/components/title), [`<link>`](/reference/react-dom/components/link) и [`<meta>`](/reference/react-dom/components/meta).

### Поддержка таблиц стилей {/*support-for-stylesheets*/}

Таблицы стилей, как внешние ссылки (`<link rel="stylesheet" href="...">`), так и встроенные (`<style>...</style>`), требуют тщательного позиционирования в DOM из-за правил приоритета стилей. Создание возможности работы с таблицами стилей, которая позволяет компоновать их внутри компонентов, сложно, поэтому пользователи часто либо загружают все свои стили далеко от компонентов, которые могут зависеть от них, либо используют библиотеку стилей, которая инкапсулирует эту сложность.

В React 19 мы решаем эту проблему и обеспечиваем еще более глубокую интеграцию в Concurrent Rendering на клиенте и Streaming Rendering на сервере со встроенной поддержкой таблиц стилей. Если вы сообщите React о `precedence` вашей таблицы стилей, он будет управлять порядком вставки таблицы стилей в DOM и обеспечит загрузку таблицы стилей (если она внешняя) до отображения контента, который зависит от этих правил стиля.

```js {4,5,17}
function ComponentOne() {
  return (
    <Suspense fallback="loading...">
      <link rel="stylesheet" href="foo" precedence="default" />
      <link rel="stylesheet" href="bar" precedence="high" />
      <article class="foo-class bar-class">
        {...}
      </article>
    </Suspense>
  )
}

function ComponentTwo() {
  return (
    <div>
      <p>{...}</p>
      <link rel="stylesheet" href="baz" precedence="default" />  <-- will be inserted between foo & bar
    </div>
  )
}
```

Во время рендеринга на стороне сервера React включит таблицу стилей в `<head>`, что гарантирует, что браузер не будет рисовать, пока она не загрузится. Если таблица стилей будет обнаружена поздно, после того как мы уже начали потоковую передачу, React обеспечит вставку таблицы стилей в `<head>` на клиенте до отображения содержимого границы Suspense, которая зависит от этой таблицы стилей.

Во время рендеринга на стороне клиента React будет ждать загрузки вновь отрендеренных таблиц стилей, прежде чем зафиксировать рендер. Если вы рендерите этот компонент из нескольких мест в вашем приложении, React включит таблицу стилей только один раз в документ:

```js {5}
function App() {
  return <>
    <ComponentOne />
    ...
    <ComponentOne /> // не приведет к дублированию ссылки на таблицу стилей в DOM
  </>
}
```

Для пользователей, привыкших загружать таблицы стилей вручную, это возможность разместить эти таблицы стилей рядом с компонентами, которые зависят от них, что позволяет лучше локально рассуждать и упрощает обеспечение загрузки только тех таблиц стилей, от которых вы действительно зависите.

Библиотеки стилей и интеграции стилей с бандлерами также могут принять эту новую возможность, поэтому, даже если вы напрямую не рендерите свои собственные таблицы стилей, вы все равно можете извлечь выгоду, когда ваши инструменты будут обновлены для использования этой функции.

Для получения более подробной информации прочитайте документацию по [`<link>`](/reference/react-dom/components/link) и [`<style>`](/reference/react-dom/components/style).

### Поддержка асинхронных скриптов {/*support-for-async-scripts*/}

В HTML обычные скрипты (`<script src="...">`) и отложенные скрипты (`<script defer="" src="...">`) загружаются в порядке документа, что делает рендеринг этих видов скриптов глубоко внутри вашего дерева компонентов сложной задачей. Однако асинхронные скрипты (`<script async="" src="...">`) будут загружаться в произвольном порядке.

В React 19 мы включили лучшую поддержку асинхронных скриптов, позволив вам рендерить их в любом месте вашего дерева компонентов, внутри компонентов, которые фактически зависят от скрипта, без необходимости управлять перемещением и дедупликацией экземпляров скриптов.

```js {4,15}
function MyComponent() {
  return (
    <div>
      <script async={true} src="..." />
      Hello World
    </div>
  )
}

function App() {
  <html>
    <body>
      <MyComponent>
      ...
      <MyComponent> // не приведет к дублированию скрипта в DOM
    </body>
  </html>
}
```

Во всех средах рендеринга асинхронные скрипты будут дедуплицированы, чтобы React загружал и выполнял скрипт только один раз, даже если он рендерится несколькими разными компонентами.

В рендеринге на стороне сервера асинхронные скрипты будут включены в `<head>` и приоритизированы после более критичных ресурсов, которые блокируют отрисовку, таких как таблицы стилей, шрифты и предварительная загрузка изображений.

Для получения более подробной информации прочитайте документацию по [`<script>`](/reference/react-dom/components/script).

### Поддержка предварительной загрузки ресурсов {/*support-for-preloading-resources*/}

Во время начальной загрузки документа и при обновлениях на стороне клиента, сообщение браузеру о ресурсах, которые ему, вероятно, потребуется загрузить как можно раньше, может оказать существенное влияние на производительность страницы.

React 19 включает в себя ряд новых API для загрузки и предварительной загрузки ресурсов браузера, чтобы максимально упростить создание отличных интерфейсов, которые не сдерживаются неэффективной загрузкой ресурсов.

```js
import { prefetchDNS, preconnect, preload, preinit } from 'react-dom'
function MyComponent() {
  preinit('https://.../path/to/some/script.js', {as: 'script' }) // загружает и выполняет этот скрипт немедленно
  preload('https://.../path/to/font.woff', { as: 'font' }) // предварительно загружает этот шрифт
  preload('https://.../path/to/stylesheet.css', { as: 'style' }) // предварительно загружает эту таблицу стилей
  prefetchDNS('https://...') // когда вы, возможно, на самом деле ничего не запрашиваете с этого хоста
  preconnect('https://...') // когда вы что-то запросите, но не уверены, что именно
}
```
```html
<!-- вышеприведенное приведет к следующему DOM/HTML -->
<html>
  <head>
    <!-- ссылки/скрипты приоритизируются по их полезности для ранней загрузки, а не по порядку вызова -->
    <link rel="prefetch-dns" href="https://...">
    <link rel="preconnect" href="https://...">
    <link rel="preload" as="font" href="https://.../path/to/font.woff">
    <link rel="preload" as="style" href="https://.../path/to/stylesheet.css">
    <script async="" src="https://.../path/to/some/script.js"></script>
  </head>
  <body>
    ...
  </body>
</html>
```

Эти API можно использовать для оптимизации начальной загрузки страниц путем перемещения обнаружения дополнительных ресурсов, таких как шрифты, из загрузки таблиц стилей. Они также могут ускорить обновления клиента, предварительно загрузив список ресурсов, используемых ожидаемой навигацией, а затем немедленно предварительно загрузив эти ресурсы по щелчку или даже при наведении курсора.

Для получения более подробной информации см. [API предварительной загрузки ресурсов](/reference/react-dom#resource-preloading-apis).

### Совместимость со сторонними скриптами и расширениями {/*compatibility-with-third-party-scripts-and-extensions*/}

Мы улучшили гидратацию, чтобы учитывать сторонние скрипты и расширения браузера.

При гидратации, если элемент, который рендерится на клиенте, не соответствует элементу, найденному в HTML с сервера, React принудительно выполнит повторный рендер клиента, чтобы исправить содержимое. Ранее, если элемент был вставлен сторонними скриптами или расширениями браузера, это вызывало ошибку несоответствия и рендер клиента.

В React 19 неожиданные теги в `<head>` и `<body>` будут пропущены, что позволит избежать ошибок несоответствия. Если React необходимо повторно отрендерить весь документ из-за не связанного с этим несоответствия гидратации, он оставит на месте таблицы стилей, вставленные сторонними скриптами и расширениями браузера.
```

### Улучшенная обработка ошибок {/*error-handling*/}

Мы улучшили обработку ошибок в React 19, чтобы удалить дублирование и предоставить возможности для обработки перехваченных и необработанных ошибок. Например, когда в рендере возникает ошибка, перехваченная предохранителем, ранее React выбрасывал ошибку дважды (один раз для исходной ошибки, затем снова после сбоя автоматического восстановления), а затем вызывал `console.error` с информацией о том, где произошла ошибка.

Это приводило к трем ошибкам для каждой перехваченной ошибки:

<ConsoleBlockMulti>

<ConsoleLogLine level="error">

Uncaught Error: hit
{'  '}at Throws
{'  '}at renderWithHooks
{'  '}...

</ConsoleLogLine>

<ConsoleLogLine level="error">

Uncaught Error: hit<span className="ms-2 text-gray-30">{'    <--'} Duplicate</span>
{'  '}at Throws
{'  '}at renderWithHooks
{'  '}...

</ConsoleLogLine>

<ConsoleLogLine level="error">

The above error occurred in the Throws component:
{'  '}at Throws
{'  '}at ErrorBoundary
{'  '}at App{'\n'}
React will try to recreate this component tree from scratch using the error boundary you provided, ErrorBoundary.

</ConsoleLogLine>

</ConsoleBlockMulti>

В React 19 мы регистрируем одну ошибку со всей включенной информацией об ошибке:

<ConsoleBlockMulti>

<ConsoleLogLine level="error">

Error: hit
{'  '}at Throws
{'  '}at renderWithHooks
{'  '}...{'\n'}
The above error occurred in the Throws component:
{'  '}at Throws
{'  '}at ErrorBoundary
{'  '}at App{'\n'}
React will try to recreate this component tree from scratch using the error boundary you provided, ErrorBoundary.
{'  '}at ErrorBoundary
{'  '}at App

</ConsoleLogLine>

</ConsoleBlockMulti>

Кроме того, мы добавили два новых корневых параметра в дополнение к `onRecoverableError`:

- `onCaughtError`: вызывается, когда React перехватывает ошибку в предохранителе.
- `onUncaughtError`: вызывается, когда ошибка выбрасывается и не перехватывается предохранителем.
- `onRecoverableError`: вызывается, когда ошибка выбрасывается и автоматически восстанавливается.

Для получения дополнительной информации и примеров см. документацию по [`createRoot`](/reference/react-dom/client/createRoot) и [`hydrateRoot`](/reference/react-dom/client/hydrateRoot).

### Поддержка пользовательских элементов {/*support-for-custom-elements*/}

React 19 добавляет полную поддержку пользовательских элементов и проходит все тесты на [Custom Elements Everywhere](https://custom-elements-everywhere.com/).

В прошлых версиях использование пользовательских элементов в React было затруднено, потому что React рассматривал неопознанные пропсы как атрибуты, а не свойства. В React 19 мы добавили поддержку свойств, которая работает на клиенте и во время SSR, со следующей стратегией:

- **Рендеринг на стороне сервера**: пропсы, переданные пользовательскому элементу, будут отображаться как атрибуты, если их тип является примитивным значением, таким как `string`, `number`, или значение равно `true`. Пропсы с непростыми типами, такими как `object`, `symbol`, `function`, или значением `false`, будут опущены.
- **Рендеринг на стороне клиента**: пропсы, соответствующие свойству экземпляра пользовательского элемента, будут назначены как свойства, в противном случае они будут назначены как атрибуты.

Спасибо [Joey Arhar](https://github.com/josepharhar) за руководство дизайном и реализацией поддержки пользовательских элементов в React.

#### Как обновиться {/*how-to-upgrade*/}

См. [Руководство по обновлению React 19](/blog/2024/04/25/react-19-upgrade-guide) для получения пошаговых инструкций и полного списка критических и важных изменений.

_Примечание: этот пост был первоначально опубликован 25.04.2024 и был обновлен до 05.12.2024 со стабильным релизом._