# Contributing to Our Project

We welcome contributions from the community! To ensure a smooth and collaborative process, please follow these guidelines.

## General Coding Standards

### Naming Conventions

- **PascalCase**: Use PascalCase for classes, interfaces, enums, and methods (e.g., `MyClass`, `IMyInterface`, `UserRole`, `CalculateValue`).
- **camelCase**: Use camelCase for local variables, method parameters, and private fields (e.g., `let myVariable`, `(int parameterName)`, `private string _fieldName`).

### Formatting

- **Indentation**: Use consistent indentation (e.g., 4 spaces).
- **Spacing**: Use spaces around operators (e.g., `a + b`) and after commas (e.g., `function(param1, param2)`).
- **Braces**:
    - C#: Place braces on new lines (Allman style).
      ```csharp
      public class MyClass
      {
          public void MyMethod()
          {
              // ...
          }
      }
      ```
    - JavaScript/TypeScript: Place braces on the same line (K&R style variant).
      ```javascript
      function myFunction() {
          // ...
      }
      ```
- **Line Length**: Keep lines to a maximum of 120 characters to improve readability.

### Comments

- **Clarity**: Write clear and concise comments to explain complex logic, assumptions, or the intent behind a piece of code.
- **Avoid Obvious Comments**: Do not write comments that merely restate what the code already clearly says.
    ```javascript
    // Bad:
    // i++; // Increment i

    // Good:
    // Increment sequence number to ensure unique IDs
    sequenceNumber++;
    ```
- **XML Documentation Comments (C#)**: Use XML documentation comments for all public APIs.
    ```csharp
    /// <summary>
    /// Calculates the sum of two integers.
    /// </summary>
    /// <param name="a">The first integer.</param>
    /// <param name="b">The second integer.</param>
    /// <returns>The sum of a and b.</returns>
    public int Add(int a, int b)
    {
        return a + b;
    }
    ```
- **JSDoc (TypeScript/JavaScript)**: Use JSDoc for documenting functions, methods, and complex types.
    ```typescript
    /**
     * Greets a person by name.
     * @param name The name of the person to greet.
     * @returns A greeting message.
     */
    function greet(name: string): string {
        return `Hello, ${name}!`;
    }
    ```

### General Principles

- **Readability**: Write code that is easy for others (and your future self) to understand. Clear naming, logical structure, and appropriate comments are key.
- **Simplicity (KISS - Keep It Simple, Stupid)**: Prefer straightforward solutions over overly complex ones. If a piece of code is hard to understand, it's likely too complex.
- **Consistency**: Follow the established patterns, conventions, and style used throughout the project. This makes the codebase more predictable and easier to maintain. If you're unsure about a particular convention, look at existing code or ask.

## ASP.NET Core Specific Guidelines

### API Design

- **RESTful Principles**: Follow RESTful principles for API development.
- **HTTP Verbs**: Use appropriate HTTP verbs (GET, POST, PUT, DELETE, PATCH).
- **URL Naming**: Use clear and consistent URL naming conventions (e.g., plural nouns for collections: `/users`, `/products`).
- **Versioning**: Implement versioning for APIs to manage changes without breaking existing clients (e.g., `/api/v1/users`, `/api/v2/users`).

### Dependency Injection (DI)

- **Built-in DI**: Leverage ASP.NET Core's built-in dependency injection for services, repositories, and other dependencies.
- **Service Lifetimes**: Register services with appropriate lifetimes:
    - **Singleton**: A single instance for the lifetime of the application. Use for stateless services or configuration objects.
    - **Scoped**: One instance per client request (connection). Use for services like DbContext or services that maintain state within a request.
    - **Transient**: A new instance is created every time it's requested. Use for lightweight, stateless services.
- **Avoid Service Locator**: Do not use the service locator pattern (e.g., `HttpContext.RequestServices.GetService<T>()` directly in components). Prefer constructor injection.

### Error Handling and Logging

- **Global Exception Handling**: Use middleware for global exception handling to catch unhandled exceptions and provide consistent error responses.
- **Structured Logging**: Implement structured logging using libraries like Serilog or NLog. This allows for easier querying and analysis of logs.
- **Meaningful Logs**: Log meaningful error messages with sufficient context (e.g., request ID, user ID, relevant parameters) to help diagnose issues.
- **ProblemDetails**: Use `ProblemDetails` (RFC 7807) for consistent, machine-readable error responses in APIs. ASP.NET Core has built-in support for this.

### Controllers

- **Thin Controllers**: Keep controllers thin and focused on request/response handling, model validation, and delegating business logic to services.
- **Business Logic in Services**: Business logic should reside in service classes, not directly in controllers.
- **Async/Await**: Use `async/await` for all I/O-bound operations (e.g., database calls, external API requests) to improve scalability and responsiveness.
- **ActionResults**: Use `ActionResult<T>` and specific results (e.g., `Ok()`, `NotFound()`, `BadRequest()`, `CreatedAtActionResult()`) to provide clear and appropriate HTTP responses.

### Services

- **Encapsulate Business Logic**: Service classes should encapsulate specific business functionalities or use cases.
- **Stateless Services**: Aim for stateless services where possible. If state is needed, it should typically be managed per request (scoped lifetime).
- **Single Responsibility Principle (SRP)**: Services should adhere to the SRP, meaning they should have one primary responsibility.

### Data Access

- **ORM**: Use an Object-Relational Mapper (ORM) like Entity Framework Core for data access.
- **Repository Pattern (Optional but Recommended)**: Consider using the Repository pattern to abstract data access logic from services, making them easier to test and maintain.
- **Query Optimization**: Be mindful of database query performance.
    - Avoid N+1 problems by using eager loading (`Include()`) or projections (`Select()`).
    - Write efficient queries and use database indexes appropriately.
- **Asynchronous Data Access**: Use asynchronous data access methods provided by EF Core (e.g., `ToListAsync()`, `SaveChangesAsync()`) to avoid blocking threads.

### Configuration

- **`appsettings.json`**: Use `appsettings.json` for environment-specific configurations (e.g., `appsettings.Development.json`, `appsettings.Production.json`).
- **Options Pattern**: Use the Options pattern (`IOptions<T>`) to access strongly-typed configuration settings in your application components.
- **Secure Secrets**:
    - **Development**: Use User Secrets Manager to store sensitive data like API keys or connection strings during local development.
    - **Production**: Use secure secret management solutions like Azure Key Vault, AWS Secrets Manager, or HashiCorp Vault. **Do not store plain text secrets in configuration files or source control.**

### Security

- **Authentication & Authorization**:
    - Implement robust authentication (verifying identity) and authorization (verifying permissions).
    - Use ASP.NET Core Identity for user management and authentication.
    - Use JWT (JSON Web Tokens) or other token-based authentication for APIs.
- **Protect Against Common Vulnerabilities**:
    - **XSS (Cross-Site Scripting)**: Use appropriate encoding (e.g., Razor automatically encodes). Be cautious with `Html.Raw()`.
    - **CSRF (Cross-Site Request Forgery)**: Use anti-forgery tokens in forms. ASP.NET Core provides this by default for Razor Pages and MVC form submissions.
    - **SQL Injection**: Use parameterized queries or EF Core, which handles this by default. Avoid raw SQL queries with concatenated user input.
    - **Input Validation**: Validate all incoming data from users or external systems.
- **HTTPS**: Always use HTTPS in production to encrypt data in transit. Enforce HTTPS using middleware.

## Blazor/Razor Specific Guidelines

### Component Design

- **Single Responsibility Principle (SRP)**: Components should be focused on a single piece of functionality.
- **Small Components**: Keep components small and manageable. Decompose larger UI sections into smaller, reusable components.
- **Code-Behind vs. `@code`**:
    - For simple components with minimal logic, using an `@code` block within the `.razor` file is acceptable.
    - For components with significant logic, event handling, or dependency injection, prefer using code-behind files (`MyComponent.razor.cs`) to separate markup from C# code. This improves readability and maintainability.
- **Parameters**: Clearly define component parameters using the `[Parameter]` attribute. Use appropriate types and provide default values if necessary.
- **EventCallbacks**: Use `EventCallback<T>` for component events to communicate changes or actions to parent components. This allows for asynchronous handling and automatic UI updates.

### State Management

- **Component Parameters & Cascading Values**:
    - For passing data down the component tree, use component parameters.
    - For sharing data across a component hierarchy without explicit parameter passing at each level, use `[CascadingParameter]`. This is useful for theming, user context, or shared services.
- **Application State**:
    - For simple global state, a custom `AppState` service (registered with DI, typically scoped or singleton depending on the need) can be used. Components can inject this service to access and modify shared state. `StateHasChanged()` might be needed to trigger UI updates.
    - For more complex scenarios, especially in larger applications, consider a dedicated state management library (e.g., Fluxor, Blazor-State) if the project's complexity justifies it.
- **Component Lifecycles**: Be aware of component lifecycle methods (`OnInitializedAsync`, `OnParametersSetAsync`, `ShouldRender`, `OnAfterRenderAsync`, `Dispose`) and manage state initialization, updates, and cleanup appropriately.

### JavaScript Interop

- **Minimize JS Interop**: Prefer Blazor's built-in C# capabilities. Only use JavaScript interop when a feature is not available in Blazor or requires direct browser API manipulation.
- **`IJSRuntime`**: Use `IJSRuntime` to call JavaScript functions from C# and vice-versa.
- **Separate JS Files**: Place JavaScript functions in separate `.js` files (e.g., under `wwwroot/js`) and load them as needed. This keeps JS code organized and maintainable.
- **Isolate Interop Calls**: Encapsulate JavaScript interop calls within specific C# services or components to make them reusable and easier to manage.
- **Error Handling**: Implement error handling for JS interop calls, as they can fail (e.g., if the JS function doesn't exist or throws an error).

### Performance

- **`@key` Directive**: When rendering lists of items, use the `@key` directive to help Blazor efficiently update the DOM. The key should be a unique identifier for each item.
  ```razor
  @foreach (var item in Items)
  {
      <MyItemComponent @key="item.Id" Item="item" />
  }
  ```
- **Virtualization**: For rendering large lists of data, use the built-in `Virtualize` component. This component only renders the items currently visible to the user, significantly improving performance.
- **Minimize Re-renders**:
    - Override `ShouldRender()` in components to prevent unnecessary re-renders if the component's state or parameters haven't changed in a way that affects the UI.
    - Use primitive types or immutable objects for parameters where possible, as Blazor can more easily detect changes.
    - Be cautious with frequent state changes that might trigger cascading re-renders throughout the component tree. Batch updates if possible.

### Routing

- **`@page` Directive**: Use the `@page "/my-route"` directive at the top of a Razor component to make it a routable page.
- **Route Parameters**: Define route parameters using `{ParameterName}` syntax in the `@page` directive (e.g., `@page "/users/{UserId}"`). These parameters can be captured by component properties with the `[Parameter]` attribute.
- **`NavLink` Component**: Use the `NavLink` component instead of `<a>` tags for navigation links within the Blazor application. `NavLink` automatically adds an `active` CSS class to the link when its `href` matches the current URL.

### Forms and Validation

- **`EditForm` Component**: Use the `EditForm` component for handling form submissions and validation in Blazor.
- **Data Annotations**: Leverage data annotations (e.g., `[Required]`, `[StringLength]`) on your model properties for declarative validation. `EditForm` integrates with these annotations.
- **`DataAnnotationsValidator`**: Include the `<DataAnnotationsValidator/>` component inside your `EditForm` to enable validation based on data annotations.
- **`ValidationSummary` & `ValidationMessage`**: Use `<ValidationSummary />` to display a summary of all validation errors and `<ValidationMessage For="@(() => Model.PropertyName)" />` to display errors for specific fields.
- **Custom Validation**: Implement custom validation logic by creating custom `ValidationAttribute` classes or by handling the `OnSubmit` or `OnValidSubmit` events of `EditForm`.
- **User Feedback**: Provide clear and immediate feedback to the user about validation errors.

### Layouts

- **Layout Components**: Use layout components (inheriting from `LayoutComponentBase`) to define common UI structures (e.g., headers, footers, navigation menus) that are shared across multiple pages.
- **`@layout` Directive**: Specify the layout for a page or a set of pages using the `@layout MyLayoutType` directive in routable components or in an `_Imports.razor` file.
- **`Body` Property**: In a layout component, use `@Body` to specify where the content of individual pages should be rendered.
- **Organize Layouts**: Organize layouts logically, potentially having multiple layouts for different sections of the application (e.g., `MainLayout.razor`, `AdminLayout.razor`).

## Vue.js Specific Guidelines

### Component Structure

- **Single File Components (SFCs)**: Always use `.vue` files for components.
- **Directory Organization**: Group related components into logical subdirectories (e.g., `components/common`, `components/featureX`).
- **Naming Convention**: Use `PascalCase.vue` for component file names (e.g., `UserProfile.vue`, `BaseButton.vue`).
- **SFC Structure**: Follow this order within `.vue` files:
    1. `<template>`
    2. `<script setup lang="ts">` (or `<script lang="ts">` if not using `setup`)
    3. `<style scoped>`
    ```vue
    <template>
      <div>{{ message }}</div>
    </template>

    <script setup lang="ts">
    import { ref } from 'vue';
    const message = ref<string>('Hello, Vue!');
    </script>

    <style scoped>
    div {
      color: blue;
    }
    </style>
    ```

### Composition API

- **`<script setup>`**: This is the preferred way to use Composition API for its cleaner syntax and better TypeScript integration.
- **Reactivity**:
    - Use `ref()` for primitive types and for object properties that might be replaced.
    - Use `reactive()` for objects where you want deep reactivity on all properties.
- **Computed Properties**: Use `computed(() => { ... })` for deriving state from other reactive sources.
- **Watchers**:
    - `watch()`: Use for specific side effects when a particular reactive source changes. Clearly define the source and callback.
    - `watchEffect()`: Use for side effects that depend on multiple reactive sources, where Vue automatically tracks dependencies. Use with caution and ensure the effect is well-understood.
    - Provide clear justification in comments if the logic for watchers is complex.

### Props and Events

- **Props Definition**: Use `defineProps` with TypeScript for type safety.
  ```typescript
  // Inside <script setup lang="ts">
  interface Props {
    title: string;
    count?: number;
  }
  const props = defineProps<Props>();
  ```
- **Emits Definition**: Use `defineEmits` to declare emitted events and their potential payloads (with types).
  ```typescript
  // Inside <script setup lang="ts">
  const emit = defineEmits<{
    (e: 'update', value: string): void;
    (e: 'close'): void;
  }>();

  function handleClick() {
    emit('update', 'new value');
  }
  ```
- **Event Naming**: Emit events using kebab-case (e.g., `emit('item-selected')`). In templates, listen to these events using kebab-case (e.g., `@item-selected="handleSelection"`).

### State Management

- **Local State**: For state confined to a single component or a small, tightly coupled group of components, Composition API's `ref` and `reactive` are sufficient.
- **Pinia (Global State)**: For application-wide state or state shared across many unrelated components, use Pinia.
    - **Store Directory**: Define Pinia stores in a dedicated `src/stores` directory (e.g., `src/stores/userStore.ts`).
    - **Store Structure**: Clearly define state properties, getters (computed state), and actions (methods to modify state) within each store.
      ```typescript
      // src/stores/counterStore.ts
      import { defineStore } from 'pinia';
      import { ref, computed } from 'vue';

      export const useCounterStore = defineStore('counter', () => {
        const count = ref(0);
        const doubleCount = computed(() => count.value * 2);
        function increment() {
          count.value++;
        }
        return { count, doubleCount, increment };
      });
      ```

### TypeScript with Vue

- **Full TypeScript Usage**: Write all Vue-related code (components, composables, stores, router files) in TypeScript.
- **Type Definitions**: Provide explicit types for props, reactive state, computed properties, function parameters, return values, and event payloads.
- **`defineComponent` (Legacy)**: While `<script setup>` is preferred, if you encounter older code or specific scenarios not using `<script setup>`, `defineComponent` can help with type inference. However, new development should prioritize `<script setup>`.

### Routing (Vue Router)

- **Router Configuration**: Define routes in a dedicated `src/router/index.ts` file.
- **Lazy Loading**: Implement lazy loading for route components to improve initial load time.
  ```typescript
  // src/router/index.ts
  import { createRouter, createWebHistory } from 'vue-router';

  const routes = [
    {
      path: '/',
      name: 'Home',
      component: () => import('../views/HomeView.vue') // Lazy load
    },
    {
      path: '/about',
      name: 'About',
      component: () => import('../views/AboutView.vue') // Lazy load
    }
  ];

  const router = createRouter({
    history: createWebHistory(import.meta.env.BASE_URL),
    routes
  });

  export default router;
  ```
- **Named Routes**: Use named routes (e.g., `{ name: 'UserDetail', params: { id: userId } }`) for programmatic navigation as they are more resilient to URL changes.
- **Navigation Guards**: Use Vue Router's navigation guards (`beforeEach`, `beforeResolve`, `beforeEnter`) for implementing authentication, authorization, and other routing logic.

### Styling

- **Scoped Styles**: Use `<style scoped>` by default to ensure component styles do not leak and affect other components.
- **Global Styles**: If global styles are necessary (e.g., for base styling or utility classes), define them in a dedicated global CSS file (e.g., `src/assets/main.css`) and import it in your main application entry point (`main.ts`).
- **CSS Methodology (Optional)**: For larger projects, consider adopting a consistent CSS methodology like BEM (Block Element Modifier) or using a utility-first CSS framework (like Tailwind CSS, if adopted project-wide) to manage global styles and layout.

### Best Practices

- **Focused Components**: Design components to be small, focused on a single responsibility, and reusable.
- **`v-for` Keys**: Always provide a unique `:key` when using `v-for` to help Vue track and update list items efficiently.
  ```html
  <li v-for="item in items" :key="item.id">{{ item.name }}</li>
  ```
- **Avoid Direct DOM Manipulation**: Let Vue manage the DOM. Use refs (`ref="myElement"`) to access underlying DOM elements only when necessary (e.g., for focusing an input or integrating a third-party library that requires direct DOM access).
- **Unit Testing**: Write unit tests for components (props, events, slots, rendering logic) and composable functions (logic, reactivity). Frameworks like Vitest with Vue Test Utils are recommended.

## TypeScript Specific Guidelines

### Static Typing

- **Avoid `any`**: Strive to use specific types for all variables, function parameters, and return values. Avoid using `any` whenever possible.
  ```typescript
  // Bad
  function processData(data: any): any {
    // ...
    return data.processed;
  }

  // Good
  interface MyData {
    raw: string;
  }
  interface ProcessedData {
    processed: string;
  }
  function processData(data: MyData): ProcessedData {
    // ...
    return { processed: data.raw.toUpperCase() };
  }
  ```
- **Justify `any`**: If `any` is absolutely necessary (e.g., when dealing with highly dynamic data or third-party libraries without types), provide a clear comment explaining why a more specific type cannot be used.
- **`unknown` vs `any`**: Prefer `unknown` over `any` when the type of a value is truly unknown. `unknown` is type-safe because it requires type checking (e.g., using type guards, `instanceof`, or casting) before operations can be performed on the value.
  ```typescript
  function logValue(value: unknown) {
    if (typeof value === 'string') {
      console.log(value.toUpperCase());
    } else {
      console.log('Value is not a string:', value);
    }
  }
  ```

### Interfaces and Types

- **Interfaces for Objects**: Prefer `interface` for defining the shape of objects or for creating contracts that classes can implement.
  ```typescript
  interface User {
    id: number;
    name: string;
    email?: string; // Optional property
  }

  interface Point {
    readonly x: number; // Readonly property
    readonly y: number;
  }
  ```
- **Type Aliases for Complex Types**: Use `type` aliases for defining union types, intersection types, tuples, or more complex type shapes.
  ```typescript
  type UserId = string | number;
  type UserStatus = 'active' | 'inactive' | 'pending';
  type Coordinate = [number, number]; // Tuple
  type UserWithRoles = User & { roles: string[] }; // Intersection
  ```
- **Naming**: Use PascalCase for interface and type alias names (e.g., `ProductDetails`, `ApiResponse`).

### Enums

- **Named Constants**: Use `enum` for defining a set of named constants.
  ```typescript
  enum OrderStatus {
    Pending,    // 0
    Processing, // 1
    Shipped,    // 2
    Delivered   // 3
  }
  const orderStatus: OrderStatus = OrderStatus.Shipped;
  ```
- **String Enums**: Consider using string enums for better readability in logs and debugging, although they are slightly less performant than numeric enums.
  ```typescript
  enum LogLevel {
    Error = 'ERROR',
    Warning = 'WARNING',
    Info = 'INFO',
    Debug = 'DEBUG'
  }
  ```
- **Naming**: Use PascalCase for enum names and enum members (e.g., `PaymentMethod.CreditCard`).
- **`const enums`**: For performance-critical code paths, use `const enum`. `const enum` members are inlined at compile time, which can reduce bundle size and improve runtime performance. However, they have limitations (e.g., cannot be iterated over at runtime).
  ```typescript
  const enum Direction {
    Up,
    Down,
    Left,
    Right
  }
  let dir: Direction = Direction.Left; // Becomes 'let dir = 2;' in compiled JS
  ```

### Modules

- **ES6 Modules**: Organize code into modules using ES6 `import` and `export` syntax.
  ```typescript
  // utils.ts
  export function helperFunction() { /* ... */ }

  // main.ts
  import { helperFunction } from './utils';
  ```
- **Relative Paths**: Use relative paths (e.g., `./`, `../`) for imports within the same project/package.
- **Path Aliases (`tsconfig.json`)**: For larger projects, configure path aliases in `tsconfig.json` to simplify imports from commonly used directories (e.g., `@/components/*` mapping to `src/components/*`). Ensure these aliases are documented for other developers.
  ```json
  // tsconfig.json example
  {
    "compilerOptions": {
      "baseUrl": ".",
      "paths": {
        "@/*": ["src/*"]
      }
    }
  }
  ```
  ```typescript
  // Usage with path alias
  import MyComponent from '@/components/MyComponent';
  ```

### `tsconfig.json`

- **Strict Configuration**: Maintain a strict `tsconfig.json`. This helps catch errors early and improves code quality.
- **Key `compilerOptions`**:
    - `strict: true`: Enables all strict type-checking options. This is highly recommended. (Includes `noImplicitAny`, `strictNullChecks`, `strictFunctionTypes`, `strictBindCallApply`, `strictPropertyInitialization`, `noImplicitThis`, `useUnknownInCatchVariables`, `alwaysStrict`).
    - `noUnusedLocals: true`: Reports errors on unused local variables.
    - `noUnusedParameters: true`: Reports errors on unused parameters in functions.
    - `noImplicitReturns: true`: Ensures all code paths in a function return a value if the function is declared to return something.
    - `forceConsistentCasingInFileNames: true`: Ensures that file references use the same casing as the actual file names on disk.
    - `esModuleInterop: true`: Enables compatibility with CommonJS modules that use `module.exports`.
    - `skipLibCheck: true`: (Optional, but common) Skips type checking of declaration files (`.d.ts`), which can speed up compilation.
- **Target ECMAScript Version**: Set `target` (e.g., `ES2020`, `ES2021`, `ESNext`) based on the environments your code will run in (e.g., Node.js version, browser support).

### Null and Undefined

- **Explicit Handling**: Be explicit about `null` and `undefined` values.
- **`strictNullChecks`**: Enable this option in `tsconfig.json`. It forces you to handle potential `null` or `undefined` values.
- **Safe Access**:
    - **Optional Chaining (`?.`)**: Access properties or call methods on potentially null/undefined objects safely.
      ```typescript
      const userName = user?.profile?.name; // Returns undefined if user or profile is null/undefined
      ```
    - **Nullish Coalescing (`??`)**: Provide a default value if an expression is `null` or `undefined`.
      ```typescript
      const displayName = user?.name ?? 'Guest';
      ```
    - **Type Guards**: Write functions that check the type of a variable, narrowing it down for safer operations.
      ```typescript
      function isString(value: unknown): value is string {
        return typeof value === 'string';
      }
      ```

### Asynchronous Code

- **`async/await`**: Prefer `async/await` syntax for writing asynchronous code as it improves readability compared to raw Promises or callbacks.
- **Typed Promises**: Always type your Promises to specify the type of the resolved value.
  ```typescript
  async function fetchData(url: string): Promise<User[]> {
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    const data: User[] = await response.json();
    return data;
  }
  ```

### Readability and Maintainability

- **Utility Types**: Leverage TypeScript's built-in utility types to create new types from existing ones without repetitive definitions.
    - `Partial<T>`: Makes all properties of `T` optional.
    - `Readonly<T>`: Makes all properties of `T` readonly.
    - `Pick<T, K>`: Constructs a type by picking a set of properties `K` from `T`.
    - `Omit<T, K>`: Constructs a type by picking all properties from `T` and then removing `K`.
    - `Record<K, T>`: Constructs an object type whose property keys are `K` and property values are `T`.
- **Type Definition Location**:
    - For types used by a single module, define them within that module.
    - For types shared across multiple modules, consider a dedicated `types.ts`, `interfaces.ts`, or `models.ts` file within a relevant directory (e.g., `src/types`, `src/featureX/types`).

### Interfacing with JavaScript

- **DefinitelyTyped**: When using JavaScript libraries, first check for existing type definitions on DefinitelyTyped by trying to install the corresponding `@types/package-name` package (e.g., `npm install --save-dev @types/lodash`).
- **Custom Declaration Files (`.d.ts`)**: If type definitions are not available for a JavaScript library, or if you need to augment existing ones, create custom declaration files (e.g., `my-library.d.ts`).
  ```typescript
  // example.d.ts
  declare module 'some-js-library' {
    export function someFunction(param: string): number;
    export const someValue: boolean;
  }
  ```
  Ensure these `.d.ts` files are included in your `tsconfig.json`'s `include` array or are picked up by the compiler automatically.

## Node.js and npm Specific Guidelines

### `package.json`

- **Maintenance**: Keep `package.json` well-maintained, accurate, and organized.
- **Scripts**:
    - Use descriptive and consistent names for scripts in the `scripts` section (e.g., `dev`, `start:prod`, `build`, `test`, `lint`, `format`).
    - Ensure scripts are functional and up-to-date.
    ```json
    "scripts": {
      "dev": "nodemon src/index.ts",
      "build": "tsc -p .",
      "start": "node dist/index.js",
      "test": "jest",
      "lint": "eslint . --ext .ts"
    }
    ```
- **Dependencies**:
    - `dependencies`: List packages required for the application to run in production.
    - `devDependencies`: List packages used only for development and build processes (e.g., testing frameworks, bundlers, linters).
- **Metadata**: Include essential metadata:
    - `description`: A brief description of the project.
    - `author`: The author or organization.
    - `license`: Specify the project's license (e.g., "MIT", "ISC").
    - `version`: Follow Semantic Versioning (SemVer - `MAJOR.MINOR.PATCH`).
    - `main`: The entry point of the package (e.g., `dist/index.js`).
    - `repository`: Link to the source code repository.
    - `keywords`: Relevant keywords for discoverability.

### Dependency Management

- **Package Manager**: Assume `npm` is the project standard unless specified otherwise (alternatives like `yarn` or `pnpm` should be consistently used if chosen).
- **Clean Installs**: Use `npm ci` for installing dependencies in CI/CD pipelines or when you need a deterministic, clean install based strictly on `package-lock.json`. This is generally faster and safer for automated builds.
- **Adding Packages**: Use `npm install <package-name>` (or `npm install --save-dev <package-name>` for dev dependencies).
- **Updates & Audits**:
    - Regularly update dependencies using `npm update`.
    - Periodically run `npm audit` to check for known vulnerabilities in dependencies and `npm audit fix` to attempt automatic patching.
- **Semantic Versioning (SemVer)**:
    - Understand SemVer (`MAJOR.MINOR.PATCH`).
    - `^` (caret): Allows updates to the latest minor version (e.g., `^1.2.3` allows `1.3.0` but not `2.0.0`). This is the default for `npm install`.
    - `~` (tilde): Allows updates to the latest patch version (e.g., `~1.2.3` allows `1.2.4` but not `1.3.0`).
- **Local Dependencies**: Avoid global package installations (`npm install -g <package>`) for project dependencies. Prefer project-local installations to ensure version consistency and avoid conflicts. Global installs are generally for CLI tools.

### `package-lock.json`

- **Commit Lock File**: Always commit the `package-lock.json` (or `yarn.lock` / `pnpm-lock.yaml`) to version control. This file records the exact versions of all dependencies and sub-dependencies, ensuring reproducible builds across different environments and team members.

### `.npmrc`

- **Project-Specific Configuration**: Use a project-level `.npmrc` file (add it to the root of your project) for npm configurations that are specific to the project. Examples:
    - Specifying a private registry: `registry=https://my-private-registry.example.com`
    - Setting script execution defaults: `script-shell=bash`
- **Security**: **Do not commit sensitive information** (like authentication tokens for private registries) directly into the `.npmrc` file if it's version controlled. Use environment variables (e.g., `${NPM_TOKEN}`) in `.npmrc` which are then resolved from the environment.
  ```
  // .npmrc example
  @my-scope:registry=https://npm.pkg.github.com/
  //npm.pkg.github.com/:_authToken=${GITHUB_TOKEN}
  ```

### Node.js Version

- **`engines` Field**: Specify the required Node.js version(s) for the project in `package.json` using the `engines` field. This helps collaborators and CI environments use the correct Node.js version.
  ```json
  "engines": {
    "node": ">=18.0.0",
    "npm": ">=9.0.0" // Optionally specify npm version too
  }
  ```
- **Version Manager (NVM)**: Encourage the use of a Node.js version manager like NVM (Node Version Manager), `n`, or `asdf` to easily switch between different Node.js versions as required by various projects. Project-specific `.nvmrc` files can automate version switching with NVM.

### Environment Variables

- **Configuration**: Use environment variables for all configuration that varies between deployment environments (e.g., API keys, database connection strings, port numbers, logging levels).
- **`.env` Files**: For local development, use `.env` files (e.g., using the `dotenv` package) to load environment variables.
    - **Ensure `.env` is added to `.gitignore`** to prevent accidental commits of sensitive or environment-specific data.
    - Provide a `.env.example` or similar template file that is committed to the repository, showing the required variables.
- **Access**: Access environment variables in your Node.js application via `process.env.MY_VARIABLE_NAME`.

### Error Handling in Node.js

- **Asynchronous Errors**:
    - **Callbacks**: Always check for an `error` argument first in callback functions (`if (err) { /* handle error */ }`).
    - **Promises**: Always attach a `.catch(err => { /* handle error */ })` handler or use `try...catch` with `async/await`.
    - **`async/await`**: Use `try...catch` blocks to handle errors in `async` functions.
      ```typescript
      async function myAsyncFunction() {
        try {
          const result = await somePromise();
          // ...
        } catch (error) {
          console.error('Async operation failed:', error);
          // Propagate error or handle gracefully
        }
      }
      ```
- **Synchronous Errors**: Use standard `try...catch` blocks for synchronous code that might throw exceptions.
- **Uncaught Exceptions & Unhandled Rejections**: Implement global handlers for `process.on('uncaughtException', ...)` and `process.on('unhandledRejection', ...)` to log such errors and potentially perform a graceful shutdown. However, these should be seen as a last resort; specific error handling is preferred.
- **Graceful Shutdown**: Implement mechanisms to allow your application to shut down gracefully (e.g., close database connections, finish processing ongoing requests) when it receives signals like `SIGINT` (Ctrl+C) or `SIGTERM`.

### Security (Node.js)

- **Dependency Vulnerabilities**: Regularly audit dependencies (`npm audit`) and keep them updated.
- **Input Sanitization**: Sanitize and validate all external inputs (e.g., from HTTP requests, file reads, user inputs) to prevent injection attacks (e.g., SQL injection, NoSQL injection, command injection).
- **Shell Commands**: Be extremely cautious when executing shell commands (e.g., with `child_process.exec`). Avoid constructing commands from user input. If necessary, use `child_process.execFile` with static commands and pass user inputs as arguments.
- **File System Access**: Validate paths and permissions when performing file system operations. Be wary of path traversal vulnerabilities (`../../`).
- **Rate Limiting & Brute Force Protection**: Implement rate limiting on APIs to protect against brute-force attacks.
- **HTTPS**: Use HTTPS for all external communication.

### Scripts

- **`package.json` Scripts**: Define common development, build, test, and deployment tasks in the `scripts` section of `package.json`.
- **Platform Agnostic Scripts**:
    - Write scripts that can run on different operating systems (Windows, macOS, Linux).
    - Avoid OS-specific commands (e.g., `COPY` vs `cp`). Use Node.js-based tools or packages (e.g., `shx`, `cpx`) for cross-platform shell operations if needed.
    - Use tools like `cross-env` to set environment variables in scripts in a cross-platform way.
      ```json
      "scripts": {
        "start:dev": "cross-env NODE_ENV=development nodemon src/index.ts"
      }
      ```

## webpack Specific Guidelines

### Configuration Files

- **Environment Splitting**: Split webpack configuration into multiple files based on the environment (e.g., development, production, common).
    - `webpack.common.js`: Contains configuration shared by all environments (e.g., entry points, output structure, common loaders, common plugins).
    - `webpack.dev.js`: Contains development-specific configuration (e.g., `webpack-dev-server` settings, source map types for development, HMR).
    - `webpack.prod.js`: Contains production-specific configuration (e.g., minification, CSS extraction, performance optimizations, more aggressive code splitting).
    - Use `webpack-merge` to combine common configuration with environment-specific ones.
    ```javascript
    // webpack.dev.js example
    const { merge } = require('webpack-merge');
    const common = require('./webpack.common.js');

    module.exports = merge(common, {
      mode: 'development',
      devtool: 'inline-source-map',
      devServer: {
        static: './dist',
        hot: true,
      },
    });
    ```
- **Clarity and Comments**: Keep configuration files well-organized, clearly structured, and include comments to explain complex parts or important decisions.

### Loaders

- **Appropriate Loaders**: Use loaders to preprocess various file types.
    - **TypeScript**: `ts-loader` (integrates with `tsconfig.json`) or `babel-loader` (with `@babel/preset-typescript`).
    - **JavaScript (ES6+)**: `babel-loader` with appropriate presets (e.g., `@babel/preset-env`).
    - **Vue SFCs**: `vue-loader`.
    - **CSS**:
        - `style-loader`: Injects CSS into the DOM via `<style>` tags (good for development).
        - `css-loader`: Resolves `@import` and `url()` in CSS.
        - `postcss-loader`: For processing CSS with PostCSS plugins (e.g., Autoprefixer).
        - `sass-loader` / `less-loader`: For compiling SASS/LESS to CSS.
        - `mini-css-extract-plugin.loader`: Extracts CSS into separate files (use in production instead of `style-loader`).
- **Loader Configuration**:
    - Configure loaders within `module.rules`.
    - **`include` and `exclude`**: Use these properties to specify which files/directories loaders should or should not process. This can significantly improve build performance by avoiding unnecessary processing of `node_modules` or other irrelevant files.
      ```javascript
      module: {
        rules: [
          {
            test: /\.tsx?$/,
            use: 'ts-loader',
            exclude: /node_modules/,
          },
        ],
      }
      ```

### Plugins

- **Essential Plugins**:
    - `HtmlWebpackPlugin`: Generates HTML files to serve your bundles (e.g., `index.html`), automatically injecting script and link tags. Useful for SPAs.
    - `MiniCssExtractPlugin`: Extracts CSS from JavaScript bundles into separate `.css` files. Essential for production builds to enable CSS caching.
    - `DefinePlugin`: Allows you to create global constants that can be configured at compile time (e.g., `process.env.NODE_ENV`).
    - `ProvidePlugin`: Automatically loads modules instead of having to `import` or `require` them everywhere. Use sparingly (e.g., for polyfills or widely used utilities like jQuery, though modern practices often avoid this).
- **Selective Use**: Only add plugins that provide clear benefits to the build process, performance, or development experience. Avoid "plugin bloat."

### Output Management

- **Output Configuration**:
    - `output.path`: Specifies the directory for all output files.
    - `output.filename`: Defines the naming pattern for entry bundles. Use placeholders for dynamic names:
        - `[name]`: Replaced by the name of the chunk.
        - `[contenthash]`: Replaced by a hash of the content of the chunk. This is crucial for long-term caching (cache busting).
        - `[chunkhash]`: Similar to `contenthash`, but specific to chunks.
      ```javascript
      output: {
        filename: 'js/[name].[contenthash].bundle.js',
        path: path.resolve(__dirname, 'dist'),
      }
      ```
- **Cleaning Build Directory**:
    - Webpack 5+: Use `output.clean = true;` to automatically clean the output directory before each build.
    - Older versions: Use `clean-webpack-plugin`.

### Development vs. Production Builds

- **`mode`**: Set `mode: 'development'` or `mode: 'production'` in respective configurations. This enables webpack's built-in optimizations for each environment.
- **Development (`mode: 'development'`)**:
    - **Build Speed**: Prioritize fast rebuilds.
    - **Debugging**: Include comprehensive source maps (e.g., `devtool: 'eval-source-map'` or `devtool: 'inline-source-map'`).
    - **`webpack-dev-server`**: Use for live reloading and Hot Module Replacement (HMR). Configure `devServer` options appropriately.
    - **HMR**: Enable Hot Module Replacement for faster updates without full page reloads, especially for CSS and JavaScript frameworks.
- **Production (`mode: 'production'`)**:
    - **Bundle Size & Performance**: Focus on creating small, optimized bundles.
    - **Minification**:
        - JavaScript: TerserWebpackPlugin is included by default when `mode: 'production'` to minify JS (`optimization.minimize: true`).
        - CSS: Use `CssMinimizerWebpackPlugin` to minify CSS files.
    - **CSS Extraction**: Use `MiniCssExtractPlugin` to extract CSS into separate files.
    - **Code Splitting**: Implement aggressive code splitting.
    - **Tree Shaking**: Enabled by default in production mode for ES modules.

### Code Splitting

- **Purpose**: Break down large bundles into smaller chunks that can be loaded on demand or in parallel, improving initial load time.
- **Dynamic Imports (`import()`)**: Use the `import()` syntax (ECMAScript proposal) to dynamically load modules. This is ideal for route-based splitting (loading code only for the current route) or component-based splitting.
  ```javascript
  // Example: Lazy loading a component
  const MyComponent = () => import(/* webpackChunkName: "my-component" */ './MyComponent');
  ```
- **`optimization.splitChunks`**: Configure webpack's `splitChunks` optimization to automatically split vendor code (from `node_modules`) and common modules into separate chunks.
  ```javascript
  optimization: {
    splitChunks: {
      chunks: 'all', // Apply to all chunks (initial, async)
    },
  }
  ```

### Tree Shaking

- **Purpose**: Eliminates unused code (dead code) from bundles.
- **Requirements**:
    - Use ES6 modules (`import` and `export` syntax).
    - Set `sideEffects: false` in your project's `package.json` to indicate that your project's modules have no side effects when imported (unless specific files are listed). This allows webpack to more aggressively prune unused exports.
      ```json
      // package.json
      {
        "name": "my-project",
        "sideEffects": ["./src/some-file-with-side-effects.js"]
      }
      // or for all files if no side effects
      {
        "name": "my-project",
        "sideEffects": false
      }
      ```
- Webpack handles tree shaking automatically in production mode for ES modules.

### Asset Handling

- **Asset Modules (webpack 5+)**: Use asset modules to handle static assets like images, fonts, etc., without needing additional loaders for common cases.
    - `asset/resource`: Emits a separate file and exports the URL (replaces `file-loader`).
    - `asset/inline`: Inlines an asset as a data URI (replaces `url-loader` when asset size is small).
    - `asset/source`: Exports the source code of the asset (replaces `raw-loader`).
    - `asset` (generic): Automatically chooses between `asset/resource` and `asset/inline` based on `parser.dataUrlCondition.maxSize`.
  ```javascript
  module: {
    rules: [
      {
        test: /\.(png|svg|jpg|jpeg|gif)$/i,
        type: 'asset/resource',
        generator: {
          filename: 'images/[name].[contenthash][ext]'
        }
      },
      {
        test: /\.(woff|woff2|eot|ttf|otf)$/i,
        type: 'asset/resource',
        generator: {
          filename: 'fonts/[name].[contenthash][ext]'
        }
      }
    ]
  }
  ```
- **Image Optimization**: Consider using `image-webpack-loader` (or similar) to optimize images (compressing them) during the build process, reducing bundle sizes.

### Bundle Analysis

- **`webpack-bundle-analyzer`**: Use this plugin to generate a visual representation of your webpack output files. It helps you understand what's inside your bundles, identify large modules, and find opportunities for optimization.
  ```javascript
  // In webpack.config.js (often for a specific analysis script)
  const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;
  module.exports = {
    plugins: [
      new BundleAnalyzerPlugin() // Add conditionally or via a separate config
    ]
  };
  ```

### Performance

- **Build Times**:
    - Use `include`/`exclude` in loaders.
    - Use `cache: true` for loaders that support it (e.g., `babel-loader`).
    - Consider `thread-loader` for parallelizing expensive loader operations (use with caution, can have overhead).
    - Keep webpack and loader versions up-to-date.
- **Bundle Sizes**:
    - Implement code splitting and tree shaking.
    - Minify JS and CSS.
    - Optimize images.
    - Analyze bundles to remove unnecessary dependencies or large modules.

### Environment Variables in Build

- **`DefinePlugin`**: Use `webpack.DefinePlugin` to make environment variables available to your frontend code.
  ```javascript
  new webpack.DefinePlugin({
    'process.env.NODE_ENV': JSON.stringify(process.env.NODE_ENV || 'development'),
    'API_ENDPOINT': JSON.stringify(process.env.API_ENDPOINT || 'https://api.example.com')
  })
  ```
- **`EnvironmentPlugin`**: A shorthand for `DefinePlugin` for `process.env` variables.
  ```javascript
  new webpack.EnvironmentPlugin(['NODE_ENV', 'API_ENDPOINT'])
  ```
- **Security**: Be very careful not to expose sensitive information (like private API keys) to the client-side bundle. Environment variables injected this way become part of the frontend JavaScript. Use these for non-sensitive configuration like public API URLs or feature flags.

## Testing Guidelines

### General Principles

- **Test New Code**: All new features and bug fixes must be accompanied by tests.
- **Reliable, Readable, Maintainable**: Tests should be:
    - **Reliable**: Produce consistent results (not flaky).
    - **Readable**: Easy to understand what is being tested and why.
    - **Maintainable**: Easy to update when the underlying code changes.
- **Coverage vs. Criticality**: Aim for good test coverage, but prioritize testing critical application paths, complex logic, and common user scenarios over achieving a specific percentage.
- **Arrange-Act-Assert (AAA)**: Structure your tests using the AAA pattern:
    - **Arrange**: Set up the test conditions (e.g., initialize objects, mock dependencies).
    - **Act**: Execute the code under test.
    - **Assert**: Verify the outcome is as expected.
- **Independence**: Tests should be independent of each other and runnable in any order. Avoid creating dependencies between tests.

### Types of Tests

- **Unit Tests**:
    - **Focus**: Test the smallest individual units of code (e.g., a single method, class, component, or function) in isolation from other parts of the system.
    - **Mocking**: External dependencies (e.g., services, database, API calls) should be mocked or stubbed to ensure the unit is tested in isolation.
    - **ASP.NET Core**:
        - **Frameworks**: Use xUnit, NUnit, or MSTest.
        - **Mocking Libraries**: Use Moq, NSubstitute, or similar.
        - **What to Test**: Services, helper/utility classes, model logic, validation logic.
    - **Vue.js/TypeScript**:
        - **Frameworks**: Use Vitest (preferred for Vite-based projects) or Jest.
        - **Utilities**: Use Vue Test Utils for mounting and interacting with Vue components.
        - **What to Test**: Component props, events, slots, computed properties, internal methods, lifecycle hooks. Also test utility functions, composables, and Pinia store logic (getters, actions, mutations/state changes).

- **Integration Tests**:
    - **Focus**: Test the interaction and communication between different parts of the application (e.g., service to database, component to service, API endpoint to business logic).
    - **ASP.NET Core**:
        - **Framework**: Use `WebApplicationFactory<TEntryPoint>` (from `Microsoft.AspNetCore.Mvc.Testing`) for in-memory testing of your API endpoints, including middleware, routing, authentication/authorization, and service integrations.
        - **Database**: Use a test database (e.g., in-memory SQLite, a dedicated test SQL Server instance, or Dockerized database) or mock the data access layer for controlled scenarios.
    - **Frontend (Vue.js/TypeScript)**:
        - **Focus**: Test interactions between multiple components, data flow through props and events, integration with state management (e.g., Pinia stores), and interactions with API service layers (usually by mocking the HTTP responses from the backend).

- **End-to-End (E2E) Tests**:
    - **Focus**: Test the entire application flow from the user's perspective, simulating real user scenarios in a browser or browser-like environment.
    - **Tools**: Use frameworks like Playwright, Cypress, or Selenium.
    - **Scenarios**: Focus on critical user journeys, core functionalities, and workflows (e.g., user login, form submission, key feature interactions).
    - **Considerations**: E2E tests are typically slower, more resource-intensive, and can be more brittle (prone to breaking due to UI changes). Use them judiciously for the most important end-to-end flows.

### Test Naming Conventions

- **Clarity**: Test names should be descriptive and clearly indicate what scenario is being tested and what the expected outcome is.
- **Common Pattern**: `MethodOrComponentUnderTest_Scenario_ExpectedBehavior`
    - **C# Example (xUnit/NUnit)**:
      ```csharp
      public class CalculatorServiceTests
      {
          [Fact] // or [Test] for NUnit
          public void Add_WithTwoPositiveNumbers_ReturnsCorrectSum()
          {
              // Arrange
              var calculator = new CalculatorService();
              // Act
              var result = calculator.Add(2, 3);
              // Assert
              Assert.Equal(5, result);
          }
      }
      ```
    - **TypeScript/JavaScript Example (Vitest/Jest)**:
      ```typescript
      // MyComponent.spec.ts
      describe('MyComponent', () => {
        it('should display the correct title when title prop is passed', () => {
          // Arrange
          const wrapper = mount(MyComponent, { props: { title: 'Test Title' } });
          // Act
          const titleElement = wrapper.find('h1');
          // Assert
          expect(titleElement.text()).toBe('Test Title');
        });
      });
      ```

### Test Organization

- **C# (ASP.NET Core)**:
    - Create separate test projects for unit tests and integration tests (e.g., `MyProject.UnitTests`, `MyProject.IntegrationTests`).
    - Test project folder structure should generally mirror the main project's structure.
- **TypeScript/JavaScript (Vue.js)**:
    - **Unit/Integration Tests**: Place test files (`.spec.ts` or `.test.ts`) alongside the code they are testing (e.g., `Button.vue` and `Button.spec.ts` in the same directory) or within a `__tests__` subdirectory in the component's folder.
    - **E2E Tests**: Typically reside in a separate top-level directory (e.g., `e2e/`, `tests/e2e/`, or `cypress/integration/`).

### Running Tests

- **Instructions**: Provide clear, documented instructions on how to run all types of tests.
    - **C#**: `dotnet test` in the solution or test project directory.
    - **Frontend**: `npm test`, `npm run test:unit`, `npx vitest run`, `npx cypress run`, etc., as defined in `package.json` scripts.
- **CI/CD Integration**: All tests (unit, integration, and ideally E2E if feasible) must be integrated into the Continuous Integration/Continuous Deployment (CI/CD) pipeline. Code should only be merged if all tests pass.

### Mocking and Stubbing

- **Purpose**: Isolate the unit under test by replacing its dependencies with controlled test doubles (mocks, stubs, fakes).
- **Guidelines**:
    - Clearly define when to mock (e.g., external services, database, things that make tests slow or non-deterministic).
    - Use established mocking libraries (Moq/NSubstitute for C#, Vitest/Jest's built-in mocking features for JS/TS).
    - **Avoid Over-Mocking**: Mocking too much can lead to tests that are tightly coupled to the implementation details of the unit under test, making them brittle. Test the behavior, not just the implementation.
    - Focus on mocking the interactions at the boundary of your unit/service.

### Code Coverage

- **Target**: Aim for a reasonable code coverage target (e.g., 70-80% lines/branches). However, high code coverage alone does not guarantee test quality.
- **Quality over Quantity**: Focus on writing meaningful tests for critical and complex parts of the application, rather than just chasing a percentage.
- **Tools**: Use code coverage tools to measure and report coverage.
    - **C#**: Coverlet, AltCover.
    - **JS/TS**: Vitest/Jest have built-in coverage capabilities (often using Istanbul).
- **Review**: Periodically review coverage reports to identify untested areas of the codebase.
