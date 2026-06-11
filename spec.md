# DelgadoJS Specification v1.0

## Purpose

DelgadoJS is a minimal server-rendered web framework designed around **AI-First architecture**.

The framework prioritizes:

* explicit behavior
* deterministic execution
* minimal API surface
* zero hidden conventions
* easy understanding by both humans and AI agents

---

# Core Principles

## Principle 1: No Hidden Behavior

Any framework behavior must be discoverable through:

* source code search
* framework specification

Developers and AI agents should never be required to guess behavior.

---

## Principle 2: Explicit Over Implicit

DelgadoJS always prefers:

```text
Explicit registration
Explicit state updates
Explicit component lifecycle
```

over:

```text
Auto-discovery
Magic reactivity
Hidden conventions
```

---

## Principle 3: Single Way To Do Things

Each framework feature should have exactly one official implementation pattern.

Multiple equivalent APIs are discouraged.

---

# Component System

## Component Registration

Components must be registered explicitly.

### Allowed

```js
import UserCard from "./components/user-card.js";

Delgado.component("user-card", UserCard);
```

or

```js
createApp({
    components: {
        "user-card": UserCard
    }
});
```

### Forbidden

Automatic component discovery.

Examples:

```text
/components/user-card.js
/components/UserCard.js
/components/**/*.js
```

must never be scanned automatically.

---

## Component Usage

```html
<div x-component="user-card"></div>
```

Resolution rule:

```text
x-component resolves only registered components.
```

No filesystem lookup is performed.

---

# Component Contract

A component must export a plain object.

Example:

```js
export default {
    state: {
        count: 0
    },

    methods: {
        increment(ctx) {}
    },

    mounted(ctx) {},

    unmounted(ctx) {}
};
```

---

# Component Context

All component methods receive a context object.

Example:

```js
increment(ctx) {
    ctx.setState({
        count: ctx.state.count + 1
    });
}
```

---

## Context API

Available properties:

```js
ctx.state
ctx.props
ctx.refs
ctx.el
ctx.setState()
```

---

## Forbidden Context Access

```js
this.state
this.props
this.refs
this.el
```

The framework must not rely on `this`.

---

# State Management

## State Ownership

Each component owns its state.

Example:

```js
state: {
    count: 0
}
```

---

## State Immutability Rule

Direct state mutation is forbidden.

### Forbidden

```js
ctx.state.count++;

ctx.state.name = "John";

ctx.state.user.name = "John";
```

---

## State Updates

State can only be modified through:

```js
ctx.setState({
    count: 10
});
```

---

## DOM Update Trigger

Only `setState()` may trigger UI updates.

Rule:

```text
Direct mutation → No update

setState() → Update allowed
```

---

# Reactivity Model

DelgadoJS does not implement automatic dependency tracking.

Supported flow:

```text
setState()
↓
update framework bindings
↓
update DOM
```

---

## Unsupported Features

```text
Proxy reactivity
Dependency tracking
Signals
Watchers
Computed values
Virtual DOM
```

---

# Directives

## x-text

### Syntax

```html
<span x-text="count"></span>
```

### Contract

```text
count must reference a state key
```

### Effect

```js
element.textContent = state.count;
```

---

## x-show

### Syntax

```html
<div x-show="visible"></div>
```

### Effect

If value is false:

```js
element.style.display = "none";
```

If value is true:

```js
element.style.display = "";
```

---

### Forbidden Implementations

```text
visibility:hidden
opacity:0
remove element
```

---

## x-model

### Syntax

```html
<input x-model="name">
```

### Effect

```text
state.name ↔ input.value
```

Two-way binding is implemented through `setState()`.

---

## x-ref

### Syntax

```html
<input x-ref="email">
```

### Access

```js
ctx.refs.email
```

---

## x-for

### Syntax

```html
<li x-for="users"></li>
```

### Contract

```text
users must reference an array
```

---

### Rendering Rule

Whenever the array changes through `setState()`:

```text
Clear rendered children
Render all items again
```

---

### Unsupported Features

```text
Keyed reconciliation
Virtual DOM diffing
Incremental list patching
```

---

# Event System

## Event Binding

### Syntax

```html
<button x-click="save"></button>
```

---

### Resolution

```text
save → methods.save
```

---

### Method Signature

```js
methods: {
    save(ctx, event) {}
}
```

---

## Forbidden Event Expressions

```html
<button x-click="save()">

<button x-click="save(id)">

<button x-click="count++">

<button x-click="user.name='John'">
```

Events may only reference component methods.

---

# Lifecycle

## mounted

Executed after component initialization and DOM binding.

```js
mounted(ctx) {}
```

---

## unmounted

Executed before component removal.

```js
unmounted(ctx) {}
```

---

# Template Rules

Templates are declarative.

Templates must not contain JavaScript expressions.

---

## Allowed

```html
<span x-text="count"></span>
```

---

## Forbidden

```html
<span x-text="count + 1"></span>

<div x-show="user.age > 18"></div>

<button x-click="save(user.id)"></button>
```

---

# Project Structure

Recommended structure:

```text
src/
├── app.js
├── components/
│   ├── user-card.js
│   └── user-list.js
├── pages/
├── services/
└── templates/
```

---

# AI Contract

The following rules are considered stable public API.

## Component API

```yaml
component_api:
  - state
  - props
  - refs
  - el
  - setState
```

---

## Method Contract

```yaml
methods:
  receive_ctx: true
```

---

## State Contract

```yaml
state:
  mutable: false
  update_method: setState
```

---

## DOM Update Contract

```yaml
dom_updates:
  trigger: setState
```

---

## Component Resolution Contract

```yaml
component_loading:
  mode: explicit_registration
```

---

## Template Contract

```yaml
template_expressions:
  allowed: false
```

---

# Forbidden Framework Features

The following concepts are explicitly outside the scope of DelgadoJS:

```yaml
forbidden:
  - virtual_dom
  - dependency_tracking
  - signals
  - computed
  - watchers
  - automatic_component_discovery
  - implicit_state_mutation
  - template_expressions
  - this_context
  - runtime_compilation
  - filesystem_component_resolution
```

---

# Framework Guarantee

DelgadoJS guarantees that:

1. All component resolution is explicit.
2. All state updates occur through `setState()`.
3. All template behavior is declarative.
4. No hidden runtime conventions exist.
5. Every framework behavior can be derived from this specification.
6. AI agents can understand framework behavior without inspecting framework internals.
