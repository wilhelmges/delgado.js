# DelgadoJS Framework Specification v1.0

## 1. Призначення

DelgadoJS — легкий фронтенд-фреймворк для серверно-рендерених сайтів.

Основна ціль — надати простоту Alpine.js при збереженні повного відокремлення HTML і JavaScript.

Фреймворк орієнтований на:

* FastAPI
* Flask
* Django
* Laravel
* ASP.NET MVC
* PHP
* статичні сайти

та інші проєкти, де HTML генерується на сервері.

---

# 2. Основні принципи

## 2.1 HTML належить HTML

Фреймворк працює поверх існуючої розмітки.

Не підтримуються:

```javascript
template()
render()
virtual DOM
```

Компоненти не генерують HTML.

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

Вся логіка повинна знаходитися в компоненті.

Дозволяється лише доступ до:

* state
* getters
* methods
* спеціальних змінних циклу

---

## 2.3 Один компонент = один модуль

Кожен компонент визначається окремим JavaScript-файлом.

---

## 2.4 Нульова конфігурація

Фреймворк не потребує:

* npm
* webpack
* vite
* babel
* transpilation

---

# 3. Підключення

```html
<script type="module" src="/js/minijs.js"></script>
```

або

```html
<script type="module">
import Mini from "/js/minijs.js";

Mini.start();
</script>
```

---

# 4. Структура компонентів

За замовчуванням:

```text
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

```html
<div x-component="counter">

    <button @click="increment">+</button>

    <span x-text="count"></span>

</div>
```

Framework автоматично завантажує:

```text
/components/counter.js
```

---

# 6. Вкладені папки

HTML:

```html
<div x-component="admin/users">
```

Framework автоматично завантажує:

```text
/components/admin/users.js
```

Підпапки підтримуються без обмежень.

---

# 7. Формат компонента

```javascript
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

Кожен елемент з атрибутом:

```html
<div x-component="counter">
```

створює окремий екземпляр.

```html
<div x-component="counter"></div>

<div x-component="counter"></div>
```

створюють два незалежні стани.

---

# 9. State

State є реактивним.

```javascript
this.state.count++;
```

автоматично оновлює всі пов'язані елементи.

---

# 10. Getters

Компонент може визначати getters.

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

HTML:

```html
<span x-text="nextCount"></span>
```

---

# 11. Методи

Методи можуть змінювати state.

```javascript
save() {

}

toggle() {

}
```

Методи можуть бути async.

```javascript
async loadUsers() {

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

Викликається перед знищенням компонента.

```javascript
destroy() {

}
```

---

# 13. Директива x-text

Встановлює textContent.

HTML:

```html
<span x-text="count"></span>
```

Дозволено:

```html
<span x-text="count"></span>

<span x-text="nextCount"></span>
```

Не дозволено:

```html
<span x-text="count + 1"></span>
```

---

# 14. Директива x-show

Керує видимістю елемента.

HTML:

```html
<div x-show="isVisible">
```

Якщо значення false:

```css
display:none;
```

---

# 15. Директива x-model

Двосторонній зв'язок.

HTML:

```html
<input x-model="username">
```

Компонент:

```javascript
state: {
    username: ""
}
```

Зміна будь-якої сторони автоматично оновлює іншу.

---

# 16. Директива x-ref

Створює посилання на DOM-вузол.

HTML:

```html
<input x-ref="fileInput">
```

Компонент:

```javascript
this.refs.fileInput
```

---

# 17. Події

## Синтаксис

```html
@click
@input
@change
@submit
```

Приклад:

```html
<button @click="save">
```

---

# 18. Виклик методів

HTML:

```html
<button @click="save">
```

Компонент:

```javascript
save() {

}
```

---

# 19. Заборонені вирази

Не допускається:

```html
@click="save(user)"
@click="save(1)"
@click="save(id)"
```

Допускається лише ім'я методу:

```html
@click="save"
```

---

# 20. Директива x-for

Використовується для рендерингу списків.

Синтаксис:

```html
<template x-for="users">

    ...

</template>
```

Аргументом може бути лише:

* state-масив
* getter, який повертає масив

---

# 21. x-for через state

Компонент:

```javascript
state: {
    users: []
}
```

HTML:

```html
<template x-for="users">

    <div>

        <span x-text="$item.name"></span>

    </div>

</template>
```

---

# 22. x-for через getter

Компонент:

```javascript
get activeUsers() {

    return this.state.users.filter(
        x => x.active
    );

}
```

HTML:

```html
<template x-for="activeUsers">

</template>
```

---

# 23. Спеціальні змінні циклу

Всередині x-for доступні:

```text
$item
$index
```

---

# 24. Доступ до властивостей елемента

Дозволяється:

```html
<span x-text="$item.name"></span>

<span x-text="$item.email"></span>

<span x-text="$item.address.city"></span>
```

Підтримується доступ через крапку.

---

# 25. Події всередині циклу

HTML:

```html
<template x-for="users">

    <button @click="editUser">

        Edit

    </button>

</template>
```

Компонент:

```javascript
editUser(user) {

}
```

Framework автоматично передає:

```javascript
editUser($item)
```

---

# 26. Реактивність списків

Після зміни:

```javascript
this.state.users = newUsers;
```

або

```javascript
this.state.users.push(user);
```

список оновлюється автоматично.

---

# 27. Обмеження x-for

Не підтримується:

```html
<template x-for="users.filter(...)">
```

Не підтримується:

```html
<template x-for="items.sort(...)">
```

Не підтримується:

```html
<template x-for="(item,index) in users">
```

---

# 28. Конфігурація

Необов'язкова.

```javascript
Mini.configure({

    componentsPath: "/components"

});
```

---

# 29. Мінімальний приклад

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

    increment() {

        this.state.count++;

    }

}
```

---

# 30. Не входить до v1

Не підтримується:

* Virtual DOM
* JSX
* Templates у JavaScript
* x-html
* x-if
* глобальний store
* router
* SSR hydration
* key у списках
* параметри у директивах
* довільні JavaScript-вирази в HTML

---

# 31. Головне правило фреймворку

HTML використовується лише для опису структури інтерфейсу.

Уся бізнес-логіка, обчислення, фільтрація, сортування та робота з даними виконуються виключно всередині JavaScript-компонентів.

Після цього документа вже можна переходити до окремої технічної специфікації ядра: механізму реактивності, завантажувача компонентів, парсера директив і життєвого циклу компонентів. Це буде вже документ для реалізації самого фреймворку, а не для його користувачів.
