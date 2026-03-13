# 100 Essential React Interview Questions in 2026

<div>
<p align="center">
<a href="https://devinterview.io/questions/web-and-mobile-development/">
<img src="https://firebasestorage.googleapis.com/v0/b/dev-stack-app.appspot.com/o/github-blog-img%2Fweb-and-mobile-development-github-img.jpg?alt=media&token=1b5eeecc-c9fb-49f5-9e03-50cf2e309555" alt="web-and-mobile-development" width="100%">
</a>
</p>

#### You can also find all 100 answers here 👉 [Devinterview.io - React](https://devinterview.io/questions/web-and-mobile-development/react-interview-questions)

<br>

## 1. What is _React_ and why is it used?

### React: Modern Definition (2026)

**React** is a declarative, component-based library for building user interfaces. As of 2026, it is governed by the **React 19+** paradigm, emphasizing **Concurrent Rendering**, **Server Components**, and **Actions**.

---

### Core Architectural Concepts

#### Concurrent Rendering & Virtual DOM
While React still utilizes a Virtual DOM for reconciliation, the 2026 standard emphasizes **Concurrent React**. This allows React to prepare multiple versions of the UI simultaneously without blocking the main thread. By prioritizing updates (e.g., input typing over data fetching), React achieves better responsiveness. The diffing algorithm maintains $O(n)$ complexity through heuristic assumptions, optimized for modern browser engines.

#### Functional Components & Hooks (The Standard)
**Class Components are effectively legacy.** Modern development exclusively uses **Functional Components**.
*   **Hooks:** The standard mechanism for state (`useState`), side effects (`useEffect`), and memoization (`useMemo`, `useCallback`).
*   **React 19 Features:** The introduction of the `use` hook and automatic transitions has replaced much of the boilerplate previously required for data fetching.

#### Server Components (RSC)
The architectural divide between **Client Components** and **Server Components** is fundamental. Server Components execute exclusively on the server, sending rendered UI to the client. This reduces bundle size significantly, as component-level logic and dependencies remain on the server, shipping zero-kilobyte JavaScript to the browser for static portions of the UI.

#### Unidirectional Data-Flow & Server Actions
Data remains strictly one-way (parent-to-child). However, **Server Actions** have replaced the traditional pattern of fetching data via `useEffect` + `useState`. Actions allow you to call server-side functions directly from UI elements (e.g., forms), integrating mutation logic directly into the component lifecycle.

#### JSX: Transpilation to React Elements
**JSX** remains the syntax extension for JavaScript. In modern build tools (e.g., Vite, Rspack), JSX is transformed into `_jsx` runtime calls, optimized for the specific rendering target (Client vs. Server).

---

### Why use React in 2026?

*   **Server-Side First:** Modern frameworks (e.g., Next.js, Remix) built on React leverage **React Server Components (RSC)** for superior SEO and performance metrics.
*   **Declarative UI:** By defining UI as a function of state ($UI = f(state)$), React simplifies state transitions and ensures predictable rendering.
*   **The Ecosystem:** 2026 React is part of a massive ecosystem including **TanStack** (Query/Router), **Zustand** (State), and **Tailwind CSS**, providing a standardized path for enterprise-grade scalability.
*   **Type Safety:** First-class support for **TypeScript** is now the baseline. Prop definitions and state types are strictly enforced during the build process, minimizing runtime errors.

---

### Modern Code Example (React 19+)

The following snippet demonstrates the shift toward `useActionState` and modern functional patterns, replacing legacy class-based state management.

```jsx
'use client'; // Required for interactivity in RSC environments

import { useActionState } from 'react';

// Modern approach: Server Actions integration
async function incrementCounter(previousState, formData) {
  return previousState + 1;
}

export default function Counter() {
  const [state, action, isPending] = useActionState(incrementCounter, 0);

  return (
    <form action={action}>
      <button type="submit" disabled={isPending}>
        Clicked {state} times
      </button>
    </form>
  );
}
```

**Audit Note:** The Class-based component example in the original content is now considered an anti-pattern. New projects must utilize functional patterns to leverage **Suspense** and **Streaming SSR** capabilities inherent in the modern React pipeline.
<br>

## 2. How is _React_ different from _Angular_ or _Vue_?

### Architectural Paradigm
*   **React (19)**: A **library** for building user interfaces using a **Causal Decoder-only** conceptual model for rendering. It emphasizes **functional programming** paradigms via Hooks and the **React Compiler** (automatic memoization).
*   **Angular (v19+)**: A **platform** utilizing a **Signals-based** reactivity system (replacing legacy `Zone.js` change detection). It mandates a strict **Dependency Injection** architecture.
*   **Vue.js (3.x/4)**: A **progressive framework** utilizing a **Proxy-based** reactivity system. It bridges the gap between React’s composability and Angular’s template-first DX.

### Learning Curve
*   **React**: Steepened in 2026 due to the required mastery of **Server Components (RSC)**, **Suspense**, and the transition to native asynchronous patterns.
*   **Angular**: Flattened by **Standalone Components**, removing the necessity for `NgModules` which previously burdened beginners.
*   **Vue.js**: Remains the most accessible due to the **Composition API** and its similarity to standard Web Components.

### Performance & Rendering
*   **React**: Shifted toward **Server-Side Rendering (SSR)** and **Partial Hydration**. Complexity of re-renders: $O(n)$ where $n$ is the number of component nodes; optimized via the **React Compiler** to minimize unnecessary diffing.
*   **Angular**: The **Signals** API allows for granular updates ($O(1)$ component updates) by tracking state dependencies directly rather than diffing the entire component tree.
*   **Vue.js**: Highly performant through **Virtual DOM** combined with **Static Hoisting** and **Patch Flags**, allowing the compiler to optimize dynamic content patching at $O(m)$ where $m$ is the number of dynamic nodes.

### State Management
*   **React**: Canonical approach is **Server Actions** and **React Query (TanStack)** for server state; **Zustand** or **Context** for client state. `setState` is effectively superseded by `useActionState` and `useOptimistic` hooks.
*   **Angular**: **RxJS** is now optional in favor of **Signals**, which provide a more ergonomic, imperative-like syntax for reactive state without the complexity of streams.
*   **Vue.js**: **Pinia** is the official standard, replacing Vuex. It is built natively for **TypeScript** and provides a modular, lightweight store architecture.

### Templating & Composition
*   **React**: **JSX** remains the industry standard. The trend is moving toward **Server Components**, where logic and UI are unified to reduce client-side bundle size.
*   **Angular**: Uses **Template Syntax** with structural directives. The recent introduction of **Control Flow** (`@if`, `@for`) has replaced `*ngIf` and `*ngFor` for better type safety and performance.
*   **Vue.js**: **Single-File Components (SFCs)** using `<script setup>` syntax are the industry best practice, offering the tightest integration between template logic and reactive state.

### Tooling & Language Support
*   **React**: **TypeScript** is ubiquitous. Tooling is highly dependent on the meta-framework (e.g., **Next.js** or **Remix**), which manages the LSP and build pipeline.
*   **Angular**: **Angular Language Service** remains the gold standard for enterprise-grade IDE support, providing deep type-checking across templates and controllers.
*   **Vue.js**: **Volar (Vue Language Tools)** provides best-in-class support for TypeScript inference within SFCs, often outperforming JSX-based tooling in template type-checking precision.
<br>

## 3. What is a _React component_?

### React Component Definition (2026 Standard)

A **React component** is the fundamental building block of modern React applications, encapsulating UI logic and structure. In the current ecosystem (React 19+), components are defined as **function components**. While class components remain technically supported for legacy compatibility, they are considered **deprecated architectural patterns** in favor of functional paradigms.

#### Function Components vs. Legacy Class Components

*   **Function Components**: The industry standard. These are JavaScript functions that accept `props` as an argument and return UI elements. They leverage **Hooks** to manage state, context, and side effects.
*   **Class Components**: Legacy structures that use ES6 classes extending `React.Component`. These are now discouraged due to increased bundle sizes and complexities regarding `this` binding and lifecycle fragmentation.

#### JSX and Modern Rendering

*   **JSX**: A syntax extension to JavaScript that allows UI markup to be written within code. As of React 19, the `React.createElement` transformation is largely optimized by modern compilers (like SWC or Babel), transpiling JSX directly into highly performant virtual DOM nodes.
*   **Server Components (RSC)**: The 2026 standard dictates that components are now differentiated by their execution environment. **Server Components** run exclusively on the server, reducing client-side bundle size, while **Client Components** handle interactivity via hooks.

#### Structural Coherence

React applications maintain a **hierarchical tree structure**. The entry point (e.g., `createRoot`) initiates the tree, which propagates updates through **Concurrent Rendering**. This allows React to prioritize urgent updates (like user input) over transitionary background tasks, improving perceived performance.

#### Data Flow and State Management

React enforces **unidirectional data flow**. Data (props) flows strictly from parent to child. 

*   **Props**: Immutable data passed down the component tree.
*   **State**: Reactive data managed via `useState` or `useReducer`. In modern React, state updates trigger a **re-render cycle** where React computes the "diff" between the previous and current Virtual DOM, applying only necessary patches ($O(n)$ complexity).
*   **State Synchronization**: Modern state management often moves beyond simple `props` drilling, utilizing `useContext` or external reactive stores (e.g., Zustand, TanStack Query) to facilitate cross-tree data access without performance bottlenecks.

#### Lifecycle Operations: The Hook Paradigm

Lifecycle methods (`componentDidMount`, etc.) have been superseded by the `useEffect` hook and the broader **Effect API**. 

*   **Declarative Synchronization**: Instead of imperative lifecycle hooks, modern components synchronize with external systems using declarative dependencies.
*   **Suspense & Transitions**: React 19+ handles "loading" states and complex data fetching via `<Suspense>` boundaries. Rather than manually managing "mounting" status, components define their loading state declaratively, leading to a smoother user experience in data-intensive applications. 

**Architectural Note**: As of 2026, the focus has shifted from "lifecycle management" to "synchronization with external state," prioritizing **Composition** over the inheritance models used in older class-based designs.
<br>

## 4. How do you create a _component_ in _React_?

### Component Architecture in React (2026 Standards)

Modern React development strictly prioritizes **Functional Components** using **Hooks**. While Class-based components remain supported for legacy compatibility, they are considered an anti-pattern in new feature development due to higher memory overhead and complex `this` binding behaviors.

### Modern Implementation

Functional components leverage **Hooks** (e.g., `useState`, `useEffect`) to manage state and side effects, replacing the legacy lifecycle methods (`componentDidMount`, `componentWillUnmount`).

#### Functional Component Example (React 19+)

```jsx
// React 19 no longer requires explicit 'import React' for JSX
const Greeting = ({ name }: { name: string }) => {
  return <h1>Hello, {name}!</h1>;
};

export default Greeting;
```

*Note: The use of **TypeScript** is the industry standard for 2026, providing static type checking for props and return types.*

### Modern State and Lifecycle Management

In 2026, the industry has transitioned away from manual lifecycle management toward declarative synchronization. 

1.  **Hooks**: Logic is encapsulated in custom hooks, increasing **reusability** and **testability** compared to class methods.
2.  **Server Components**: For performance optimization, components can now execute on the server, reducing the client-side JavaScript bundle size.
3.  **Concurrency**: React 19 features, such as `useTransition` and `useActionState`, allow for non-blocking UI transitions and seamless form state management.

### Development Standards: Linting and Formatting

Modern ecosystems rely on **ESLint (Flat Config)** and **Biome** or **Prettier** to enforce code quality.

*   **TypeScript Integration**: Strict mode is mandated to ensure type safety, reducing runtime errors.
*   **Component Composition**: Architecture patterns favor **Composition over Inheritance**. Complex behaviors are abstracted into reusable Hooks rather than High-Order Components (HOCs) or Render Props.
*   **Modern Formatting**: The Airbnb style guide has largely been superseded by community-driven standards that emphasize **readability** and **zero-config** setups (e.g., Vite/Turbopack ecosystems).

### Technical Audit Summary

| Feature | Legacy Status | 2026 Standard |
| :--- | :--- | :--- |
| **Component Type** | Class-based | Functional (Hooks) |
| **Type Checking** | PropTypes | TypeScript (Interface/Types) |
| **Data Fetching** | `componentDidMount` | `use` API / TanStack Query |
| **Rendering** | Client-Side (CSR) | Server Components (RSC) / Streaming |

#### Key Takeaways

*   **Functional Components** are the standard; avoid classes in new architecture.
*   **TypeScript** is essential for maintaining large-scale codebases.
*   **Composition** is the primary mechanism for logic reuse; prioritize Custom Hooks.
*   **Performance** is optimized via Server Components and concurrent rendering features.
<br>

## 5. What is _JSX_ and why do we use it in _React_?

### JSX in React 19+

**JSX** (JavaScript XML) is a syntax extension for JavaScript that provides a declarative way to describe the UI structure. While it resembles HTML, it is a syntactic sugar for function calls that compile into the React element tree.

### Technical Foundations

*   **Syntactic Sugar**: JSX is not HTML; it is an abstraction over `React.createElement` (or the newer `jsx()` transform introduced in React 17+).
*   **Expression Embedding**: Developers can embed arbitrary JavaScript expressions within curly braces `{}`. The React runtime handles the evaluation and injection of these expressions into the VDOM.
*   **Transpilation**: Modern build tools (Vite, SWC, or Babel) transform JSX into standard JavaScript during the build phase. SWC, written in Rust, is the 2026 standard for high-performance JSX transpilation.

### Key Features

*   **Declarative UI**: By aligning code structure with the DOM hierarchy, JSX reduces cognitive load, mapping component logic directly to the rendered output.
*   **Type-Safe Elements**: When coupled with TypeScript, JSX provides full **type inference** for component props, ensuring that the interface contracts are enforced during the compilation phase ($O(n)$ complexity for type checking against the component's signature).
*   **Component Composition**: JSX facilitates the composition of complex UIs by allowing the nesting of custom components just like standard intrinsic elements.

### Benefits of Modern JSX

*   **Performance Optimization**: React 19’s compiler can perform advanced **memoization** and **hoisting** optimizations directly on JSX structures, reducing the overhead of re-renders.
*   **Developer Experience (DX)**: IDEs (VS Code/Cursor) utilize the JSX structure to provide robust IntelliSense, refactoring capabilities, and real-time linting.
*   **Reduced Boilerplate**: JSX eliminates the verbosity of manual functional calls, replacing deeply nested `React.createElement` chains with readable, declarative trees.

### Code Example: JSX and Transpiled Output

**JSX (React 19 syntax)**:
```jsx
// Direct usage of the new JSX transform
const element = <h1 className="title">Hello, World!</h1>;
```

**Transpiled JavaScript (React 19 Runtime)**:
```javascript
import { jsx as _jsx } from "react/jsx-runtime";

// The modern transform removes the need to import 'React' in every file
const element = _jsx("h1", { className: "title", children: "Hello, World!" });
```

### Why Use JSX in 2026?

*   **Ecosystem Alignment**: The entire React ecosystem (Server Components, Suspense, Hooks) is built around JSX. Deviating from JSX restricts access to the latest framework features and community-maintained UI libraries.
*   **Compile-Time Verification**: JSX allows build tools to detect missing props or invalid tag structures before the code ever reaches the browser, drastically reducing runtime errors.
*   **Declarative Consistency**: JSX provides a unified language for both Client Components and React Server Components (RSC), allowing for a seamless transition between server-side rendering and client-side interactivity.
*   **Tooling Optimization**: The 2026 build stack is optimized specifically for JSX. Using JSX allows tools like the React Compiler to analyze your component graph statically, resulting in significant runtime performance gains compared to non-JSX approaches.
<br>

## 6. Can you explain the _virtual DOM_ in _React_?

### Virtual DOM: 2026 Technical Audit

The **Virtual DOM (VDOM)** remains a fundamental architectural pattern in React, though its implementation has evolved significantly with the introduction of **React Fiber** and concurrent rendering primitives. It serves as a declarative blueprint for the UI, allowing React to decouple UI state from expensive imperative browser DOM manipulations.

### Refined Mechanism

1. **Initial Rendering**: React creates a **Fiber tree**, a internal data structure representing the component tree. Each Fiber node corresponds to a React component or DOM element, holding state, props, and side effects.

2. **Reconciliation (The Diffing Algorithm)**: When state updates occur, React triggers a re-render. It performs a tree comparison between the current Fiber tree and the new structure. React uses a heuristic $O(n)$ algorithm, assuming that components of the same type produce similar trees, to minimize computational overhead.

3. **Fiber Architecture**: Unlike older versions, the 2026 reconciliation process is **incremental and interruptible**. React can pause, abort, or reuse work on the component tree to prioritize urgent updates (e.g., user input) over background tasks (e.g., data fetching), maintaining high frame rates.

4. **Batched Transitions**: React 19+ utilizes `useTransition` and `useDeferredValue` to categorize updates. State changes are grouped into **transitions**, ensuring that lower-priority updates do not block the main thread, effectively decoupling logic from rendering.

5. **Commit Phase**: Once reconciliation is complete, React enters the "Commit Phase." Here, it applies changes to the actual DOM in a single pass. By batching these operations, React minimizes **layout thrashing** and forced synchronous reflows.

6. **Concurrent Rendering**: Modern React renders components "concurrently," meaning multiple versions of the UI can be prepared in memory simultaneously. The VDOM acts as a scratchpad where React experiments with changes before "committing" them to the screen, providing a seamless transition between states.

7. **Declarative DOM Abstraction**: Direct DOM manipulation (e.g., `document.getElementById`) is considered an anti-pattern. React provides `useRef` for necessary imperative escapes, but the VDOM ensures that the source of truth remains within the React component lifecycle, preventing synchronization desyncs.

8. **Universal Reconciliation**: The VDOM is platform-agnostic. While `react-dom` handles browser-specific updates, the reconciliation logic is reused by `react-native` (via native bridge) and `react-three-fiber` (via custom renderers), providing a consistent declarative API across platforms.

### Performance Complexity
The efficiency of the VDOM is rooted in:
* **Heuristic Diffing**: Reduces tree comparison from $O(n^3)$ to $O(n)$ by utilizing key properties on lists and component type comparisons.
* **Batching**: Reduces the constant factor of DOM writes, which are typically $O(DOM\_nodes\_changed)$, by grouping them into a single browser reflow cycle.
<br>

## 7. What are the differences between a _class component_ and a _functional component_?

### Technical Audit: Component Architecture (2026 Standards)

In 2026, **Functional Components** are the industry standard for all new development. **Class Components** are considered legacy and should be avoided in modern React 19+ applications.

### Core Distinctions

**Class Components (Legacy)**:
*   Defined by extending `React.Component` or `React.PureComponent`.
*   Relies heavily on the `this` keyword, which often leads to complex binding issues.
*   Encapsulates state in an object-based `this.state`.
*   Uses imperative lifecycle methods (e.g., `componentDidMount`, `componentDidUpdate`).

**Functional Components (Modern)**:
*   Defined as pure JavaScript functions.
*   Stateless by nature but achieve state/effect persistence via **Hooks**.
*   Eliminates the `this` binding overhead.
*   Optimized for modern compiler features like **React Forget** (Automatic Memoization).

### Detail Evaluation

#### Code Structure
*   **Class Components**: Requires a `render()` method returning JSX. Complex components often result in "wrapper hell" due to the limitations of inheritance and Higher-Order Components (HOCs).
*   **Functional Components**: Direct return of JSX. Employs **Composition** and **Custom Hooks** to extract logic, resulting in a cleaner, flatter component tree.

#### Purpose and Use-Cases
*   **Class Components**: Exists strictly for maintenance of legacy codebases (pre-React 16.8). 
*   **Functional Components**: The primary building block. Encourages the "Single Responsibility Principle" through Hook composition.

#### Editable State
*   **Class Components**: `this.setState()` triggers a shallow merge. 
*   **Functional Components**: `useState` and `useReducer` allow for granular state updates. In 2026, state is typically treated as immutable, often managed via deep-comparable structures or reactive primitives.

#### Lifecycle Methods vs. Synchronization
*   **Class Components**: Lifecycle methods are imperative and grouped by timing (Mount, Update, Unmount). This often leads to fragmented logic, where related code is spread across multiple methods.
*   **Functional Components**: The `useEffect` hook enables **synchronization logic**. By focusing on state dependencies rather than lifecycle events, the code remains highly cohesive.
    *   *Note*: `useEffect` is increasingly replaced by specialized hooks or server-side primitives in React 19+.

#### Context and State Management
*   **Class Components**: Consumed via `static contextType` (limited to one context) or Render Props.
*   **Functional Components**: `useContext` hook allows multiple context consumptions. Compatible with modern state stores like **Zustand**, **Valtio**, or **React Query**, which leverage hooks for seamless reactivity.

### Adoption and Transition

*   **React 19/20 Shift**: Modern React emphasizes **Server Components (RSC)**. Functional Components are now the execution unit for both Server and Client-side rendering.
*   **Performance**: Functional components perform better with the **React Compiler**, which automatically memoizes components, a feat difficult to achieve with complex `class` hierarchies.
*   **Maintenance**: Migrating legacy classes to functional components is the recommended path to reduce technical debt and unlock features like Concurrent Rendering and Streaming SSR.

### Key Takeaways

*   **Class Components**: 
    *   Deprecated pattern; rely on `this` context.
    *   Heavyweight, imperative, and verbose.
    *   Restrictive composition patterns.
*   **Functional Components**: 
    *   Standardized since React 16.8; mandatory for modern paradigms (RSC).
    *   Lean, declarative, and highly reusable.
    *   Deeply integrated with the React ecosystem and performance-optimizing compilers.
<br>

## 8. How do you handle _events_ in _React_?

### Handling Events in React (2026 Standards)

React handles events via **SyntheticBaseEvent**, a cross-browser wrapper around the native browser event. This abstraction ensures consistent interface behavior across environments (DOM, React Native) and provides performance optimizations.

#### Synthetic Events
React normalizes events so that they have the same properties across all browsers. As of **React 19**, synthetic events are not pooled, eliminating the need to call `event.persist()` to access event properties asynchronously.

#### Event Subscription
- **Naming Conventions**: React uses `camelCase` for event handlers (e.g., `onClick`, `onKeyDown`) instead of the native HTML lowercase (e.g., `onclick`).
- **Function References**: You pass a function as the event handler rather than a string.
- **Modern Syntax**: Class components are legacy. Modern development utilizes **Functional Components** with **Hooks**.

#### Modern Event Handling: Functional Components
In 2026, state is managed via `useState`. Binding in constructors is unnecessary, and the use of `useCallback` is recommended to prevent unnecessary re-renders of child components receiving event handlers.

### Code Example: Functional Component Pattern (React 19)

```tsx
import { useState, useCallback, FormEvent, ChangeEvent } from 'react';

export default function Form() {
  const [value, setValue] = useState<string>('');

  // useCallback optimizes performance by memoizing the handler reference
  const handleChange = useCallback((e: ChangeEvent<HTMLInputElement>) => {
    setValue(e.target.value);
  }, []);

  const handleSubmit = useCallback((e: FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    console.log('Submitted:', value);
  }, [value]);

  return (
    <form onSubmit={handleSubmit}>
      <label htmlFor="name-input">Name:</label>
      <input 
        id="name-input"
        type="text" 
        value={value} 
        onChange={handleChange} 
      />
      <button type="submit">Submit</button>
    </form>
  );
}
```

### Key Architectural Updates (2026)
1. **Event Delegation**: React 19 continues to attach event listeners to the root container rather than individual DOM nodes, reducing memory overhead to $O(1)$ relative to total interactive elements.
2. **TypeScript Integration**: Strictly typed `ChangeEvent` and `FormEvent` objects replace the generic `event` type, ensuring type safety in the event pipeline.
3. **Transition API**: For non-urgent updates (e.g., filtering a large list), `useTransition` should be used to prevent event handlers from blocking the main thread, maintaining a UI responsiveness complexity of $O(1)$ for input latency.
<br>

## 9. What are _state_ and _props_ in _React_?

### Data Management in React (2026 Standards)

In modern React (v19+), **props** and **state** remain the fundamental primitives for data propagation and UI synchronization. React's architecture follows a **unidirectional data flow** where data typically moves from parent to child via props, while state remains localized to the component or lifted to shared ancestors.

### Role & Lifecycle

*   **Props (Properties):** Immutable data objects passed from a parent component to a child. They serve as the component’s **configuration**. A component must be **pure** with respect to its props; it should never modify its own props, as they are managed by the parent.
*   **State:** Local, mutable data storage within a component. Updating state via the setter function (e.g., `useState` dispatch) schedules a **re-render** of the component tree to reflect the new state.

### Strategic Selection

*   **Props:** Use for data transmission, configuration, and event callbacks. Props act as the "API" of a component.
*   **State:** Use for data that changes over the component’s lifecycle—such as user inputs, server-side response buffers, or ephemeral UI toggles. With the rise of Server Components in React 19, state is minimized in favor of streaming data directly from the server.

### Management & Performance

*   **Re-rendering:** React uses a **Fiber** reconciliation algorithm. Updating state triggers a sub-tree render. In 2026, we avoid manual optimization like `shouldComponentUpdate` (deprecated for functional components). Instead, we utilize `useMemo`, `useCallback`, and the `memo` Higher-Order Component to prevent unnecessary re-computations.
*   **State Hoisting:** When multiple components share state, "lift" the state to the nearest common ancestor.
*   **Composition:** Rather than deep prop-drilling, use **React Context** or **Server Components** to inject data into sub-trees.

### Unifying with Hooks and Server Components

*   **Functional Components:** The industry standard. Class components are legacy and should not be used in new projects.
*   **React 19 Context:** The `use` hook has modernized how we consume Promises and Context, allowing for cleaner asynchronous state resolution within components.
*   **Server Components:** By default, components in the App Router architecture are **Server Components**. They do not support hooks like `useState`. State is relegated to **Client Components** explicitly marked with the `"use client"` directive.

### Code Example: Modernized State and Props

```javascript
'use client';

import React, { useState, memo } from 'react';

// Use memo to prevent unnecessary re-renders of stable children
const Button = memo(({ text, color, onClick }) => {
  return (
    <button style={{ backgroundColor: color }} onClick={onClick}>
      {text}
    </button>
  );
});

const ColorPicker = () => {
  const [color, setColor] = useState('blue');

  // useCallback is omitted here for brevity, 
  // but recommended for complex prop-drilling scenarios.
  return (
    <div>
      <p>Current Color: {color}</p>
      <Button text="Red" color="red" onClick={() => setColor('red')} />
      <Button text="Blue" color="blue" onClick={() => setColor('blue')} />
    </div>
  );
};

export default ColorPicker;
```

### Complexity Analysis
*   **Rendering Complexity:** Given $n$ nodes in a component sub-tree, a state update has a time complexity of $O(n)$ in the worst case, as React must perform reconciliation (diffing) to determine DOM patches. 
*   **Optimization:** Using `memo` reduces the branching factor of the reconciliation process, effectively pruning sub-trees that do not require re-evaluation, optimizing performance towards $O(k)$ where $k$ is the number of changed nodes.
<br>

## 10. How do you pass _data_ between _components_ in _React_?

### Data Propagation in React (2026)

**Data propagation** in React (v19+) adheres to the core principle of **unidirectional data flow**. While the fundamentals of Props and Callbacks remain, modern React emphasizes **Server Components**, **Context API**, and **Signals** (or state management libraries) to mitigate "prop drilling."

#### Mechanism 1: Unidirectional Props
Props facilitate parent-to-child communication. In React 19, props are now treated as standard **stable arguments** to component functions, eliminating the need for `React.FC` wrapper types, which are now largely considered boilerplate.

#### Mechanism 2: Callback Functions
Data moves up the tree when children invoke functions passed down as props. These functions typically trigger `dispatch` actions or state setters (e.g., `useState`, `useActionState`).

---

### Best Practices: 2026 Standards

1.  **Component Definition**: Prefer standard function declarations over `React.FC`.
2.  **Type Safety**: Use `interface` or `type` for props, favoring **discriminated unions** for complex state transitions.
3.  **Prop Drilling Mitigation**: For deep tree propagation, use the **Context API** or **Signals** to bypass intermediary components.
4.  **Composition**: Use the `children` prop (or slot patterns) to compose components, reducing the need to pass data through multiple layers.

---

### Modernized Code Example: State Synchronization

In this 2026-compliant implementation, `App` maintains the state, and `DropDown` notifies the parent of changes via a callback, maintaining **Single Source of Truth (SSOT)**.

```tsx
// src/components/DropDown.tsx
interface DropDownProps {
  options: string[];
  onSelect: (option: string) => void;
  selected: string;
}

// Modern function declaration without React.FC boilerplate
export function DropDown({ options, onSelect, selected }: DropDownProps) {
  return (
    <nav>
      <p>Current: {selected}</p>
      {options.map((opt) => (
        <button 
          key={opt} 
          onClick={() => onSelect(opt)}
          aria-pressed={selected === opt}
        >
          {opt}
        </button>
      ))}
    </nav>
  );
}

// src/App.tsx
import { useState } from 'react';
import { DropDown } from './components/DropDown';

export default function App() {
  const [selection, setSelection] = useState<string>('Apple');
  const options = ['Apple', 'Banana', 'Cherry'];

  return (
    <main>
      <DropDown 
        options={options} 
        onSelect={setSelection} 
        selected={selection} 
      />
      <section>You selected: {selection}</section>
    </main>
  );
}
```

### Complexity Analysis
For a tree of depth $d$ and branching factor $b$, passing props through every layer incurs:
- **Time Complexity**: $O(1)$ per prop transfer.
- **Maintenance Complexity**: $O(d)$ for refactoring.

Using **Context API** reduces maintenance complexity to $O(1)$ for leaf node access, effectively decoupling the intermediate nodes from the data propagation logic.
<br>

## 11. What is a _stateful component_?

### Modernization Audit (2026 Standards)

The original content relies on **Class Components** (`React.Component`), which are legacy patterns. In **React 19**, the industry standard for state management is the **Hooks API**. While class components remain supported for backward compatibility, they are deprecated in modern architectural paradigms.

#### Definition
**Stateful components** in React are components that manage internal data that persists across renders. In modern React, this is achieved using the `useState` or `useReducer` hooks. When state values update, React triggers a re-render of the component to synchronize the DOM with the new state.

### When to Use

*   **Dynamic Interactions**: Components requiring real-time updates (e.g., counters, toggles).
*   **User Input Handling**: Managing controlled components in forms.
*   **Data Synchronization**: Handling asynchronous API responses and managing their loading/error states.

### Code Example: Functional Stateful Component

In **React 19**, use the `useState` hook. This approach eliminates `this` binding issues and reduces boilerplate code.

```jsx
import React, { useState } from 'react';

const ClickCounter = () => {
  // useState returns the current value and a setter function
  const [count, setCount] = useState(0);

  const handleIncrement = () => {
    // Functional updates ensure correct state transition based on previous value
    setCount((prevCount) => prevCount + 1);
  };

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={handleIncrement}>Increment</button>
    </div>
  );
};

export default ClickCounter;
```

### Technical Note: Complexity & Performance
*   **Re-rendering**: State updates in React trigger a reconciliation process. React utilizes a **Fiber** architecture, allowing the engine to split rendering work into chunks with an asymptotic complexity of $O(n)$ in the worst case relative to the component tree size.
*   **Memoization**: To optimize stateful components, use `React.memo` or `useMemo` to prevent unnecessary re-renders of child components if props remain referentially equal, maintaining efficient $O(1)$ lookup for dependency changes.
<br>

## 12. Can you explain how _useState_ works?

### `useState` in Modern React (2026)

**`useState`** is a fundamental **React Hook** that enables functional components to maintain state across re-renders. It returns a pair: the current state value and a dispatch function to update it. By leveraging **Fiber architecture**, React tracks the order of hook calls via a linked list stored on the `memoizedState` property of the `fiber` node.

#### Core Components of `useState`

1. **Stateful Value**: The first element in the returned array represents the current state. React preserves this value between renders.
2. **Dispatch Function**: The second element is the state-setter. In React 19+, this function is stable across renders, allowing it to be passed as a prop without triggering unnecessary downstream re-renders.

#### Behavioral Traits of `useState`

*   **Lazy Initialization**: Passing a function to `useState` (e.g., `useState(() => expensiveCalculation())`) ensures the function executes only during the **initial mount**. This prevents unnecessary overhead during subsequent render cycles, which occur with $O(n)$ complexity relative to the state tree size.
*   **State Batching**: Since React 18, all state updates—including those in promises, timeouts, and native event handlers—are automatically batched. This minimizes the number of DOM mutations, effectively reducing the render cost to $O(1)$ per state transition.
*   **Referential Integrity**: Each call to `useState` is indexed by its position in the component's hook list. Conditional execution of hooks is strictly forbidden; breaking this rule causes an architectural mismatch in the linked list, leading to state corruption.

#### Functional Updates
When the new state is derived from the previous state, pass an updater function to avoid stale closures. This pattern is mandatory for **Concurrent React** environments to ensure consistent state transitions.

```jsx
import { useState } from 'react';

const Counter = () => {
  // 2026 Best Practice: Using an arrow function for atomic updates
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount((prev) => prev + 1)}>
      Count: {count}
    </button>
  );
};
```

#### Performance Analysis
Updating state triggers a **Reconciliation** process. React compares the new state with the previous state using `Object.is`. If they are identical, React performs a **bailout**, preventing a re-render of the component and its children. The time complexity for this check is $O(1)$.
<br>

## 13. How do you update the _state_ of a _parent component_ from a _child component_?

### Modernizing React State Synchronization (2026 Standards)

**React** enforces **unidirectional data flow**, where data descends the tree via props. Updating **Parent state** from a **Child component** is fundamentally achieved via **Lifting State Up**, leveraging callbacks or dependency injection.

### Primary Methods

1. **Props Callback (Standard Pattern)**: The parent passes a memoized handler function (e.g., `useCallback`) as a prop. The child invokes this function to trigger a state update in the parent.
  
2. **Context API (via State-Reducer Pattern)**: Use `createContext` coupled with `useReducer` to provide a state dispatcher. This avoids "prop drilling" in deep component trees and is the preferred 2026 standard for intermediate-scale state sharing.

### Advanced Patterns (2026 Context)

1. **Imperative Handle (`useImperativeHandle`)**: Used with `forwardRef` to expose specific imperative methods to a parent. This is preferred over direct reference manipulation, allowing the parent to trigger child logic while maintaining **encapsulation**.

2. **State Management Libraries (Zustand/TanStack Store)**: For complex global state, **Zustand** is the industry standard (2026) due to its minimal boilerplate and **atomic updates**. Redux Toolkit remains viable for large-scale enterprise systems, while MobX is largely relegated to legacy maintenance.

3. **Server State (TanStack Query/SWR)**: Do not use local/global state for server data. Use **TanStack Query** to synchronize client-side state with the server, ensuring automatic cache invalidation and mutation handling.

### Code Example: Optimized Callback (React 19+)

```jsx
// Parent.jsx
import { useState, useCallback } from 'react';
import { Child } from './Child';

export const Parent = () => {
  const [data, setData] = useState('');

  // useCallback prevents unnecessary re-renders of the child
  const handleUpdate = useCallback((newValue) => {
    setData(newValue);
  }, []);

  return <Child onUpdate={handleUpdate} />;
};

// Child.jsx
import { memo } from 'react';

// memo ensures the child only re-renders if props change
export const Child = memo(({ onUpdate }) => {
  return (
    <button onClick={() => onUpdate('Updated via memoized callback')}>
      Update Parent
    </button>
  );
});
```

### Architectural Performance Note
When updating parent state, ensure the state is placed as low as possible in the tree to prevent unnecessary re-renders of unrelated branches. Complexity of state propagation in React is $O(n)$, where $n$ is the number of component updates. Utilize `React.memo` and `useCallback` to maintain an amortized update cost of $O(1)$ relative to the total component count.
<br>

## 14. What is _lifting state up_ in _React_?

### Definition: Lifting State Up
**Lifting State Up** is the architectural pattern of moving local component state to a common ancestor to synchronize data across multiple dependent components. This enforces a **unidirectional data flow**, ensuring that changes propagate downward through `props` while maintaining a **Single Source of Truth (SSOT)**.

### Why Use Lifting State Up?
- **Data Synchronization**: Ensures child components react to the same state updates, preventing disparate or "stale" UI states.
- **Derived State Efficiency**: Simplifies logic where multiple children depend on a single state variable; the parent calculates derived values, reducing redundant computations.

### Core Mechanism: React 19 Standards
In modern React (19+), functional components and hooks have superseded class-based components. State is managed via `useState` and synchronized via prop drilling or **React Context** for deep component trees.

### Modernized Code Implementation (React 19)

#### Parent Component: `RectangleAreaCalculator`
```jsx
import { useState } from 'react';

const RectangleAreaCalculator = () => {
  const [dimensions, setDimensions] = useState({ width: 0, height: 0 });

  const handleChange = (e) => {
    const { name, value } = e.target;
    setDimensions(prev => ({ ...prev, [name]: Number(value) }));
  };

  return (
    <section>
      <ShapeInput name="width" value={dimensions.width} onChange={handleChange} />
      <ShapeInput name="height" value={dimensions.height} onChange={handleChange} />
      <ShapeArea area={dimensions.width * dimensions.height} />
    </section>
  );
};
```

#### Child Components: `ShapeInput` and `ShapeArea`
```jsx
const ShapeInput = ({ name, value, onChange }) => (
  <div>
    <label>{name.charAt(0).toUpperCase() + name.slice(1)}</label>
    <input name={name} type="number" value={value} onChange={onChange} />
  </div>
);

const ShapeArea = ({ area }) => <div>Area: {area}</div>;
```

### Advantages
- **SSOT Integrity**: Prevents state duplication. Since React's reconciliation engine ($O(n)$ complexity for component tree updates) relies on state stability, centralized control minimizes unnecessary re-renders.
- **Predictable Debugging**: Facilitates the use of DevTools to trace state mutations from the parent down, streamlining the isolation of UI defects.

### Implementation Patterns
- **Controlled Components**: Form inputs are managed entirely via parent state, making validation and submission logic centralized.
- **Cross-Component Communication**: Facilitates sibling interaction that would otherwise require complex event bubbling or context overhead.

### When It's Overkill
Lifting state unnecessarily causes **Prop Drilling**, where intermediate components pass data they do not utilize. 

- **Escalation Path**: If state must be accessed by components deeply nested in the tree (e.g., global themes, auth state), utilize the **Context API** or state management libraries (e.g., Zustand or TanStack Store) to bypass the middle-tier components. 
- **Heuristic**: If moving state up increases the `props` count of intermediate components by more than 3, evaluate the use of **Composition** (passing components as `children`) or a dedicated state provider.
<br>

## 15. When do you use _Redux_ or _Context API_ for state management?

### State Management Architecture: Redux vs. Context API (2026 Standards)

Managing application state in **React 19** requires distinguishing between **Global App State** (frequently changing, high-frequency updates) and **Dependency Injection** (low-frequency updates, configuration).

### Redux (Redux Toolkit)
In 2026, **Redux Toolkit (RTK)** is the mandatory interface. The legacy `connect` HOC is deprecated; the `useSelector` and `useDispatch` hooks are the standard for all functional components.

1.  **High-Frequency State**: Optimized for state that changes multiple times per second (e.g., real-time tickers, complex drag-and-drop buffers).
2.  **Predictable State Transitions**: Enforces unidirectional data flow via **Immer**-integrated reducers, ensuring state immutability with $O(1)$ conceptual complexity for developers.
3.  **Middleware Ecosystem**: Robust support for side-effect management via **RTK Query**, which natively handles caching, polling, and optimistic updates, reducing boilerplate compared to manual `useEffect` implementations.
4.  **DevTools & Observability**: Provides centralized logging and time-travel debugging, which remains the industry standard for auditing complex state trajectories in distributed teams.

#### When to Pick Redux
*   **Complex State Dependencies**: Use when state slices share data or require relational logic.
*   **Large-Scale Performance Requirements**: Use when granular component updates are required to prevent re-rendering the entire component tree.
*   **Advanced Caching**: Use when the application requires sophisticated data fetching and cache invalidation strategies provided by RTK Query.

### React Context API
**Context API** is a dependency injection mechanism, not a state management solution. It is designed to propagate values through the tree without prop drilling.

1.  **Lightweight Integration**: Zero-bundle overhead as it is a core feature of React. Ideal for avoiding third-party dependency bloat in lightweight components.
2.  **Performance Constraints**: Context broadcasts updates to all consumers whenever the value changes. Without memoization ($useMemo$, $memo$), this triggers re-renders across the entire provider tree, resulting in $O(n)$ re-render complexity where $n$ is the number of child nodes.
3.  **Encapsulation**: Simplifies the distribution of static or semi-static data (e.g., Localization, Theme, Auth session).

#### When to Pick Context API
*   **Static or Low-Frequency Data**: Ideal for configuration data that rarely changes (e.g., UI themes, current user locale).
*   **Simple Dependency Injection**: Use when you need to pass down services or singleton instances without explicit prop drilling.
*   **Component Libraries**: Essential for building compound component patterns where sub-components must access parent state implicitly.

### Technical Comparison Summary

| Feature | Redux Toolkit | Context API |
| :--- | :--- | :--- |
| **Primary Use** | Complex, high-frequency state | Configuration, Dependency Injection |
| **Performance** | Selective rendering (selectors) | Full subtree re-render (default) |
| **Boilerplate** | Moderate (standardized via RTK) | Minimal |
| **DevTools** | Native Time-Travel | Limited (React DevTools only) |
| **Complexity** | $O(1)$ to $O(log n)$ scaling | $O(n)$ rendering behavior |

**Audit Conclusion**: Favor **Context API** for global UI configurations and **Redux Toolkit** for application business logic, form state, and server-cache synchronization. Abandon the use of legacy class-based `connect` patterns in favor of the current Hook-based API.
<br>



#### Explore all 100 answers here 👉 [Devinterview.io - React](https://devinterview.io/questions/web-and-mobile-development/react-interview-questions)

<br>

<a href="https://devinterview.io/questions/web-and-mobile-development/">
<img src="https://firebasestorage.googleapis.com/v0/b/dev-stack-app.appspot.com/o/github-blog-img%2Fweb-and-mobile-development-github-img.jpg?alt=media&token=1b5eeecc-c9fb-49f5-9e03-50cf2e309555" alt="web-and-mobile-development" width="100%">
</a>
</p>

