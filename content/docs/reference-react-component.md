---
id: react-component
title: React.Component
layout: docs
category: Reference
permalink: docs/react-component.html
redirect_from:
  - "docs/component-api.html"
  - "docs/component-specs.html"
  - "docs/component-specs-ko-KR.html"
  - "docs/component-specs-zh-CN.html"
  - "tips/UNSAFE_componentWillReceiveProps-not-triggered-after-mounting.html"
  - "tips/dom-event-listeners.html"
  - "tips/initial-ajax.html"
  - "tips/use-react-with-other-libraries.html"
---

Ця сторінка містить API довідник для визначення класового компонента React. Ми припускаємо, що ви знайомі з фундаментальними концепціями React, такими як [Компоненти та пропси](/docs/components-and-props.html), а також [Стан і життєвий цикл](/docs/state-and-lifecycle.html). Якщо ні, то спочатку ознайомтеся з ними.

## Огляд {#overview}

React дозволяє вам визначати компоненти як класи чи функції. Компоненти визначені як класи, наразі надають більше можливостей, які детально описані на цій сторінці. Щоб визначити класовий React-компонент, вам потрібно розширити `React.Component`:

```js
class Welcome extends React.Component {
  render() {
    return <h1>Привіт, {this.props.name}</h1>;
  }
}
```

Єдиний метод, який ви *зобов'язані* визначити в підкласі `React.Component` називається [`render()`](#render). Всі інші методи описані на цій сторінці є необов'язковими.

**Ми наполегливо рекомендуємо, щоб ви утримались від створення власних базових класів компонента.** У компонентах React, [повторне використання коду в першу чергу досягається за допомогою композиції, а не наслідування](/docs/composition-vs-inheritance.html).

>Примітка:
>
>React не змушує вас використовувати синтаксис класів ES6. Якщо ви намагаєтесь уникати його, натомість ви можете використовувати `create-react-class` модуль чи схожу власну абстракцію. Перегляньте [Використання React без ES6](/docs/react-without-es6.html) щоб дізнатися більше.

### Життєвий цикл компонента {#the-component-lifecycle}

Кожен компонент має декілька "методів життєвого циклу", які ви можете перевизначати, щоб запускати код в певний момент часу. **Ви можете використовувати [цю діаграму життєвого циклу](http://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/) як шпаргалку.** В списку нижче найчастіше вживані методи життєвого циклу виділені **напівжирним**. Решта існують лише для випадків, що трапляються відносно нечасто.

#### Монтування {#mounting}

Ці методи викликаються в наступному порядку, коли екземпляр компонента створюється і вставляється в DOM:

- [**`constructor()`**](#constructor)
- [`static getDerivedStateFromProps()`](#static-getderivedstatefromprops)
- [**`render()`**](#render)
- [**`componentDidMount()`**](#componentdidmount)

>Примітка:
>
>Ці методи вважаються застарілими і ви маєте [уникати їх](/blog/2018/03/27/update-on-async-rendering.html) в новому коді:
>
>- [`UNSAFE_componentWillMount()`](#unsafe_componentwillmount)

#### Оновлення {#updating}

Оновлення може бути спричиненим зміною пропсів чи стану. Ці методи викликаються в наступному порядку, коли компонент повторно рендериться:

- [`static getDerivedStateFromProps()`](#static-getderivedstatefromprops)
- [`shouldComponentUpdate()`](#shouldcomponentupdate)
- [**`render()`**](#render)
- [`getSnapshotBeforeUpdate()`](#getsnapshotbeforeupdate)
- [**`componentDidUpdate()`**](#componentdidupdate)

>Примітка:
>
>Ці методи вважаються застарілими і ви маєте [уникати їх](/blog/2018/03/27/update-on-async-rendering.html) в новому коді:
>
>- [`UNSAFE_componentWillUpdate()`](#unsafe_componentwillupdate)
>- [`UNSAFE_componentWillReceiveProps()`](#unsafe_componentwillreceiveprops)

#### Демонтування {#unmounting}

Цей метод викликається, коли компонент видаляється з DOM:

- [**`componentWillUnmount()`**](#componentwillunmount)

#### Обробка помилок {#error-handling}

Наступні методи викликаються, якщо виникла помилка під час рендерингу в методі житєвого циклу чи конструкторі будь-якого дочірнього компонента.

- [`static getDerivedStateFromError()`](#static-getderivedstatefromerror)
- [`componentDidCatch()`](#componentdidcatch)

### Інші API {#other-apis}

Кожен компонент також надає деякі інші API:

  - [`setState()`](#setstate)
  - [`forceUpdate()`](#forceupdate)

### Властивості класу {#class-properties}

  - [`defaultProps`](#defaultprops)
  - [`displayName`](#displayname)

### Властивості екземпляру {#instance-properties}

  - [`props`](#props)
  - [`state`](#state)

* * *

## Довідка {#reference}

### Часто використовані методи життєвого циклу {#commonly-used-lifecycle-methods}

Методи в цьому розділі охоплюють переважну більшість випадків з якими ви зустрінетесь під час створення React-компонентів. **Для наочної ілюстрації, перегляньте [цю діаграму життєвого циклу](http://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/).**

### `render()` {#render}

```javascript
render()
```

Метод `render()` — єдиний необхідний метод в класових компонентах.

Під час виклику він перевіряє `this.props` та `this.state` і повертає один з наступних типів:

- **React-елементи.** Зазвичай створені за допомогою [JSX](/docs/introducing-jsx.html). Наприклад, `<div />` і `<MyComponent />` є React-елементами, які інструктують React відрендерити вузол DOM або інший компонент визначений користувачем, відповідно.
- **Масиви та фрагменти.** Дозволяють повернути декілька елементів під час рендерингу. Перегляньте документацію для [фрагментів](/docs/fragments.html), щоб дізнатися більше.
- **Портали**. Дозволють рендерити дочірні елементи в іншому піддереві DOM. Перегляньте документацію для [порталів](/docs/portals.html), щоб дізнатися більше.
- **Рядки і числа.** Будуть відрендерені як текстові вузли в DOM.
- **Логічні значення чи `null`**. Не рендерять нічого. (Існують, здебільшого, для підтримки шаблону `return test && <Child />`, де `test` — логічне значення.)

Функція `render()` має бути чистою, а це означає, що вона не змінює стан компонента, повертає однаковий результат при кожному виклику і не взаємодіє з браузером напряму.

Якщо вам потрібно взаємодіяти з браузером, виконуйте необхідні дії в `componentDidMount()` чи інших методах життєвого циклу. Збереження `render()` чистим, робить компонент легшим для розуміння.

> Примітка
>
> `render()` не викличеться, якщо [`shouldComponentUpdate()`](#shouldcomponentupdate) повертає false.

* * *

### `constructor()` {#constructor}

```javascript
constructor(props)
```

**Якщо ви не ініціалізуєте стан і не прив'язуєте методи, вам не потрібно реалізовувати конструктор у вашому React-компоненті.**

Конструктор для React-компонента викликається до того, як він буде примонтований. При реалізації конструктора для підкласу `React.Component`, ви маєте викликати `super(props)` перед будь-яким іншим виразом. У іншому випадку, `this.props` буде невизначеним в конструкторі, що може призвести до помилок.

Як правило, у React конструктори використовуються лише для двох цілей:

* Ініціалізація [локального стану](/docs/state-and-lifecycle.html) шляхом присвоєння об'єкта `this.state`.
* Прив'язка методів [обробника подій](/docs/handling-events.html) до екземпляру.

**Не варто викликати `setState()`** у `constructor()`. Натомість, якщо компонент потребує використання локального стану, **присвоюйте початкове значення `this.state`** безпосередньо в конструкторі:

```js
constructor(props) {
  super(props);
  // Не викликайте this.setState() тут!
  this.state = { counter: 0 };
  this.handleClick = this.handleClick.bind(this);
}
```

Конструктор — це єдине місце, де ви маєте присвоювати `this.state` напряму. У всіх інших методах для цього слід використовувати `this.setState()`.

Уникайте додавання будь-яких побічних ефектів чи підписок у конструкторі. Для таких випадків використовуйте `componentDidMount()`.

>Примітка
>
>**Уникайте копіювання пропсів в стан! Це поширена помилка:**
>
>```js
>constructor(props) {
>  super(props);
>  // Не робіть цього!
>  this.state = { color: props.color };
>}
>```
>
>Проблема в тому, що це є і надлишковим (ви можете просто використати `this.props.color` напряму), і приводить до помилок (оновлення пропу `color` не буде зафіксоване в стані).
>
>**Використовуйте даний підхід лише тоді, коли ви навмисно хочете ігнорувати оновлення пропу.** В такому випадку є сенс перейменувати проп в `initialColor` чи `defaultColor`. Потім ви можете змусити компонент "скинути" його внутрішній стан, [змінивши його `key`](/blog/2018/06/07/you-probably-dont-need-derived-state.html#recommendation-fully-uncontrolled-component-with-a-key), за необхідності.
>
>Прочитайте нашу [статтю в блозі про уникнення похідного стану](/blog/2018/06/07/you-probably-dont-need-derived-state.html), щоб дізнатися що робити, якщо вам потрібен деякий стан, залежний від пропсів.


* * *

### `componentDidMount()` {#componentdidmount}

```javascript
componentDidMount()
```

`componentDidMount()` викликається відразу після монтування компонента (вставки в DOM-дерево). Ініціалізація, яка потребує DOM-вузли, має знаходитись тут. Якщо вам потрібно завантажити дані з віддаленого сервера, це гарне місце для створення мережевого запиту.

Також цей метод є вдалим місцем для налаштування підписок. Якщо ви це зробите, то не забудьте відписатись в `componentWillUnmount()`.

Ви **можете негайно викликати `setState()`** в `componentDidMount()`. Це запустить додатковий рендер, але це станеться до того, як браузер оновить екран. Це гарантує те, що навіть якщо `render()` в цьому випадку буде викликаний двічі, користувач не побачить проміжного стану. Обережно використовуйте цей підхід, тому що він часто приводить до проблем з продуктивністю. У більшості випадків, замість цього у вас має бути можливість присвоїти початковий стан у `constructor()`. Однак, це може бути необхідно для таких випадків як модальні вікна і спливаючі підказки, коли вам потрібно відрендерити щось, що залежить від розмірів та позиції вузла DOM.

* * *

### `componentDidUpdate()` {#componentdidupdate}

```javascript
componentDidUpdate(prevProps, prevState, snapshot)
```

`componentDidUpdate()` викликається відразу після оновлення. Цей метод не викликається під час першого рендеру.

Використовуйте це як можливість працювати з DOM при оновленні компонента. Також це хороше місце для мережевих запитів, якщо ви порівнюєте поточні пропси з попередніми (наприклад, мережевий запит може бути не потрібним, якщо проп не змінився).

```js
componentDidUpdate(prevProps) {
  // Типове використання (не забудьте порівняти пропси):
  if (this.props.userID !== prevProps.userID) {
    this.fetchData(this.props.userID);
  }
}
```

Ви **можете відразу викликати `setState()`** у `componentDidUpdate()`, але зверніть увагу, що цей виклик **має бути обгорнутий в умову** як у прикладі вище, інакше можна спричинити безкінечний цикл. Крім того, це спричинить додатковий повторний рендер який, хоч і не буде видимий користувачу, може вплинути на продуктивність компонента. Якщо ви намагаєтесь "дзеркально відобразити" певний стан в пропі, що приходять зверху, розгляньте безпосереднє використання пропу. Докладніше про те, [чому копіювання пропсів в стан спричиняє помилки](/blog/2018/06/07/you-probably-dont-need-derived-state.html).

Якщо ваш компонент реалізує метод життєвого циклу `getSnapshotBeforeUpdate()` (що трапляється доволі рідко), значення, яке він повертає, буде передане третім "snapshot" параметром в `componentDidUpdate()`. В іншому випадку цей параметр буде невизначеним.

> Примітка
>
> `componentDidUpdate()` не викликається, якщо [`shouldComponentUpdate()`](#shouldcomponentupdate) повертає false.

* * *

### `componentWillUnmount()` {#componentwillunmount}

```javascript
componentWillUnmount()
```

`componentWillUnmount()` викликається безпосередньо перед тим як компонент буде демонтовано і знищено. Виконуйте будь-яку необхідну очистку в цьому методі, таку як скасування таймерів, мережевих запитів чи підписок створених у `componentDidMount()`.

Ви **не повинні викликати `setState()`** у `componentWillUnmount()`, тому що компонент не буде повторно рендеритись. Як тільки екземпляр компонента буде демонтований, він ніколи не буде примонтованим знову.

* * *

### Рідковживані методи життєвого циклу {#rarely-used-lifecycle-methods}

Методи в цьому розділі відповідають малопоширеним випадкам використання. Вони є корисними час від часу, але швидше за все, більшість ваших компонентів не потребують жодного з них. **Ви можете побачити більшість наведених нижче методів на [цій діаграмі життєвого циклу](http://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/) якщо натиснете прапорець "Show less common lifecycles" зверху сторінки.**


### `shouldComponentUpdate()` {#shouldcomponentupdate}

```javascript
shouldComponentUpdate(nextProps, nextState)
```

Використовуйте `shouldComponentUpdate()`, щоб дати знати React, чи поточна зміна стану і пропсів не впливає на виведення компонента. Поведінка за замовчуванням полягає в повторному рендері при кожній зміні стану і в переважній більшості випадків ви маєте покладатись на поведінку за замовчуванням.

`shouldComponentUpdate()` викликається перед рендерингом при отриманні нових пропсів і стану. За замовчуванням має значення `true`. Цей метод не викликається при першому рендері чи коли використовується `forceUpdate()`.

Цей метод існує лише в якості **[оптимізації продуктивності](/docs/optimizing-performance.html).** Не покладайтесь на нього, щоб "запобігти" рендерингу, оскільки це може привести до помилок. **Розгляньте можливість використання вбудованого [`PureComponent`](/docs/react-api.html#reactpurecomponent)** замість написання власного `shouldComponentUpdate()`. `PureComponent` виконує поверхове порівняння пропсів та стану і зменшує шанс того, що ви пропустите необхідне оновлення.

Якщо ви впевнені, що ви хочете реалізувати його власноруч, ви можете порівняти `this.props` із `nextProps` та `this.state` із `nextState`, і повернути `false`, щоб сказати React, що це оновлення можна пропустити. Зверніть увагу на те, що повернення `false` не запобігає повторному рендерингу дочірніх компонентів, коли *їх* стан змінюється.

Ми не рекомендуємо робити глибокі порівняння або використовувати `JSON.stringify()` у `shouldComponentUpdate()`. Це надзвичайно неефективно і негативно вплине на продуктивність.

Наразі, якщо `shouldComponentUpdate()` повертає `false`, тоді [`UNSAFE_componentWillUpdate()`](#unsafe_componentwillupdate), [`render()`](#render), і [`componentDidUpdate()`](#componentdidupdate) не будуть викликані. У майбутньому React може розглядати `shouldComponentUpdate()` як пораду, а не строгу вимогу, і повернення `false` може спричинити повторний рендеринг компоненту, як зазвичай.

* * *

### `static getDerivedStateFromProps()` {#static-getderivedstatefromprops}

```js
static getDerivedStateFromProps(props, state)
```

`getDerivedStateFromProps` викликається безспосередньо перед викликом методу render, як при першому рендерингу, так і при всіх наступних оновленнях. Він має повернути об'єкт для оновлення стану або null, щоб не оновлювати нічого.

Цей метод існує для [малопоширених випадків](/blog/2018/06/07/you-probably-dont-need-derived-state.html#when-to-use-derived-state) коли стан залежить від змін в пропсах з часом. Наприклад, він може бути корисним для реалізації компоненту `<Transition>` котрий порівнює свої попередні і наступні дочірні елементи, щоб вирішити котрі з них потрібно анімувати для появи і зникнення.

Успадкування стану приводить до багатослівного коду і робить ваші компоненти важчими для розуміння.
[Переконайтеся, що ви знайомі з більш простими альтернативами:](/blog/2018/06/07/you-probably-dont-need-derived-state.html)

* Якщо вам потрібно **виконати побічний ефект** (наприклад, вибірку даних чи анімацію) у відповідь на зміну пропсів, використовуйте натомість метод [`componentDidUpdate`](#componentdidupdate).

* Якщо вам потрібно **повторно обрахувати якісь дані лише коли проп змінюється**, [використовуйте натомість допоміжний метод мемоізації](/blog/2018/06/07/you-probably-dont-need-derived-state.html#what-about-memoization).

* Якщо ви хочете **"скинути" деякий стан при зміні пропу**, подумайте про те, щоб натомість зробити компонент [повністю контрольованим](/blog/2018/06/07/you-probably-dont-need-derived-state.html#recommendation-fully-controlled-component) або [повністю неконтрольованим з `key`](/blog/2018/06/07/you-probably-dont-need-derived-state.html#recommendation-fully-uncontrolled-component-with-a-key).

Цей метод не має доступу до екземпляра компонента. Якщо ви бажаєте, ви можете повторно використовувати код між `getDerivedStateFromProps()` й іншими методами класу витягуючи чисті функції пропсів і стану компонента за межі класу.

Зверніть увагу, що цей метод викликається при *кожному* рендерингу, незалежно від причини. На відміну від `UNSAFE_componentWillReceiveProps`, котрий запускається лиш тоді, коли батьківський компонент викликає повторний рендеринг, а не як результат локального `setState`.

* * *

### `getSnapshotBeforeUpdate()` {#getsnapshotbeforeupdate}

```javascript
getSnapshotBeforeUpdate(prevProps, prevState)
```

`getSnapshotBeforeUpdate()` викликається безпосередньо перед  тим, як останній відрендерений вивід буде зафіксовано, наприклад в DOM. Він дозволяє вашому компоненту захопити деяку інформацію з DOM (наприклад, позицію прокрутки) перед її можливою зміною. Будь-яке значення повернуте цим методом життєвого циклу, буде передане як параметр в `componentDidUpdate()`.

Цей випадок не поширений, але він може бути в інтерфейсах користувача, таких як ланцюжок повідомлень в чаті, який має оброблювати позицію прокрутки особливим чином.

Значення знімку (або `null`) має бути повернуте.

Наприклад:

`embed:react-component-reference/get-snapshot-before-update.js`

У наведених вище прикладах є важливим прочитати `scrollHeight` властивість у `getSnapshotBeforeUpdate`, тому що можуть виникати затримки між "рендер" етапами життєвого циклу (таких як `render`) і етапами "фіксації" життєвого циклу (такими як `getSnapshotBeforeUpdate` і `componentDidUpdate`).

* * *

### Запобіжники {#error-boundaries}

[Запобіжники](/docs/error-boundaries.html) — це React-компоненти, котрі перехоплюють помилки JavaScript будь-де в їхньому дереві дочірніх компонентів, логують їх і відображають резервний інтерфейс користувача, замість невалідного дерева компонентів. Запобіжники перехоплюють помилки протягом рендерингу, в методах життєвого циклу і конструкторах всього дерева під ними.

Класовий компонент стає запобіжником, якщо він визначає один (або обидва) з методів життєвого циклу — `static getDerivedStateFromError()` чи `componentDidCatch()`. Оновлення стану з цих методів дозволить вам перехопити необроблену помилку JavaScript у дереві нижче і відобразити резервний інтерфейс користувача.

Використовуйте запобіжники тільки для відновлення від несподіваних виключних ситуацій; **не намагайтесь використовувати їх для управління потоком.**

Щоб дізнатися більше, перегляньте [*Обробка помилок у React 16*](/blog/2017/07/26/error-handling-in-react-16.html).

> Примітка
>
> Запобіжники перехоплюють лише помилки в компонентах у дереві **нижче** за них. Запобіжник не перехоплює помилки, що виникли в ньому.

### `static getDerivedStateFromError()` {#static-getderivedstatefromerror}
```javascript
static getDerivedStateFromError(error)
```

Цей метод життєвого циклу викликається після того, як компонент-нащадок згенерує помилку.
Як параметр він отримує помилку, що була згенерована і повинен повернути значення, щоб оновити стан.

```js{7-10,13-16}
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // Оновити стан, щоб наступний рендеринг показав резервний інтерфейс користувача.
    return { hasError: true };
  }

  render() {
    if (this.state.hasError) {
      // Ви можете рендерити будь-який власний резервний інтерфейс користувача
      return <h1>Щось пішло не так.</h1>;
    }

    return this.props.children;
  }
}
```

> Примітка
>
> `getDerivedStateFromError()` викликається на "render" етапі, а отже побічні ефекти не допускаються.
Для таких випадків використовуйте `componentDidCatch()`.

* * *

### `componentDidCatch()` {#componentdidcatch}

```javascript
componentDidCatch(error, info)
```

Цей метод життєвого циклу викликається після того, як компонент-нащадок згенерує помилку.
Ві отримує два параметри:

1. `error` — Помилка, яка була згенерована.
2. `info` — Об'єкт з ключем `componentStack`, який містить [інформацію про компонент, який згенерував помилку](/docs/error-boundaries.html#component-stack-traces).


`componentDidCatch()` викликається на етапі "фіксації", а отже побічні ефекти допустимі.
Він має використовуватись для таких речей, як логування помилок:

```js{12-19}
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // Оновити стан, щоб наступний рендеринг показав резервний інтерфейс користувача.
    return { hasError: true };
  }

  componentDidCatch(error, info) {
    // Приклад "componentStack":
    //   in ComponentThatThrows (created by App)
    //   in ErrorBoundary (created by App)
    //   in div (created by App)
    //   in App
    logComponentStackToMyService(info.componentStack);
  }

  render() {
    if (this.state.hasError) {
      // Ви можете рендерити будь-який власний резервний інтерфейс користувача
      return <h1>Щось пішло не так.</h1>;
    }

    return this.props.children;
  }
}
```

> Примітка
>
> При виникненні помилки, ви можете рендерити резервний інтерфейс користувача `componentDidCatch()` викликом `setState`, але така поведінка буде вважатися застарілою в наступному релізі.
> Натомість використовуйте `static getDerivedStateFromError()` для обробки резервного рендерингу.

* * *

### Застарілі методи життєвого циклу {#legacy-lifecycle-methods}

Нижченаведені методи життєвого циклу є "застарілими". Вони досі працюють, але ми не радимо використовувати їх у новому коді. Ви можете дізнатися більше про перехід від застарілих методів життєвого циклу в [цій статті](/blog/2018/03/27/update-on-async-rendering.html).

### `UNSAFE_componentWillMount()` {#unsafe_componentwillmount}

```javascript
UNSAFE_componentWillMount()
```

> Примітка
>
> Цей метод раніше називався `componentWillMount`. Попереднє ім'я продовжить працювати до версії 17. Використовуйте [`rename-unsafe-lifecycles` codemod](https://github.com/reactjs/react-codemod#rename-unsafe-lifecycles), щоб автоматично оновити ваші компоненти.

`UNSAFE_componentWillMount()` викликаєтся безпосередньо перед монтуванням. Він викликається перед `render()`, а тому синхронний виклик `setState()` в цьому методі не викличе повторний рендеринг. У загальному випадку, ми радимо використовувати `constructor()` для ініціалізації стану.

Уникайте додавання будь-яких побічних ефектів чи підписок в цьому методі. Для таких випадків використовуйте `componentDidMount()`.

Це єдиний метод життєвого циклу, що викликається серверним рендерингом.

* * *

### `UNSAFE_componentWillReceiveProps()` {#unsafe_componentwillreceiveprops}

```javascript
UNSAFE_componentWillReceiveProps(nextProps)
```

> Примітка
>
> Цей метод раніше називався `componentWillReceiveProps`. Попереднє ім'я продовжить працювати до версії 17. Використовуйте [`rename-unsafe-lifecycles` codemod](https://github.сom/reactjs/react-codemod#rename-unsafe-lifecycles), щоб автоматично оновити ваші компоненти.

> Примітка:
>
> Використання цього методу часто приводить до помилок і невідповідностей.
>
> * Якщо вам потрібно **виконати побічний ефект** (наприклад, вибірку даних чи анімацію) у відповідь на зміну пропсів, замість нього використовуйте [`componentDidUpdate`](#componentdidupdate).
> * Якщо ви використовували `componentWillReceiveProps` для **повторного обрахування деяких даних лише при зміні пропу**, [використовуйте натомість допоміжний метод мемоізації](/blog/2018/06/07/you-probably-dont-need-derived-state.html#what-about-memoization).
> * Якщо ви використовували `componentWillReceiveProps` для **"скидання" деякого стану при зміні пропу**, подумайте про те, щоб натомість зробити компонент [повністю контрольованим](/blog/2018/06/07/you-probably-dont-need-derived-state.html#recommendation-fully-controlled-component) або [повністю неконтрольованим з `key`](/blog/2018/06/07/you-probably-dont-need-derived-state.html#recommendation-fully-uncontrolled-component-with-a-key).
>
> Для інших випадків, [перегляньте поради в цій статті про похідний стан](/blog/2018/06/07/you-probably-dont-need-derived-state.html).

`UNSAFE_componentWillReceiveProps()` викликається перед тим, як примонтований компонент отримує нові пропси. Якщо вам потрібно оновити стан у відповідь на зміну пропсів (наприклад, щоб скинути його), ви можете порівняти `this.props` і `nextProps` в цьому методі та виконати переходи стану, використовуючи `this.setState()`.

Зверніть увагу, що якщо батьківський компонент спричиняє повторний рендер, то цей метод буде викликаний, навіть якщо пропси не змінились. Переконайтеся, що ви порівнюєте поточні і наступні значення тільки тоді, коли ви хочете обробити зміни.

React не викликає `UNSAFE_componentWillReceiveProps()` для початкових пропсів під час [монтування](#mounting). Він викликається лише тоді, коли деякі пропси компонента можуть оновитись. Виклик `this.setState()`, як правило, не викликає `UNSAFE_componentWillReceiveProps()`.

* * *

### `UNSAFE_componentWillUpdate()` {#unsafe_componentwillupdate}

```javascript
UNSAFE_componentWillUpdate(nextProps, nextState)
```

> Примітка
>
> Цей метод раніше називався `componentWillUpdate`. Попереднє ім'я продовжить працювати до версії 17. Використовуйте [`rename-unsafe-lifecycles` codemod](https://github.сom/reactjs/react-codemod#rename-unsafe-lifecycles), щоб автоматично оновити ваші компоненти.

`UNSAFE_componentWillUpdate()` викликається безпосередньо перед рендерингом, коли компонент отримує нові пропси чи стан. Використовуйте це, як можливість для виконання підготовки перед оновленням. Цей метод не викликається при першому рендері.

Зверніть увагу, що ви не можете викликати `this.setState()` тут; ви також не повинні робити будь-що (наприклад, відправляти дію Redux), що спричинить оновлення React-компоненту перед поверненням з `UNSAFE_componentWillUpdate()`.

Як правило, цей метод можна замінити на `componentDidUpdate()`. Якщо ви зчитуєте з DOM в цьому методі (наприклад, для збереження позиції прокрутки), ви можете перемістити цю логіку в `getSnapshotBeforeUpdate()`.

> Примітка
>
> `UNSAFE_componentWillUpdate()` не викликається, якщо [`shouldComponentUpdate()`](#shouldcomponentupdate) повертає false.

* * *

## Інші API {#other-apis-1}

На відміну від вищенаведених методів життєвого циклу (які React викликає для вас), *ви* можете викликати методи нижче з ваших компонентів.

Їх всього два: `setState()` і `forceUpdate()`.

### `setState()` {#setstate}

```javascript
setState(updater[, callback])
```

`setState()` ставить в чергу оновлення стану компонента і повідомляє React, що цей компонент і його нащадки мають бути повторно відрендерені з оновленим станом. Це основний метод, який ви використовуєте для оновлення інтерфейсу користувача у відповідь на обробники подій і відповіді сервера.

Думайте про `setState()` як про *запит*, а не як про команду, що має бути негайно виконана для оновлення компонента. Для кращої наочної продуктивності, React може відкласти виклик і тоді оновити кілька компонентів за один прохід. React не гарантує негайного застосування змін стану.

`setState()` не завжди відразу оновлює компонент. Цей метод може групувати чи відкладати оновлення на потім. Це робить зчитування `this.state` відразу після виклику `setState()` потенційною пасткою. Натомість, використовуйте `componentDidUpdate` чи функцію зворотнього виклику `setState` (`setState(updater, callback)`), обидва підходи гарантовано запустяться після застосування оновлення. Якщо вам потрібно оновити стан на основі поперенього стану, прочитайте про аргумент `updater` нижче.

`setState()` завжди призводить до повторного рендерингу за умови, що `shouldComponentUpdate()` не повертає `false`. Якщо використовуються змінні об'єкти і логіка умовного рендерингу не може бути реалізована у `shouldComponentUpdate()`, виклик `setState()` тільки тоді, коли новий стан відрізняється від попереднього, може запобігти непотрібних повторних рендерингів.

Перший аргумент — це функція `updater` визначена як:

```javascript
(state, props) => stateChange
```

`state` — це посилання на стан компонента в момент часу, коли зміна застосовується. Воно не має змінюватись напряму. Замість цього, зміни повинні бути представлені шляхом створення нового об'єкту на основі вхідних даних із `state` та `props`. Наприклад, ми хочемо збільшити значення в стані на `props.step`:

```javascript
this.setState((state, props) => {
  return {counter: state.counter + props.step};
});
```

Як `state`, так і `props`, отримані функцією оновлення, гарантовано будуть в актуальному стані. Результат функції оновлення буде поверхово об'єднаний із `state`.

Другий параметр `setState()` — це необов'язкова функція зворотнього виклику, яка буде виконана після того, як `setState` завершив роботу і компонент повторно відрендерився. Зазвичай ми радимо використовувати `componentDidUpdate()` для подібної логіки.

Ви можете передати в `setState()` першим аргументом об'єкт, а не функцію:

```javascript
setState(stateChange[, callback])
```

Це виконає поверхове об'єднання `stateChange` в новий стан, наприклад, для зміни кількості товарів у корзині:

```javascript
this.setState({quantity: 2})
```

Ця форма запису `setState()` також асинхронна і кілька викликів впродовж одного циклу можуть бути згруповані в один. Наприклад, якщо ви спробуєте інкрементувати кількість елементів більше, ніж один раз в одному циклі, результат буде еквівалентний:

```javaScript
Object.assign(
  previousState,
  {quantity: state.quantity + 1},
  {quantity: state.quantity + 1},
  ...
)
```

Наступні виклики перезапишуть попередні значення в цьому циклі і кількість буде інкрементована лише раз. Якщо наступний стан залежить від поточного, ми радимо використовувати форму з функцією оновлення:

```js
this.setState((state) => {
  return {quantity: state.quantity + 1};
});
```

Для більш детальної інформації перегляньте:

* [Стан та життєвий цикл](/docs/state-and-lifecycle.html)
* [В подробицях: Коли і чому виклики `setState()` групуються?](https://stackoverflow.com/a/48610973/458193)
* [В подробицях: Чому `this.state` не оновлюється негайно?](https://github.com/facebook/react/issues/11527#issuecomment-360199710)

* * *

### `forceUpdate()` {#forceupdate}

```javascript
component.forceUpdate(callback)
```

За замовчуванням, коли стан чи пропси вашого компонента змінюються, він буде повторно відрендерений. Якщо ваш метод `render()` залежить від деяких інших даних, за допомогою виклику `forceUpdate()` ви можете вказати React, що компонент потребує повторного рендерингу.

Виклик `forceUpdate()` спричинить виклик `render()` у компоненті з пропуском `shouldComponentUpdate()`. Це викличе звичайні методи життєвого циклу в дочірніх компонентах, включно з `shouldComponentUpdate()` для кожного нащадка. React, як і раніше, оновить DOM лише у випадку зміни розмітки.


Зазвичай краще уникати всіх використань `forceUpdate()` і тільки зчитувати `this.props` і `this.state` в `render()`.

* * *

## Властивості класу {#class-properties-1}

### `defaultProps` {#defaultprops}

`defaultProps` можна визначити як властивість класу компонента, щоб встановити пропси за замовчуванням в класі. Це використовується для невизначених пропсів, але не для пропсів зі значенням null. Наприклад:

```js
class CustomButton extends React.Component {
  // ...
}

CustomButton.defaultProps = {
  color: 'синій'
};
```

Якщо `props.color` не наданий, за замовчуванням буде встановлено значення `'синій'`:

```js
  render() {
    return <CustomButton /> ; // props.color буде встановлено в синій
  }
```

Якщо для `props.color` встановлено значення null, то воно залишиться null:

```js
  render() {
    return <CustomButton color={null} /> ; // props.color залишиться null
  }
```

* * *

### `displayName` {#displayname}

Рядок `displayName` використовується для повідомлень при налагодженні. Зазвичай, ви не повинні вказувати його явно, тому що за замовчуванням він є іменем функції чи класу, що визначає компонент. Можливо, ви захочете встановити його явно, якщо вам з метою налагодження потрібно відобразити інше ім'я чи коли ви створюєте компонент вищого порядку, перегляньте [Обгортання відображуваного імені для легкого налагоджування](/docs/higher-order-components.html#convention-wrap-the-display-name-for-easy-debugging), щоб дізнатися більше.

* * *

## Властивості екземпляру {#instance-properties-1}

### `props` {#props}

`this.props` містить пропси, що були визначені в момент виклику компонента. Перегляньте [Компоненти і пропси](/docs/components-and-props.html) для ознайомлення з пропсами.

Зокрема, `this.props.children` — це спеціальний проп, зазвичай визначений дочірніми тегами в JSX виразі, а не в самому тегові.

### `state` {#state}

Стан містить дані, конкретні для цього компонента і які можуть змінюватися з часом. Стан визначається користувачем і має бути простим JavaScript-об'єктом.

Якщо певне значення не використовується для рендерингу чи потоку даних (наприклад, ідентифікатор таймера), вам не потрібно вставляти його в стан. Такі значення можуть бути визначені як поля екземпляру компонента.

Перегляньте [Стан і життєвий цикл](/docs/state-and-lifecycle.html), щоб дізнатися більше про стан.

Ніколи напряму не змінюйте `this.state`, так як подальший виклик `setState()` може перезаписати ваші зміни. Поводьтеся з `this.state` так, ніби він є незмінним.
