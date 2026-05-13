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

  \  * [Универсальное руководство по стилю](#Универсальное руководство по стилю)

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
date: 2024/05/22
description: На прошлой неделе мы провели React Conf 2024, двухдневную
  конференцию в Хендерсоне, штат Невада, где более 700 участников собрались
  лично, чтобы обсудить последние достижения в области UI-инженерии. В этой
  статье мы подведём итоги выступлений и анонсов с мероприятия.
---
```
May 22, 2024 от [Ricky Hanlon](https://twitter.com/rickhanlonii).

---

<Intro>

На прошлой неделе мы провели React Conf 2024, двухдневную конференцию в Хендерсоне, штат Невада, где более 700 участников собрались лично, чтобы обсудить последние достижения в области разработки пользовательских интерфейсов. Это была наша первая личная конференция с 2019 года, и мы были рады снова собрать сообщество вместе.

</Intro>

---

На React Conf 2024 мы анонсировали [React 19 RC](/blog/2024/12/05/react-19), [React Native New Architecture Beta](https://github.com/reactwg/react-native-new-architecture/discussions/189) и экспериментальный выпуск [React Compiler](/learn/react-compiler). Сообщество также вышло на сцену, чтобы анонсировать [React Router v7](https://remix.run/blog/merging-remix-and-react-router), [Universal Server Components](https://www.youtube.com/watch?v=T8TZQ6k4SLE&t=20765s) в Expo Router, React Server Components в [RedwoodJS](https://redwoodjs.com/blog/rsc-now-in-redwoodjs) и многое другое.

Полные записи [первого дня](https://www.youtube.com/watch?v=T8TZQ6k4SLE) и [второго дня](https://www.youtube.com/watch?v=0ckOUBiuxVY) доступны онлайн. В этой статье мы подведем итоги выступлений и анонсов с мероприятия.

## День 1 {/*day-1*/}

_[Посмотреть полную запись первого дня можно здесь.](https://www.youtube.com/watch?v=T8TZQ6k4SLE&t=973s)_

Чтобы открыть первый день, технический директор Meta [Andrew "Boz" Bosworth](https://www.threads.net/@boztank) поделился приветственным словом, за которым последовало вступление от [Seth Webster](https://twitter.com/sethwebster), который руководит React Org в Meta, и нашего MC [Ashley Narcisse](https://twitter.com/_darkfadr).

В основном докладе первого дня [Joe Savona](https://twitter.com/en_JS) поделился нашими целями и видением React, чтобы упростить для всех создание отличного пользовательского опыта. [Lauren Tan](https://twitter.com/potetotes) выступила со State of React, где она сообщила, что React был загружен более 1 миллиарда раз в 2023 году и что 37% новых разработчиков изучают программирование с помощью React. Наконец, она отметила работу сообщества React по созданию React, React.

Для получения дополнительной информации ознакомьтесь со следующими выступлениями сообщества, которые состоялись позже на конференции:

- [Vanilla React](https://www.youtube.com/watch?v=T8TZQ6k4SLE&t=5542s) от [Ryan Florence](https://twitter.com/ryanflorence)
- [React Rhythm & Blues](https://www.youtube.com/watch?v=0ckOUBiuxVY&t=12728s) от [Lee Robinson](https://twitter.com/leeerob)
- [RedwoodJS, теперь с React Server Components](https://www.youtube.com/watch?v=T8TZQ6k4SLE&t=26815s) от [Amy Dutton](https://twitter.com/selfteachme)
- [Представляем Universal React Server Components в Expo Router](https://www.youtube.com/watch?v=T8TZQ6k4SLE&t=20765s) от [Evan Bacon](https://twitter.com/Baconbrix)

Далее в основном докладе [Josh Story](https://twitter.com/joshcstory) и [Andrew Clark](https://twitter.com/acdlite) поделились новыми функциями, которые появятся в React 19, и анонсировали React 19 RC, который готов к тестированию в продакшене. Ознакомьтесь со всеми функциями в [публикации о выпуске React 19](/blog/2024/12/05/react-19) и посмотрите эти выступления, чтобы узнать подробности о новых функциях:

- [Что нового в React 19](https://www.youtube.com/watch?v=T8TZQ6k4SLE&t=8880s) от [Lydia Hallie](https://twitter.com/lydiahallie)
- [React Unpacked: A Roadmap to React 19](https://www.youtube.com/watch?v=T8TZQ6k4SLE&t=10112s) от [Sam Selikoff](https://twitter.com/samselikoff)
- [React 19 Deep Dive: Coordinating HTML](https://www.youtube.com/watch?v=T8TZQ6k4SLE&t=24916s) от [Josh Story](https://twitter.com/joshcstory)
- [Улучшение форм с помощью React Server Components](https://www.youtube.com/watch?v=0ckOUBiuxVY&t=25280s) от [Aurora Walberg Scharff](https://twitter.com/aurorascharff)
- [React для двух компьютеров](https://www.youtube.com/watch?v=T8TZQ6k4SLE&t=18825s) от [Dan Abramov](https://bsky.app/profile/danabra.mov)
- [И теперь вы понимаете React Server Components](https://www.youtube.com/watch?v=0ckOUBiuxVY&t=11256s) от [Kent C. Dodds](https://twitter.com/kentcdodds)

Наконец, мы завершили основной доклад тем, что [Joe Savona](https://twitter.com/en_JS), [Sathya Gunasekaran](https://twitter.com/_gsathya) и [Mofei Zhang](https://twitter.com/zmofei) объявили, что React Compiler теперь [Open Source](https://github.com/facebook/react/pull/29061), и поделились экспериментальной версией React Compiler для тестирования.

Для получения дополнительной информации об использовании Compiler и о том, как он работает, ознакомьтесь с [документацией](/learn/react-compiler) и этими выступлениями:

- [Забудьте о Memo](https://www.youtube.com/watch?v=T8TZQ6k4SLE&t=12020s) от [Lauren Tan](https://twitter.com/lydiahallie)
- [React Compiler Deep Dive](https://www.youtube.com/watch?v=0ckOUBiuxVY&t=9313s) от [Sathya Gunasekaran](https://twitter.com/_gsathya) и [Mofei Zhang](https://twitter.com/zmofei)

Посмотрите полный основной доклад первого дня здесь:

<YouTubeIframe src="https://www.youtube.com/embed/T8TZQ6k4SLE?t=973s" />

## День 2 {/*day-2*/}

_[Посмотреть полную запись второго дня можно здесь.](https://www.youtube.com/watch?v=0ckOUBiuxVY&t=1720s)_

Чтобы открыть второй день, [Seth Webster](https://twitter.com/sethwebster) поделился приветственным словом, за которым последовало «Спасибо» от [Eli White](https://x.com/Eli_White) и вступление от нашего Chief Vibes Officer [Ashley Narcisse](https://twitter.com/_darkfadr).

В основном докладе второго дня [Nicola Corti](https://twitter.com/cortinico) поделился State of React Native, включая 78 миллионов загрузок в 2023 году. Он также выделил приложения, использующие React Native, в том числе более 2000 экранов, используемых в Meta; страницу с информацией о продукте в Facebook Marketplace, которую посещают более 2 миллиардов раз в день; и часть меню «Пуск» Microsoft Windows, а также некоторые функции почти в каждом продукте Microsoft Office для мобильных устройств и настольных компьютеров.

Nicola также выделил всю работу, которую сообщество делает для поддержки React Native, включая библиотеки, фреймворки и несколько платформ. Для получения дополнительной информации ознакомьтесь со следующими выступлениями сообщества:

- [Расширение React Native за пределы мобильных и настольных приложений](https://www.youtube.com/watch?v=0ckOUBiuxVY&t=5798s) от [Chris Traganos](https://twitter.com/chris_trag) и [Anisha Malde](https://twitter.com/anisha_malde)
- [Пространственные вычисления с React](https://www.youtube.com/watch?v=0ckOUBiuxVY&t=22525s) от [Michał Pierzchała](https://twitter.com/thymikee)

[Riccardo Cipolleschi](https://twitter.com/cipolleschir) продолжил основной доклад второго дня, объявив, что React Native New Architecture теперь находится в бета-версии и готов к внедрению приложениями в продакшене. Он поделился новыми функциями и улучшениями в новой архитектуре, а также поделился дорожной картой будущего React Native. Для получения дополнительной информации ознакомьтесь со следующими материалами:

- [Cross Platform React](https://www.youtube.com/watch?v=0ckOUBiuxVY&t=26569s) от [Olga Zinoveva](https://github.com/SlyCaptainFlint) и [Naman Goel](https://twitter.com/naman34)

Далее в основном докладе Nicola объявил, что теперь мы рекомендуем начинать с фреймворка, такого как Expo, для всех новых приложений, созданных с помощью React Native. С этим изменением он также анонсировал новую домашнюю страницу React Native и новую документацию «Начало работы». Вы можете просмотреть новое руководство по началу работы в [документации React Native](https://reactnative.dev/docs/next/environment-setup).

Наконец, чтобы завершить основной доклад, [Kadi Kraman](https://twitter.com/kadikraman) поделилась последними функциями и улучшениями в Expo, а также информацией о том, как начать разработку с помощью React Native, используя Expo.

Посмотрите полный основной доклад второго дня здесь:

<YouTubeIframe src="https://www.youtube.com/embed/0ckOUBiuxVY?t=1720s" />

## Вопросы и ответы {/*q-and-a*/}

Команды React и React Native также завершали каждый день сессией вопросов и ответов:

- [React Q&A](https://www.youtube.com/watch?v=T8TZQ6k4SLE&t=27518s) под руководством [Michael Chan](https://twitter.com/chantastic)
- [React Native Q&A](https://www.youtube.com/watch?v=0ckOUBiuxVY&t=27935s) под руководством [Jamon Holmgren](https://twitter.com/jamonholmgren)

## И многое другое... {/*and-more*/}

Мы также услышали выступления о доступности, отчетах об ошибках, css и многом другом:

- [Развенчание доступности в React-приложениях](https://www.youtube.com/watch?v=0ckOUBiuxVY&t=20655s) от [Kateryna Porshnieva](https://twitter.com/krambertech)
- [Pigment CSS, CSS в эпоху серверных компонентов](https://www.youtube.com/watch?v=0ckOUBiuxVY&t=21696s) от [Olivier Tassinari](https://twitter.com/olivtassinari)
- [React Server Components в реальном времени](https://www.youtube.com/watch?v=T8TZQ6k4SLE&t=24070s) от [Sunil Pai](https://twitter.com/threepointone)
- [Давайте нарушим правила React](https://www.youtube.com/watch?v=T8TZQ6k4SLE&t=25862s) от [Charlotte Isambert](https://twitter.com/c_isambert)
- [Решение 100% ваших ошибок](https://www.youtube.com/watch?v=0ckOUBiuxVY&t=19881s) от [Ryan Albrecht](https://github.com/ryan953)

## Спасибо {/*thank-you*/}

Спасибо всем сотрудникам, докладчикам и участникам, которые сделали React Conf 2024 возможным. Их слишком много, чтобы перечислять, но мы хотим поблагодарить некоторых в частности.

Спасибо [Barbara Markiewicz](https://twitter.com/barbara_markie), команде [Callstack](https://www.callstack.com/) и нашему защитнику интересов разработчиков React Team [Matt Carroll](https://twitter.com/mattcarrollcode) за помощь в планировании всего мероприятия; и [Sunny Leggett](https://zeroslopeevents.com/about) и всем из [Zero Slope](https://zeroslopeevents.com) за помощь в организации мероприятия.

Спасибо [Ashley Narcisse](https://twitter.com/_darkfadr) за то, что она была нашим MC и Chief Vibes Officer; и [Michael Chan](https://twitter.com/chantastic) и [Jamon Holmgren](https://twitter.com/jamonholmgren) за проведение сессий вопросов и ответов.

Спасибо [Seth Webster](https://twitter.com/sethwebster) и [Eli White](https://x.com/Eli_White) за то, что приветствовали нас каждый день и давали направление по структуре и содержанию; и [Tom Occhino](https://twitter.com/tomocchino) за то, что присоединился к нам со специальным сообщением во время афтер-пати.

Спасибо [Ricky Hanlon](https://www.youtube.com/watch?v=FxTZL2U-uKg&t=1263s) за предоставление подробных отзывов о выступлениях, работу над дизайном слайдов и в целом заполнение пробелов, чтобы проработать детали.

Спасибо [Callstack](https://www.callstack.com/) за создание веб-сайта конференции; и [Kadi Kraman](https://twitter.com/kadikraman) и команде [Expo](https://expo.dev/) за создание мобильного приложения конференции.

Спасибо всем спонсорам, которые сделали мероприятие возможным: [Remix](https://remix.run/), [Amazon](https://developer.amazon.com/apps-and-games?cmp=US_2024_05_3P_React-Conf-2024&ch=prtnr&chlast=prtnr&pub=ref&publast=ref&type=org&typelast=org), [MUI](https://mui.com/), [Sentry](https://sentry.io/for/react/?utm_source=sponsored-conf&utm_medium=sponsored-event&utm_campaign=frontend-fy25q2-evergreen&utm_content=logo-reactconf2024-learnmore), [Abbott](https://www.jobs.abbott/software), [Expo](https://expo.dev/), [RedwoodJS](https://redwoodjs.com/) и [Vercel](https://vercel.com).

Спасибо команде AV за визуальные эффекты, сцену и звук; и отелю Westin за гостеприимство.

Спасибо всем докладчикам, которые поделились своими знаниями и опытом с сообществом.

Наконец, спасибо всем, кто присутствовал лично и онлайн, чтобы показать, что делает React, React. React — это больше, чем просто библиотека, это сообщество, и было вдохновляюще видеть, как все собираются вместе, чтобы делиться и учиться вместе.

До встречи в следующий раз!
```