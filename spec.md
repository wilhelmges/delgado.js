# DelgadoJS Framework Specification v1.0

## 1. Призначення

DelgadoJS — легкий реактивний JavaScript-фреймворк для серверно-рендерених сайтів.

Мета DelgadoJS — поєднати:

* простоту Alpine.js;
* модульність Vue;
* відсутність збірки;
* відокремлення HTML від JavaScript.

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

```html id="kq3p6r"
<span x-text="count + 1"></span>

<div x-show="users.length > 0"></div>

<button @click="save(user.id)"></button>
```

Вся логіка повинна знаходитися в компоненті.

---

## 2.3 Один компонент = один модуль

Кожен компонент визначається окремим JavaScript-файлом.

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

```html id="6chgg5"
<script type="module" src="/js/delgado.js"></script>
```

або

```html id="kvchwo"
<script type="module">
import Delgado from "/js/delgado.js";

Delgado.start();
</script>
```

---

# 4. Структура проєкту

За замовчуванням:

```text id="g2zh1r"
/components
    counter.js
    upload.js

    /admin
        users.js
        roles.js
```

---

# 5. Компоненти

HTML:

```html id="lz5kgj"
<div x-component="counter">

    <button @click="increment">+</button>

    <span x-text="count"></span>

</div>
```

DelgadoJS автоматично завантажує:

```text id="55fkjg"
/components/counter.js
```

---

# 6. Вкладені папки

HTML:

```html id="2uc4f3"
<div x-component="admin/users">
```

DelgadoJS автоматично завантажує:

```text id="e8wl08"
/components/admin/users.js
```

Глибина вкладеності не обмежується.

---

# 7. Формат компонента

```javascript id="09m4yu"
export default {

    state: {
        count: 0
    },

    increment() {
        this.state.count++;
    }

}
```

---

# 8. Екземпляри компонентів

Кожен елемент із директивою:

```html id="l5txmq"
<div x-component="counter">
```

створює окремий екземпляр компонента.

```html id="5a8a4h"
<div x-component="counter"></div>

<div x-component="counter"></div>
```

мають незалежні стани.

---

# 9. State

State є реактивним.

Будь-яка зміна:

```javascript id="x6u75d"
this.state.count++;
```

або

```javascript id="tcn9cq"
this.state.count = 10;
```

автоматично оновлює пов'язані елементи DOM.

---

# 10. Getters

Компоненти можуть містити getters.

```javascript id="6hfpxo"
export default {

    state: {
        count: 5
    },

    get nextCount() {
        return this.state.count + 1;
    }

}
```

HTML:

```html id="glj4ls"
<span x-text="nextCount"></span>
```

---

# 11. Методи

Компонент може містити будь-які методи.

```javascript id="7zn4k0"
save() {

}

toggle() {

}
```

Підтримуються async-методи.

---

# 12. Життєвий цикл

## init()

Викликається після створення компонента.

```javascript id="p9f5s0"
init() {

}
```

---

## destroy()

Викликається перед знищенням компонента.

```javascript id="s90qun"
destroy() {

}
```

---

# 13. Директива x-text

Оновлює textContent.

```html id="0wsh1m"
<span x-text="count"></span>
```

Дозволено:

```html id="8wo6v3"
<span x-text="count"></span>

<span x-text="nextCount"></span>
```

Не дозволено:

```html id="y10j80"
<span x-text="count + 1"></span>
```

---

# 14. Директива x-show

Керує видимістю елемента.

```html id="w6eekj"
<div x-show="isVisible">
```

При значенні false:

```css id="em5d87"
display:none;
```

---

# 15. Директива x-model

Двосторонній зв'язок між DOM та state.

```html id="jlsqpc"
<input x-model="username">
```

```javascript id="2ljvdk"
state: {
    username: ""
}
```

---

# 16. Директива x-ref

Створює посилання на DOM-вузол.

```html id="m34v42"
<input x-ref="fileInput">
```

У компоненті:

```javascript id="gmpa57"
this.refs.fileInput
```

---

# 17. Події

Підтримуються:

```text id="cm6gnk"
@click
@input
@change
@submit
```

Приклад:

```html id="9mqrlk"
<button @click="save">
```

---

# 18. Виклик методів

Допускається лише ім'я методу.

```html id="n54d90"
<button @click="save">
```

---

# 19. Заборонені вирази

Не допускається:

```html id="ndogkv"
@click="save(user)"
@click="save(id)"
@click="save(1)"
```

---

# 20. Директива x-for

Використовується для рендерингу списків.

Синтаксис:

```html id="ly3mbm"
<template x-for="users">

</template>
```

Аргументом може бути:

* state-масив
* getter, який повертає масив

---

# 21. Рендеринг списку

```javascript id="qdfz18"
state: {
    users: []
}
```

```html id="mbu0i0"
<template x-for="users">

    <div>

        <span x-text="$item.name"></span>

    </div>

</template>
```

---

# 22. Getters для списків

```javascript id="u8h3w0"
get activeUsers() {

    return this.state.users.filter(
        x => x.active
    );

}
```

```html id="mnptgt"
<template x-for="activeUsers">

</template>
```

---

# 23. Спеціальні змінні циклу

Усередині x-for доступні:

```text id="lr2u5z"
$item
$index
```

---

# 24. Доступ до властивостей елемента

Підтримуються вкладені властивості.

```html id="jk42j5"
<span x-text="$item.name"></span>

<span x-text="$item.email"></span>

<span x-text="$item.address.city"></span>
```

---

# 25. Події всередині списків

HTML:

```html id="j6u7bh"
<template x-for="users">

    <button @click="editUser">

        Edit

    </button>

</template>
```

Компонент:

```javascript id="0eqc68"
editUser(user) {

}
```

DelgadoJS автоматично викликає:

```javascript id="9vb01r"
editUser($item)
```

---

# 26. Реактивність списків

Після змін:

```javascript id="5jk5dm"
this.state.users = newUsers;
```

або

```javascript id="1t0z2m"
this.state.users.push(user);
```

DOM автоматично оновлюється.

---

# 27. Обмеження x-for

Не підтримується:

```html id="tdxmdw"
<template x-for="users.filter(...)">
```

Не підтримується:

```html id="25whtr"
<template x-for="items.sort(...)">
```

Не підтримується:

```html id="jlwmk7"
<template x-for="(item,index) in users">
```

---

# 28. Конфігурація

Необов'язкова.

```javascript id="96b8qv"
Delgado.configure({

    componentsPath: "/components"

});
```

---

# 29. Мінімальний приклад

HTML:

```html id="spmbje"
<div x-component="counter">

    <button @click="increment">

        +

    </button>

    <span x-text="count"></span>

</div>
```

Файл:

```text id="1z8wbm"
/components/counter.js
```

Код:

```javascript id="q9s0v6"
export default {

    state: {
        count: 0
    },

    increment() {

        this.state.count++;

    }

}
```

---

# 30. Не входить до DelgadoJS v1

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

---

# 31. Головне правило DelgadoJS

HTML використовується лише для опису структури інтерфейсу.
Уся бізнес-логіка, обчислення, фільтрація, сортування та робота з даними виконуються виключно всередині JavaScript-компонентів.
інші рішення ти можеш взяти з урахуванням того, що фреймворк має містити мінімум магії і неочевидності. якщо треба щось додаткове - то користувач має вказати це явно. плюс бажана розділення логіки і представлення

