---
title: preloadModule
---

<Note>

[Фреймворки на основе React](/learn/start-a-new-react-project) часто сами обрабатывают загрузку ресурсов, поэтому вам может не понадобиться вызывать этот API самостоятельно. Обратитесь к документации вашего фреймворка для получения подробной информации.

</Note>

<Intro>

`preloadModule` позволяет предварительно загрузить ESM-модуль, который вы ожидаете использовать.

```js
preloadModule("https://example.com/module.js", {as: "script"});
```

</Intro>

<InlineToc />

---

## Справочник {/*reference*/}

### `preloadModule(href, options)` {/*preloadmodule*/}

Чтобы предварительно загрузить ESM-модуль, вызовите функцию `preloadModule` из `react-dom`.

```js
import { preloadModule } from 'react-dom';

function AppRoot() {
  preloadModule("https://example.com/module.js", {as: "script"});
  // ...
}

```

[См. примеры ниже.](#usage)

Функция `preloadModule` даёт браузеру подсказку о том, что следует начать загрузку указанного модуля, что может сэкономить время.

#### Параметры {/*parameters*/}

* `href`: строка. URL модуля, который вы хотите загрузить.
* `options`: объект. Содержит следующие свойства:
  *  `as`: обязательная строка. Должна быть `'script'`.
  *  `crossOrigin`: строка. [Политика CORS](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin), которую следует использовать. Возможные значения: `anonymous` и `use-credentials`.
  *  `integrity`: строка. Криптографический хэш модуля для [проверки его подлинности](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity).
  *  `nonce`: строка. Криптографический [nonce для разрешения модуля](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/nonce) при использовании строгой политики безопасности контента (Content Security Policy).


#### Возвращает {/*returns*/}

`preloadModule` ничего не возвращает.

#### Ограничения {/*caveats*/}

* Многократные вызовы `preloadModule` с одинаковым `href` имеют тот же эффект, что и один вызов.
* В браузере вы можете вызывать `preloadModule` в любой ситуации: во время рендеринга компонента, в Effect, в обработчике событий и т. д.
* При серверном рендеринге или при рендеринге Server Components `preloadModule` имеет эффект только в том случае, если вы вызываете его во время рендеринга компонента или в асинхронном контексте, возникшем в результате рендеринга компонента. Любые другие вызовы будут проигнорированы.

---

## Использование {/*usage*/}

### Предварительная загрузка во время рендеринга {/*preloading-when-rendering*/}

Вызывайте `preloadModule` при рендеринге компонента, если вы знаете, что он или его дочерние компоненты будут использовать определённый модуль.

```js
import { preloadModule } from 'react-dom';

function AppRoot() {
  preloadModule("https://example.com/module.js", {as: "script"});
  return ...;
}
```

Если вы хотите, чтобы браузер немедленно начал выполнение модуля (а не просто загрузку), вместо этого используйте [`preinitModule`](/reference/react-dom/preinitModule). Если вы хотите загрузить скрипт, который не является ESM-модулем, используйте [`preload`](/reference/react-dom/preload).

### Предварительная загрузка в обработчике событий {/*preloading-in-an-event-handler*/}

Вызывайте `preloadModule` в обработчике событий перед переходом на страницу или в состояние, где модуль будет необходим. Это позволит начать процесс раньше, чем если бы вы вызвали его во время рендеринга новой страницы или состояния.

```js
import { preloadModule } from 'react-dom';

function CallToAction() {
  const onClick = () => {
    preloadModule("https://example.com/module.js", {as: "script"});
    startWizard();
  }
  return (
    <button onClick={onClick}>Start Wizard</button>
  );
}
```