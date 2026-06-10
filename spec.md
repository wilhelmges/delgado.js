# DelgadoJS Framework Specification v1.0

## 1. Призначення

DelgadoJS — легкий реактивний JavaScript-фреймворк для серверно-рендерених веб-додатків.

Мета DelgadoJS:

* простота Alpine.js;
* модульність Vue;
* відсутність збірки;
* відсутність Virtual DOM;
* жорстке розділення HTML та JavaScript.

DelgadoJS призначений для:

* FastAPI
* Flask
* Django
* Laravel
* ASP.NET MVC
* PHP
* статичних сайтів

та інших проєктів, де HTML генерується на сервері.

---

# 2. Основні принципи

## 2.1 HTML належить HTML

DelgadoJS працює поверх існуючого DOM.

Не підтримуються:

* Virtual DOM
* JSX
* render-функції
* шаблони всередині JavaScript

HTML завжди знаходиться у html-файлах.

---

## 2.2 JavaScript належить JavaScript

У шаблонах заборонено виконання довільного JavaScript-коду.

Не допускається:

```html
<span x-text="count + 1"></span>

<div x-show="users.length > 0"></div>

<button @click="save(user.id)"></button>
```

Уся логіка виконується всередині компонентів.

---

## 2.3 Один компонент = один модуль

Кожен компонент визначається окремим JavaScript-модулем.

---

## 2.4 Нульова конфігурація

DelgadoJS не потребує:

* npm
* webpack
* vite
* babel
* transpilation

---

# 3. Підключення

```html
<script type="module" src="/js/delgado.js"></script>
```

або

```html
<script type="module">
import Delgado from "/js/delgado.js";

Delgado.start();
</script>
```

---

# 4. Структура проєкту

За замовчуванням:

```text
/components
    counter.js

    /admin
        users.js
        roles.js
```

---

# 5. Компоненти

HTML:

```html
<div x-component="counter">

    <button @click="increment">+</button>

    <span x-text="count"></span>

</div>
```

Автоматично завантажується:

```text
/components/counter.js
```

---

# 6. Вкладені папки

HTML:

```html
<div x-component="admin/users">
```

Відповідає:

```text
/components/admin/users.js
```

Глибина вкладеності не обмежується.

---

# 7. Формат компонента

```javascript
export default {

    state: {
        count: 0
    },

    increment(event) {

        this.state.count++;

    }

}
```

---

# 8. Екземпляри компонентів

Кожен елемент:

```html
<div x-component="counter">
```

створює окремий екземпляр компонента.

Стани між екземплярами не розділяються.

---

# 9. API компонента

Усередині компонента доступні:

```javascript
this.state
this.props
this.refs
this.el
```

---

## this.state

Реактивний стан компонента.

---

## this.props

Об'єкт, сформований із data-атрибутів кореневого елемента.

---

## this.refs

Колекція x-ref.

---

## this.el

Кореневий DOM-елемент компонента.

---

# 10. State

State є реактивним.

---

## 10.1 Shallow Reactivity

DelgadoJS відстежує лише зміни властивостей верхнього рівня.

Працює:

```javascript
this.state.count++;

this.state.user = {
    ...this.state.user,
    name: "John"
};
```

Не гарантується:

```javascript
this.state.user.name = "John";

this.state.users[0].name = "John";
```

Для оновлення DOM необхідно змінити властивість верхнього рівня.

---

# 11. Getters

Компонент може містити getters.

```javascript
export default {

    state: {
        count: 5
    },

    get nextCount() {

        return this.state.count + 1;

    }

}
```

---

# 12. Життєвий цикл

## init()

Викликається після створення компонента.

```javascript
init() {

}
```

---

## destroy()

Викликається перед видаленням кореневого елемента компонента.

```javascript
destroy() {

}
```

---

# 13. Async Init

Допускається:

```javascript
async init() {

}
```

DelgadoJS не очікує завершення init().

Початковий рендеринг виконується негайно.

---

# 14. Props

Props формуються з data-атрибутів кореневого елемента.

HTML:

```html
<div
    x-component="user-card"
    data-user-id="15"
    data-role="admin">
</div>
```

У компоненті:

```javascript
this.props.userId

this.props.role
```

Імена автоматично конвертуються:

```text
data-user-id → userId
data-company-name → companyName
```

---

# 15. Директива x-text

Оновлює textContent.

```html
<span x-text="count"></span>
```

---

# 16. Директива x-show

Керує видимістю елемента.

```html
<div x-show="isVisible">
```

При значенні false:

```css
display:none;
```

---

# 17. Директива x-model

Двосторонній зв'язок між DOM та state.

```html
<input x-model="username">
```

```javascript
state: {
    username: ""
}
```

---

# 18. Директива x-ref

Створює посилання на DOM-вузол.

```html
<input x-ref="fileInput">
```

```javascript
this.refs.fileInput
```

---

# 19. Розв'язання властивостей

Усі директиви працюють лише з:

1. state
2. getters

Пошук виконується саме в такому порядку.

---

Працює:

```html
<span x-text="count"></span>
```

```javascript
state.count
```

або

```javascript
get count()
```

---

Не допускається доступ до:

```javascript
this.props
methods
this.refs
this.el
```

безпосередньо з шаблону.

---

# 20. Події

Підтримуються:

```text
@click
@input
@change
@submit
```

---

# 21. Виклик методів

Дозволено лише ім'я методу.

```html
<button @click="save">
```

---

Не допускається:

```html
@click="save()"

@click="save(user)"

@click="save(1)"
```

---

# 22. Обробники подій

DelgadoJS передає лише DOM Event.

```javascript
save(event) {

}
```

---

# 23. x-for

Використовується для рендерингу списків.

```html
<template x-for="users">

</template>
```

Аргументом може бути:

* state-масив
* getter, який повертає масив

---

# 24. Спеціальні змінні циклу

Усередині x-for доступні:

```text
$item
$index
```

---

Приклад:

```html
<template x-for="users">

    <span x-text="$item.name"></span>

</template>
```

---

# 25. Події всередині x-for

DelgadoJS не передає автоматично:

* $item
* $index
* context

в обробники подій.

---

Для передачі даних використовуються стандартні HTML-атрибути.

```html
<template x-for="users">

    <button
        data-id="$item.id"
        @click="editUser">

        Edit

    </button>

</template>
```

```javascript
editUser(event) {

    const id =
        event.target.dataset.id;

}
```

---

# 26. Реактивність списків

Працює:

```javascript
this.state.users = users;

this.state.users.push(user);
```

DOM автоматично оновлюється.

---

# 27. Оновлення списків

При будь-якій зміні джерела даних DelgadoJS повністю перебудовує список.

Diffing не використовується.

---

# 28. Обмеження x-for

Не підтримується:

```html
<template x-for="users.filter(...)">
```

```html
<template x-for="items.sort(...)">
```

```html
<template x-for="(item,index) in users">
```

---

# 29. Конфігурація

Необов'язкова.

```javascript
Delgado.configure({

    componentsPath: "/components"

});
```

---

# 30. Помилки завантаження

Якщо компонент:

* не знайдено;
* містить синтаксичну помилку;
* не може бути імпортований;

DelgadoJS виводить помилку в console.error та пропускає ініціалізацію компонента.

---

# 31. Мінімальний приклад

HTML:

```html
<div x-component="counter">

    <button @click="increment">

        +

    </button>

    <span x-text="count"></span>

</div>
```

Файл:

```text
/components/counter.js
```

Код:

```javascript
export default {

    state: {
        count: 0
    },

    increment(event) {

        this.state.count++;

    }

}
```

---

# 32. Не входить до DelgadoJS v1

Не підтримуються:

* Virtual DOM
* JSX
* шаблони в JavaScript
* x-html
* x-if
* глобальний store
* router
* SSR hydration
* key у списках
* параметри в директивах
* довільні JavaScript-вирази в HTML
* diffing
* watchers
* computed dependency tracking

---

# 33. Головне правило DelgadoJS

HTML використовується лише для опису структури інтерфейсу.

Уся бізнес-логіка, обчислення, фільтрація, сортування та робота з даними виконуються виключно всередині JavaScript-компонентів.
