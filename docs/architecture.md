# React Web App Architecture: Grouping by File Type (REST API & TypeScript Reference)

This document defines the architecture and folder structure for a React web application, employing the "Grouping by File Type" pattern. It emphasizes integration with REST APIs for data operations and leverages TypeScript for robust type safety.

---

## Core React Architecture Principles

This "Grouping by File Type" structure adheres to fundamental React architectural principles:

* **Component-Based Design:** UI is broken down into independent, reusable components (`components/`, `pages/`).

* **Separation of Concerns:** Responsibilities are clearly delineated: UI (`components/`), business logic (`hooks/`), API communication (`services/`), and general helpers (`utils/`).

* **Modularity:** Each directory serves as a distinct module for a specific code type.

* **Maintainability:** Categorization by file type facilitates quick location and modification of application parts, including the API interaction layer.

* **Type Safety (with TypeScript):** Static type checking reduces runtime errors, improves readability, and enhances developer tooling.

---

## Mandatory Folder Structure (`src/`)

The `src/` directory organizes the application's source code. TypeScript files use `.ts` and `.tsx` extensions.

src/
├── assets/
├── components/
├── hooks/
├── pages/
├── services/
├── utils/
├── styles/
├── types/
├── App.tsx
└── index.tsx

---

### 1. `assets/`

* **Purpose:** Stores static media files and global resources directly served to the browser or used application-wide.

* **Content:**
    * `images/`: PNG, JPEG, SVG images.
    * `fonts/`: Custom font files.
    * `icons/`: Global SVG icons (if not using an icon library or inline SVGs within components).
    * Other static files (e.g., `favicon.ico`).

* **Architectural Role:** Centralizes non-code assets, maintaining clean code directories and improving asset management.

* **Example:**
    ```
    src/assets/
    ├── images/
    │   └── logo.png
    ├── fonts/
    │   └── Inter-Regular.ttf
    └── icons/
        └── search.svg
    ```

---

### 2. `components/`

* **Purpose:** Houses reusable, presentational UI components. These components are "dumb" (receive data via props, render UI, minimal internal state/logic). They are the fundamental building blocks of the UI.

* **Content:**
    * Atomic UI elements: `Button.tsx`, `Input.tsx`, `Checkbox.tsx`, `Avatar.tsx`.
    * Simple composite components: `Card.tsx`, `Modal.tsx`, `Dropdown.tsx`, `Navbar.tsx`.
    * Each component often has its own subfolder for the component file, specific styles, and tests.

* **Architectural Role:** Fosters **component-based design** and **reusability**. Components receive data from `pages/` or `hooks/`, which interact with `services/` for API data.

* **TypeScript Consideration:** `Props` interfaces define type safety for component inputs.

* **Example:**
    ```typescript
    src/components/
    ├── Button/
    │   ├── Button.tsx
    │   └── Button.module.css (or Button.css/Button.styled.ts)
    ├── Modal/
    │   ├── Modal.tsx
    │   └── Modal.module.css
    └── Card/
        └── Card.tsx
    ```

---

### 3. `hooks/`

* **Purpose:** Contains custom React hooks. These encapsulate reusable logic, stateful behavior, or side effects shared across components. This is a common integration point with `services/` for data fetching and mutation.

* **Content:**
    * **Data fetching hooks:** `useFetchData.ts` (e.g., `usePosts`, `useUserDetails`), internally calling `services/` functions.
    * **Form handling hooks:** `useForm.ts`, `useValidation.ts`, potentially triggering `services/` functions for form submission.
    * Utility hooks: `useDebounce.ts`, `useLocalStorage.ts`, `useMediaQuery.ts`.

* **Architectural Role:** Enhances **separation of concerns** by extracting logic from components. Promotes **reusability** of logic. Acts as an intermediary between UI components and the `services/` layer for data management.

* **TypeScript Consideration:** Types define hook parameters, return values, and internal state for correctness.

* **Example:**
    ```typescript
    src/hooks/
    ├── useAuth.ts         // Calls authService.login()
    ├── useFetchPosts.ts   // Calls postService.getPosts()
    └── useSubmitForm.ts   // Calls formService.submitForm()
    ```

---

### 4. `pages/`

* **Purpose:** Houses top-level components representing distinct application views or routes. These compose components from `components/` and consume data from `hooks/` (which interact with `services/`) or directly call `services/` functions for page-specific data.

* **Content:**
    * `HomePage.tsx`
    * `DashboardPage.tsx`
    * `LoginPage.tsx`
    * `ProductDetailPage.tsx`

* **Architectural Role:** Maps URLs/routes to main components for rendering views. These are "container" components that manage dynamic **state**, orchestrating data and user interactions. They orchestrate display of data fetched via APIs.

* **TypeScript Consideration:** Interfaces define page-specific props or state.

* **Example:**
    ```typescript
    src/pages/
    ├── HomePage.tsx        // Uses useFetchPosts() to display posts
    ├── DashboardPage.tsx   // Uses useUserDetails() for user info
    └── Auth/
        └── LoginPage.tsx   // Handles login form submission via useAuth() or directly authService.login()
    ```

---

### 5. `services/`

* **Purpose:** **Critical layer for all REST API interactions.** Modules responsible for HTTP requests (GET, POST, PUT, DELETE), data fetching, and data uploading. These are pure JavaScript/TypeScript functions abstracting API call details.

* **Content:**
    * `authService.ts`: Functions for user login (`POST /login`), registration (`POST /register`), logout (`POST /logout`).
    * `apiClient.ts`: Generic HTTP client (e.g., `Workspace` or `axios`) with base URLs, headers (auth tokens), and error handling. Other services use this client.
    * `userService.ts`: Functions for fetching user profiles (`GET /users/:id`), updating user data (`PUT /users/:id`), uploading user avatars (`POST /users/:id/avatar`).
    * `productService.ts`: Functions for fetching product lists (`GET /products`), adding new products (`POST /products`), updating products (`PUT /products/:id`).

* **Architectural Role:** Enforces strong **separation of concerns** by isolating data access and external communication logic from UI and business logic. This simplifies API changes, data source swaps, and caching. It is the single source of truth for frontend-backend communication.

* **TypeScript Consideration:** Crucial for defining interfaces for API request payloads, response data, and error structures, ensuring type safety for data exchange.

* **Example:**
    ```typescript
    src/services/
    ├── apiClient.ts     // Configures fetch/axios, handles auth headers
    ├── authService.ts   // login, register, logout API calls
    ├── userService.ts   // getUser, updateUser, uploadAvatar API calls
    └── productService.ts // getProducts, createProduct, updateProduct API calls
    ```

---

### 6. `utils/`

* **Purpose:** Contains pure utility functions, helper classes, constants, or shared configurations not specific to React components, hooks, or services. These are small, independent functions for common tasks.

* **Content:**
    * `formatters.ts`: Date/currency formatting.
    * `validators.ts`: Input validation functions.
    * `constants.ts`: Global constants (e.g., API base URLs, error messages).
    * `helpers.ts`: Miscellaneous helper functions.

* **Architectural Role:** Promotes **reusability** for common, non-domain-specific logic. Organizes functions for easy discoverability. May contain functions to transform API data or prepare data for uploads.

* **TypeScript Consideration:** Types define function parameters and return values for correct usage.

* **Example:**
    ```typescript
    src/utils/
    ├── formatters.ts
    ├── validators.ts
    └── constants.ts // e.g., export const API_BASE_URL = '[https://api.yourapp.com](https://api.yourapp.com)';
    ```

---

### 7. `styles/`

* **Purpose:** Manages global styles, theme definitions, or shared styling configurations.

* **Content:**
    * `global.css` (or `index.css`): Global CSS resets, base styles.
    * `variables.css` (or `theme.ts` for CSS-in-JS): CSS variables or theme object.
    * `typography.css`: Global font styles.

* **Architectural Role:** Centralizes styling concerns, maintaining consistent visual identity.

* **Example:**
    ```
    src/styles/
    ├── global.css
    └── theme.ts
    ```

---

### 8. `types/`

* **Purpose:** Defines global TypeScript interfaces, types, enums, and utility types used across the application.

* **Content:**
    * `common.ts`: General-purpose types (e.g., `LoadingState`, `ApiResponse`).
    * `user.ts`: User-related types (e.g., `User`, `UserProfile`).
    * `product.ts`: Product-related types (e.g., `Product`, `Category`).
    * `api.ts`: Shared API response structures or error interfaces.

* **Architectural Role:** Centralized location for global type definitions, improving type consistency and discoverability.

* **TypeScript Consideration:** Essential for a robust and scalable type system.

* **Example:**
    ```typescript
    src/types/
    ├── common.ts
    ├── user.ts
    └── product.ts
    ```

---

### 9. `App.tsx`

* **Purpose:** The main root component. Sets up global layouts, routing, and potentially global context providers. Orchestrates the top-level application structure.

* **Content:**
    * React Router setup.
    * Layout components (e.g., `Header`, `Footer`).
    * Global context providers (e.g., `AuthProvider`, `ThemeProvider`).

* **Architectural Role:** High-level entry point for UI structure, defining how pages and global elements are composed.

* **TypeScript Consideration:** Uses `.tsx` as a React component with JSX.

---

### 10. `index.tsx`

* **Purpose:** Primary entry point for the application. Renders the root `App` component into the HTML DOM.

* **Content:**
    * `ReactDOM.createRoot().render()` call.
    * Imports for `App.tsx` and global styles.

* **Architectural Role:** Initial bootstrap file for React application rendering.

* **TypeScript Consideration:** Uses `.tsx` extension.

---

## Benefits of Grouping by File Type (REST API & TypeScript)

* **Simplicity:** Easy to understand and implement for smaller teams.

* **Clear Categorization:** Quick identification of file types by location.

* **Dedicated API Layer:** `services/` centralizes API communication for easy management and debugging.

* **Enhanced Type Safety:** TypeScript reduces runtime errors, improves code quality, and provides superior tooling.

* **Improved Developer Experience:** Clear types facilitate understanding of data structures and function signatures.

* **Suitable for Small/Medium Projects:** Reduces overhead and complexity.

---

## Considerations and Best Practices

* **Scalability Limitations:** Large projects may find `components/`, `hooks/`, `services/`, or `types/` folders unwieldy.

* **Colocation:** Consider colocating component-specific styles/tests within their component's subfolder (e.g., `components/Button/Button.tsx` and `components/Button/Button.css`).

* **Naming Conventions:** Maintain consistent naming (e.g., PascalCase for components, camelCase for hooks/utilities, `[feature]Service.ts`, `I[Type]` or `[Type]Type` for interfaces/types).

* **Absolute Imports:** Configure build system and `tsconfig.json` for absolute imports (e.g., `import Button from 'components/Button'`).

* **Error Handling in Services:** Implement robust, typed error handling in `services/` for API failures.

* **Data Transformation:** Transform API data structures within `services/` or `utils/` before passing to components, ensuring type conversions.

* **Refactor as Project Grows:** Be prepared to transition to a feature-based structure if the project's complexity increases.

* **`tsconfig.json`:** Ensure correct configuration, including `jsx: "react-jsx"` (or `"react"`), `esModuleInterop`, and appropriate `target`/`module` settings.