# Building a Invoicer app with Refine and Deploying It on DigitalOcean

## Introduction

In this tutorial, we will build a B2B React Invoicer application with [Refine Framework](https://github.com/refinedev/refine) and deploy it to [DigitalOcean App Platform](https://www.digitalocean.com/products/app-platform/).

At the end of this tutorial, we'll have a Invoicer application that includes:

- Accounts pages to list, create, edit and show accounts.
- Clients pages to list, create, edit and show clients.
- Invoice pages to list, create, edit, show and export as PDF

While doing these, we'll use the:

In this project, we will use the following technologies:

- [Strapi cloud-based headless CMS](https://strapi.io/cloud) to store our data. To fetch data from Strapi, we'll use the built-in Strapi [data-provider](https://refine.dev/docs/data/packages/strapi-v4/) of Refine. We have already set up the API with Strapi to concentrate on the frontend aspect of our project. You can access the Strapi API at this URL: [https://api.strapi-invoice.refine.dev](https://api.strapi-invoice.refine.dev)
- [Ant Design](https://ant.design/) UI library.
- Once we've built the app, we'll put it online using [DigitalOcean's App Platform](https://www.digitalocean.com/). This service makes it really easy and fast to set up, launch, and grow apps and static websites. You can deploy code by simply pointing to a GitHub repository and let App Platform do the heavy lifting of managing the infrastructure, app runtimes, and dependencies.

You can get the final source code of the application on [GitHub](https://github.com/refinedev/refine/master/examples/invoicer).

[slideshow https://refine.ams3.cdn.digitaloceanspaces.com/blog/invoicer/accounts-list.png https://refine.ams3.cdn.digitaloceanspaces.com/blog/invoicer/accounts-new.png https://refine.ams3.cdn.digitaloceanspaces.com/blog/invoicer/accounts-edit.png https://refine.ams3.cdn.digitaloceanspaces.com/blog/invoicer/clients-list.png https://refine.ams3.cdn.digitaloceanspaces.com/blog/invoicer/clients-new.png https://refine.ams3.cdn.digitaloceanspaces.com/blog/invoicer/clients-edit.png https://refine.ams3.cdn.digitaloceanspaces.com/blog/invoicer/invoices-list.png https://refine.ams3.cdn.digitaloceanspaces.com/blog/invoicer/invoices-new.png https://refine.ams3.cdn.digitaloceanspaces.com/blog/invoicer/invoices-show.png]

## Prerequisites

- A local development environment for Node.js. You can follow the [How To Install Node.js and Create a Local Development Environment](https://www.digitalocean.com/community/tutorial-series/how-to-install-node-js-and-create-a-local-development-environment).
- Some preliminary knowledge of React and TypeScript. You can follow the [How To Code in React.js](https://www.digitalocean.com/community/tutorial-series/how-to-code-in-react-js) and [Using TypeScript with React](https://www.digitalocean.com/community/tutorials/react-typescript-with-react) series.
- A GitHub account to host the code.
- A DigitalOcean account. You can create one for free if you don't have one already.

## Step 1 — What is Refine?

[Refine](https://refine.dev/) is a React meta-framework for building data-intensive B2B CRUD web applications like internal tools, dashboards, and admin panels. It ships with various hooks and components to reduce the development time and increase the developer experience.

It is designed to build production-ready enterprise B2B apps. Instead of starting from scratch, it provides essential hooks and components, to help with tasks such as data & state management, handling authentication, and managing permissions.

So, you can focus on building the important parts of your app without getting bogged down in the technical stuff.

Refine is particularly effective in situations where managing data is key, such as:

- **Internal tools**:
- **Dashboards**:
- **Admin panels**:
- **All type of CRUD apps**

### Customization and styling

Refine's headless architecture allows the flexibility to use any UI library or custom CSS. Additionally, it has built-in support for popular open-source UI libraries, including Ant Design, Material UI, Mantine, and Chakra UI.

## Step 2 — Setting Up the Project

### Creating a New Refine App

We'll use the `npm create refine-app` command to interactively initialize the project.

```bash
npm create refine-app@latest
```

Select the following options when prompted:

```bash
✔ Choose a project template · Vite
✔ What would you like to name your project?: · refine-invoicer
✔ Choose your backend service to connect: · Strapi v4
✔ Do you want to use a UI Framework?: · Ant Design
✔ Do you want to add example pages?: · No
✔ Choose a package manager: · npm
```

Once the setup is complete, navigate to the project folder and start your app with:

```bash
npm run dev
```

Open `http://localhost:5173` in your browser to see the app.

![Welcome Page](https://refine.ams3.cdn.digitaloceanspaces.com/blog/invoicer/welcome.jpeg)

---

### Installing the 3rd Party Libraries

Our project is already set up with the most of needed libraries. But to create the Invoicer app, we need to install the following libraries:

- [`react-input-mask`](https://github.com/sanniassin/react-input-mask): To format the input fields. We will use this library to format the phone number field.
- [`antd-style`](https://github.com/ant-design/antd-style): css-in-js solution for Ant Design. We will use this library to customize the Ant Design components.
- `vite-tsconfig-paths`: This plugin allows Vite to resolve imports using TypeScript's path mapping.

To install these libraries, you can run the following command:

```bash
npm install react-input-mask antd-style
```

Also we need to install the types:

```bash
npm install @types/react-input-mask vite-tsconfig-paths --save-dev
```

In this project we will use typescript path mapping to import the files more easy to read and maintain. For example, instead of `import { MyComponent } from "../../components/MyComponent"`, we will use `import { MyComponent } from "@/components/MyComponent"`. To do this, we need to add the following code to the `tsconfig.jsons` file:

[details Show `tsconfig.json` code

```json
{
  "compilerOptions": {
    "target": "ESNext",
    "useDefineForClassFields": true,
    "lib": ["DOM", "DOM.Iterable", "ESNext"],
    "allowJs": false,
    "skipLibCheck": true,
    "esModuleInterop": false,
    "allowSyntheticDefaultImports": true,
    "strict": true,
    "forceConsistentCasingInFileNames": true,
    "module": "ESNext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",
    <^>"baseUrl": "./src",<^>
    <^>"paths": {<^>
      <^>"@/*": ["./*"]<^>
    <^>}<^>
  },
  "include": ["src", "vite.config.ts"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

]

Also, we need to update the `vite.config.ts` file to use the `vite-tsconfig-paths` plugin. This plugin allows Vite to resolve imports using TypeScript's path mapping.

[details Show `vite.config.ts` code

```typescript
import react from "@vitejs/plugin-react";
import { defineConfig } from "vite";
import tsconfigPaths from "vite-tsconfig-paths";

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [tsconfigPaths({ root: __dirname }), react()],
  build: {
    rollupOptions: {
      output: {
        manualChunks: {
          antd: ["antd"],
        },
      },
    },
  },
});
```

]

### Getting the necessary Components and Helper Functions from Refine Repository

Here is the finalized version of what we'll be building in this article:

https://github.com/refinedev/refine/tree/master/examples/invoicer

Before we move on, you need to add required components, styles and helper functions to the project if you want build the app by following the article.

First, Please remove the following files and folders from the project:

- `src/components` folder.
- `src/authProvider.ts` file.
- `src/contants.ts` file.

Then, you can copy the following files and folders from the Refine repository to the project:

- components: https://github.com/refinedev/refine/tree/master/examples/invoicer/src/components
- providers: https://github.com/refinedev/refine/tree/master/examples/invoicer/src/providers
- utils: https://github.com/refinedev/refine/tree/master/examples/invocer/src/utils
- types: https://github.com/refinedev/refine/tree/master/examples/invoicer/src/types
- styles: https://github.com/refinedev/refine/tree/master/examples/invoicer/src/styles

After removing the unnecessary files and folders and adding the necessary files and folders, the project structure should look like this:

![Project Structure](https://refine.ams3.cdn.digitaloceanspaces.com/blog/invoicer/file-structure-1.png)

In the next sections we will use this components and helper functions to create the "accounts", "clients", and "invoices" pages.

### Updating the `App.tsx` File

Now, your `App.tsx` files gives an error because we removed imported `authProvider.ts` and `constants.ts` files. Let's fix this by updating the `App.tsx` file.

[details Show `App.tsx` code

```typescript
import { Refine } from "@refinedev/core";
import { useNotificationProvider } from "@refinedev/antd";
import routerProvider, {
  UnsavedChangesNotifier,
  DocumentTitleHandler,
} from "@refinedev/react-router-v6";
import { BrowserRouter, Routes } from "react-router-dom";
import { DevtoolsPanel, DevtoolsProvider } from "@refinedev/devtools";
import { App as AntdApp } from "antd";
import { dataProvider } from "@/providers/data-provider";
import { authProvider } from "@/providers/auth-provider";
import { ConfigProvider } from "@/providers/config-provider";
import "@refinedev/antd/dist/reset.css";
import "./styles/custom.css";

const App: React.FC = () => {
  return (
    <DevtoolsProvider>
      <BrowserRouter>
        <ConfigProvider>
          <AntdApp>
            <Refine
              routerProvider={routerProvider}
              authProvider={authProvider}
              dataProvider={dataProvider}
              notificationProvider={useNotificationProvider}
              options={{
                syncWithLocation: true,
                warnWhenUnsavedChanges: true,
                breadcrumb: false,
              }}
            >
              <Routes>{/* Your routes here */}</Routes>
              <UnsavedChangesNotifier />
              <DocumentTitleHandler />
            </Refine>
          </AntdApp>
        </ConfigProvider>
        <DevtoolsPanel />
      </BrowserRouter>
    </DevtoolsProvider>
  );
};

export default App;
```

]

We are now ready to create "accounts", "clients", "invoices" and "login" pages.

## Step 3 — Building Login Page and Authentication System

In this step, we will create the login page and protect the routes to handle authentication.

Refine handles [authentication](https://refine.dev/docs/guides-concepts/authentication/) by [Auth Provider](https://refine.dev/docs/guides-concepts/authentication/#auth-provider) and consumes the auth provider methods by [auth hooks](https://refine.dev/docs/guides-concepts/authentication/#auth-hooks). We already copied [`authProvivder.ts`](https://github.com/refinedev/refine/blob/feat/add-refine-invoicer/examples/invoicer/src/providers/auth-provider/index.ts) file from the Refine repository give it to [`<Refine />`](https://refine.dev/docs/core/refine-component/) component to handle the [authentication](https://refine.dev/docs/guides-concepts/authentication/).

Let's closer look at the `authProvider.ts` file implemented for the Strapi API:

- `login`: It sends a request to the Strapi API to authenticate the user. If the authentication is successful, it saves the JWT token to the local storage and redirects the user to the home page. If the authentication fails, it returns an error message.
- `logout`: It removes the JWT token from the local storage and redirects the user to the login page.
- `onError`: This function is called when an error occurs during the authentication process. If the error is due to an unauthorized request, it logs the user out.
- `check`: It checks if the JWT token is present in the local storage. If the token is present, it returns that the user is authenticated. If the token is not present, it returns an error message and logs the user out.
- `getIdentity`: It sends a request to the Strapi API to get the user's identity. If the request is successful, it returns the user's id, username, and email. If the request fails, it returns null.

[details Show `authProvider.ts` code

```typescript
[label src/providers/auth-provider/index.ts]
import { AuthProvider } from "@refinedev/core";
import { AuthHelper } from "@refinedev/strapi-v4";
import { API_URL, TOKEN_KEY } from "@/utils/constants";

export const strapiAuthHelper = AuthHelper(`${API_URL}/api`);

export const authProvider: AuthProvider = {
  login: async ({ email, password }) => {
    try {
      const { data, status } = await strapiAuthHelper.login(email, password);
      if (status === 200) {
        localStorage.setItem(TOKEN_KEY, data.jwt);

        return {
          success: true,
          redirectTo: "/",
        };
      }
    } catch (error: any) {
      const errorObj = error?.response?.data?.message?.[0]?.messages?.[0];
      return {
        success: false,
        error: {
          message: errorObj?.message || "Login failed",
          name: errorObj?.id || "Invalid email or password",
        },
      };
    }

    return {
      success: false,
      error: {
        message: "Login failed",
        name: "Invalid email or password",
      },
    };
  },
  logout: async () => {
    localStorage.removeItem(TOKEN_KEY);
    return {
      success: true,
      redirectTo: "/login",
    };
  },
  onError: async (error) => {
    if (error.response?.status === 401) {
      return {
        logout: true,
      };
    }

    return { error };
  },
  check: async () => {
    const token = localStorage.getItem(TOKEN_KEY);
    if (token) {
      return {
        authenticated: true,
      };
    }

    return {
      authenticated: false,
      error: {
        message: "Authentication failed",
        name: "Token not found",
      },
      logout: true,
      redirectTo: "/login",
    };
  },
  getIdentity: async () => {
    const token = localStorage.getItem(TOKEN_KEY);
    if (!token) {
      return null;
    }

    const { data, status } = await strapiAuthHelper.me(token);
    if (status === 200) {
      const { id, username, email } = data;
      return {
        id,
        username,
        email,
      };
    }

    return null;
  },
};
```

]

### Authenticated Routes

To protect the routes, we will use [`<Authenticated />`](https://refine.dev/docs/authentication/components/authenticated/) component from the `"@refinedev/core"` package. This component checks if the user is authenticated. If the user is authenticated, it renders the children. If the user is not authenticated, it renders `fallback` prop if provided. Otherwise, it navigates page to `data.redirectTo` value returned from the `authProvider.check` method.

Let's create our first protected route, after that we will create the login page.

[details Show `App.tsx` code

```typescript
<^>import { Authenticated, Refine } from "@refinedev/core";<^>
import { <^>ErrorComponent<^>, useNotificationProvider } from "@refinedev/antd";
import routerProvider, {
  UnsavedChangesNotifier,
  DocumentTitleHandler,
  <^>NavigateToResource,<^>
} from "@refinedev/react-router-v6";
<^>import { BrowserRouter, Outlet, Route, Routes } from "react-router-dom";<^>
import { DevtoolsPanel, DevtoolsProvider } from "@refinedev/devtools";
import { App as AntdApp } from "antd";
import { dataProvider } from "@/providers/data-provider";
import { authProvider } from "@/providers/auth-provider";
import { ConfigProvider } from "@/providers/config-provider";
import "@refinedev/antd/dist/reset.css";
import "./styles/custom.css";

const App: React.FC = () => {
  return (
    <DevtoolsProvider>
      <BrowserRouter>
        <ConfigProvider>
          <AntdApp>
            <Refine
              routerProvider={routerProvider}
              authProvider={authProvider}
              dataProvider={dataProvider}
              notificationProvider={useNotificationProvider}
              options={{
                syncWithLocation: true,
                warnWhenUnsavedChanges: true,
                breadcrumb: false,
              }}
            >
              <^><Routes><^>
                <^><Route<^>
                  <^>element={<^>
                    <^><Authenticated<^>
                      <^>key="authenticated-routes"<^>
                      <^>redirectOnFail="/login"<^>
                    <^>><^>
                      <^><Outlet /><^>
                    <^></Authenticated><^>
                  }
                >
                  <^><Route path="/" element={<div>Home page</div>} /><^>
                <^></Route><^>

                <^><Route<^>
                  <^>element={<^>
                    <^><Authenticated key="auth-pages" fallback={<Outlet />}><^>
                      <^><NavigateToResource /><^>
                    <^></Authenticated><^>
                  <^>}<^>
                <^>><^>
                  <^><Route path="/login" element={<div>Login page</div>} /><^>
                <^></Route><^>

              <^><Route<^>
                <^>element={<^>
                  <^><Authenticated key='catch-all'><^>
                    <^><Outlet /><^>
                  <^></Authenticated><^>
                <^>}><^>
                <^><Route path='*' element={<ErrorComponent />} /><^>
              <^></Route><^>

              <^></Routes><^>
              <UnsavedChangesNotifier />
              <DocumentTitleHandler />
            </Refine>
          </AntdApp>
        </ConfigProvider>
        <DevtoolsPanel />
      </BrowserRouter>
    </DevtoolsProvider>
  );
};

export default App;
```

]

As you can see in your highlighted code lines above, we have created a protected route for the "/home". If the user is not authenticated, it will redirect the user to the "/login" page. If the user is authenticated, it will render the children.

We also created a catch-all route to show the [`<ErrorComponent />`](https://refine.dev/docs/ui-integrations/ant-design/introduction/#auth-pages) component when the user navigates to a non-existing route(404 not-found page).

### Login Page

Let's create the login page. We will use the [`<AuthPage />`](https://refine.dev/docs/ui-integrations/ant-design/components/auth-page/) component from the `@refinedev/antd` package. This component provides a login form with email and password fields with validation, and a submit button. After form is submitted it will call the `authProvider.login` method.

[details Show `App.tsx` code

```typescript
import {
  // ...
  <^>AuthPage,<^>
} from "@refinedev/antd";
import { Logo } from "@/components/logo";

const App: React.FC = () => {
  return (
    //...
    <Refine
    //...
    >
      <Routes>
        //...
        <^><Route<^>
          <^>element={<^>
            <^><Authenticated key="auth-pages" fallback={<Outlet />}><^>
              <^><NavigateToResource /><^>
            <^></Authenticated><^>
          <^>}<^>
        <^>><^>
          <^><Route<^>
            <^>path="/login"<^>
            <^>element={<^>
              <^><AuthPage<^>
                <^>type="login"<^>
                <^>registerLink={false}<^>
                <^>forgotPasswordLink={false}<^>
                <^>title={<^>
                  <^><Logo<^>
                    <^>titleProps={{ level: 2 }}<^>
                    <^>svgProps={{<^>
                      <^>width: "48px",<^>
                      <^>height: "40px",<^>
                    <^>}}<^>
                  <^>/><^>
                <^>}<^>
                <^>formProps={{<^>
                  <^>initialValues: {<^>
                    <^>email: "demo@refine.dev",<^>
                    <^>password: "demodemo",<^>
                  <^>},<^>
                <^>}}<^>
              <^>/><^>
            <^>}<^>
          <^>/><^>
        <^></Route><^>
      </Routes>
      //...
    </Refine>
    //...
  );
};

export default App;
```

]

In the code lines highlighted above, we've created a login page using the `<AuthPage />` component. We specified the `type` prop as `"login"` to enable the display of the login form. The `registerLink` and `forgotPasswordLink` props are set to `false`, thereby hiding the registration and forgot password links, which are not required for this tutorial. Additionally, the `formProps` prop is used to initialize the email and password fields. We also set the `title` property to show the `<Logo />` component, previously copied from the GitHub repository.

After everything is set up, our "/login" page should look like this:

![Login Page](https://refine.ams3.cdn.digitaloceanspaces.com/blog/invoicer/login-page.png)

After the user logs in, they will be redirected to the home page which is only shows "Home page" text right now but we will add the "accounts", "clients", and "invoices" pages in the next steps.

But before that, let's add our layout and `<Header />` components to the project.

[details Show `App.tsx` code

```typescript
import { Authenticated, Refine } from "@refinedev/core";
import {
  AuthPage,
  ErrorComponent,
  ThemedLayoutV2,
  useNotificationProvider,
} from "@refinedev/antd";
import routerProvider, {
  UnsavedChangesNotifier,
  DocumentTitleHandler,
  NavigateToResource,
  CatchAllNavigate,
} from "@refinedev/react-router-v6";
import { BrowserRouter, Outlet, Route, Routes } from "react-router-dom";
import { DevtoolsPanel, DevtoolsProvider } from "@refinedev/devtools";
import { App as AntdApp } from "antd";
import { dataProvider } from "@/providers/data-provider";
import { authProvider } from "@/providers/auth-provider";
import { ConfigProvider } from "@/providers/config-provider";
import { Logo } from "@/components/logo";
<^>import { Header } from "@/components/header";<^>
import "@refinedev/antd/dist/reset.css";
import "./styles/custom.css";

const App: React.FC = () => {
  return (
    <DevtoolsProvider>
      <BrowserRouter>
        <ConfigProvider>
          <AntdApp>
            <Refine
              routerProvider={routerProvider}
              authProvider={authProvider}
              dataProvider={dataProvider}
              notificationProvider={useNotificationProvider}
              options={{
                syncWithLocation: true,
                warnWhenUnsavedChanges: true,
                breadcrumb: false,
              }}
            >
              <Routes>
                <Route
                  element={
                    <Authenticated
                      key="authenticated-routes"
                      fallback={<CatchAllNavigate to="/login" />}
                    >
                      <^><ThemedLayoutV2<^>
                        <^>Header={() => <Header />}<^>
                        <^>Sider={() => null}<^>
                      >
                        <^><div<^>
                          <^>style={{<^>
                            <^>maxWidth: "1280px",<^>
                            <^>padding: "24px",<^>
                            <^>margin: "0 auto",<^>
                          }}
                        <^>><^>
                          <^><Outlet /><^>
                        <^></div><^>
                      <^></ThemedLayoutV2><^>
                    </Authenticated>
                  }
                >
                  <Route path="/" element={<div>Home page</div>} />
                </Route>

                <Route
                  element={
                    <Authenticated key="auth-pages" fallback={<Outlet />}>
                      <NavigateToResource />
                    </Authenticated>
                  }
                >
                  <Route
                    path="/login"
                    element={
                      <AuthPage
                        type="login"
                        registerLink={false}
                        forgotPasswordLink={false}
                        title={
                          <Logo
                            titleProps={{ level: 2 }}
                            svgProps={{
                              width: "48px",
                              height: "40px",
                            }}
                          />
                        }
                        formProps={{
                          initialValues: {
                            email: "demo@refine.dev",
                            password: "demodemo",
                          },
                        }}
                      />
                    }
                  />
                </Route>

                <Route
                  element={
                    <Authenticated key="catch-all">
                      <^><ThemedLayoutV2<^>
                        <^>Header={() => <Header />}<^>
                        <^>Sider={() => null}<^>
                      >
                        <Outlet />
                      <^></ThemedLayoutV2><^>
                    </Authenticated>
                  }
                >
                  <Route path="*" element={<ErrorComponent />} />
                </Route>
              </Routes>
              <UnsavedChangesNotifier />
              <DocumentTitleHandler />
            </Refine>
          </AntdApp>
        </ConfigProvider>
        <DevtoolsPanel />
      </BrowserRouter>
    </DevtoolsProvider>
  );
};

export default App;
```

]

In the code lines highlighted above, we've added the [`<ThemedLayoutV2 />`](https://refine.dev/docs/ui-integrations/ant-design/components/themed-layout/) component to the project. This component provides a layout with a header and a content area.
We give `Sider` prop as `null` because we don't need a sidebar in this project. We also added the `<Header />` component to the `Header` prop. This component is previously copied from the GitHub repository.

This `<Header />` component will be used to navigate between the "accounts", "clients", and "invoices", Show the user's name and logout button, and show the logo. Also have search input to search the accounts and clients.

After everything is set up, our layout should look like this:

![Layout](https://refine.ams3.cdn.digitaloceanspaces.com/blog/invoicer/layout.png)

## Step 4 — Building Accounts CRUD Pages

Accounts will be used to store information about the companies sending invoices to clients. It will have many-to-one relationship with the clients. Each account can have multiple clients, but each client can only belong to one account.

In this step, we will create the "accounts" page. This page will list all the accounts, and users will be able to create, edit, and delete accounts. Before we start working on these pages, we need to update the `<Refine />` component to include the `accounts` resource.

[details Show `App.tsx` code

```typescript
import { Authenticated, Refine } from "@refinedev/core";
import {
  AuthPage,
  ErrorComponent,
  ThemedLayoutV2,
  useNotificationProvider,
} from "@refinedev/antd";
import routerProvider, {
  UnsavedChangesNotifier,
  DocumentTitleHandler,
  NavigateToResource,
  CatchAllNavigate,
} from "@refinedev/react-router-v6";
import { BrowserRouter, Outlet, Route, Routes } from "react-router-dom";
import { DevtoolsPanel, DevtoolsProvider } from "@refinedev/devtools";
import { App as AntdApp } from "antd";
import { dataProvider } from "@/providers/data-provider";
import { authProvider } from "@/providers/auth-provider";
import { ConfigProvider } from "@/providers/config-provider";
import { Logo } from "@/components/logo";
import { Header } from "@/components/header";
import "@refinedev/antd/dist/reset.css";
import "./styles/custom.css";

const App: React.FC = () => {
  return (
    <DevtoolsProvider>
      <BrowserRouter>
        <ConfigProvider>
          <AntdApp>
            <Refine
              routerProvider={routerProvider}
              authProvider={authProvider}
              dataProvider={dataProvider}
              notificationProvider={useNotificationProvider}
               <^>resources={[ <^>
                 <^>{ <^>
                   <^>name: "accounts",<^>
                   <^>list: "/accounts",<^>
                   <^>create: "/accounts/new",<^>
                   <^>edit: "/accounts/:id/edit",<^>
                 <^>},<^>
               <^>]} <^>
              options={{
                syncWithLocation: true,
                warnWhenUnsavedChanges: true,
                breadcrumb: false,
              }}
            >
              <Routes>
                <Route
                  element={
                    <Authenticated
                      key="authenticated-routes"
                      fallback={<CatchAllNavigate to="/login" />}
                    >
                      <ThemedLayoutV2
                        Header={() => <Header />}
                        Sider={() => null}
                      >
                        <div
                          style={{
                            maxWidth: "1280px",
                            padding: "24px",
                            margin: "0 auto",
                          }}
                        >
                          <Outlet />
                        </div>
                      </ThemedLayoutV2>
                    </Authenticated>
                  }
                >
                  <Route path="/" element={<div>Home page</div>} />
                </Route>

                <Route
                  element={
                    <Authenticated key="auth-pages" fallback={<Outlet />}>
                      <NavigateToResource />
                    </Authenticated>
                  }
                >
                  <Route
                    path="/login"
                    element={
                      <AuthPage
                        type="login"
                        registerLink={false}
                        forgotPasswordLink={false}
                        title={
                          <Logo
                            titleProps={{ level: 2 }}
                            svgProps={{
                              width: "48px",
                              height: "40px",
                            }}
                          />
                        }
                        formProps={{
                          initialValues: {
                            email: "demo@refine.dev",
                            password: "demodemo",
                          },
                        }}
                      />
                    }
                  />
                </Route>

                <Route
                  element={
                    <Authenticated key="catch-all">
                      <ThemedLayoutV2
                        Header={() => <Header />}
                        Sider={() => null}
                      >
                        <Outlet />
                      </ThemedLayoutV2>
                    </Authenticated>
                  }
                >
                  <Route path="*" element={<ErrorComponent />} />
                </Route>
              </Routes>
              <UnsavedChangesNotifier />
              <DocumentTitleHandler />
            </Refine>
          </AntdApp>
        </ConfigProvider>
        <DevtoolsPanel />
      </BrowserRouter>
    </DevtoolsProvider>
  );
};

export default App;
```

]

The [resource](https://refine.dev/docs/core/refine-component/#resources) definition mentioned doesn't actually create any CRUD pages itself. Instead, it establishes the routes that these CRUD pages will follow. These routes are essential for ensuring the proper functionality of various Refine hooks and components.

In the next steps, we will use the [`useNavigation`](https://refine.dev/docs/api-reference/core/hooks/navigation/useNavigation/) hook, which relies on these routes (`list`, `create`, `edit`, and `show`) to help users navigate between different pages in your application. Additionally, data hooks like [`useTable`](https://refine.dev/docs/api-reference/core/hooks/useTable/) will automatically use the resource name if the resource prop is not explicitly provided.

### List Page

The List page will displays account data in a table. To fetch the data, we'll use the [`useTable`](https://refine.dev/docs/api-reference/antd/hooks/table/useTable/) hook from `@refinedev/antd` package, and to render the table, we'll use the [`<Table />`](https://ant.design/components/table) component from the `antd`.

Let's create a `src/pages/accounts/list.tsx` file with the following code:

[details Show `<AccountsPageList />` component

```typescript
[label src/pages/accounts/list.tsx]
import { PropsWithChildren } from "react";
import { getDefaultFilter, useGo } from "@refinedev/core";
import {
  CreateButton,
  DeleteButton,
  EditButton,
  FilterDropdown,
  List,
  NumberField,
  getDefaultSortOrder,
  useSelect,
  useTable,
} from "@refinedev/antd";
import { EyeOutlined, SearchOutlined } from "@ant-design/icons";
import { Avatar, Flex, Input, Select, Table, Typography } from "antd";
import { API_URL } from "@/utils/constants";
import { Account } from "@/types";
import { getRandomColorFromString } from "@/utils/get-random-color";

export const AccountsPageList = ({ children }: PropsWithChildren) => {
  const go = useGo();

  const { tableProps, filters, sorters } = useTable<Account>({
    sorters: {
      initial: [{ field: "updatedAt", order: "desc" }],
    },
    filters: {
      initial: [
        {
          field: "owner_email",
          operator: "contains",
          value: "",
        },
        {
          field: "phone",
          operator: "contains",
          value: "",
        },
      ],
    },
    meta: {
      populate: ["logo", "invoices"],
    },
  });

  const { selectProps: companyNameSelectProps } = useSelect({
    resource: "accounts",
    optionLabel: "company_name",
    optionValue: "company_name",
  });

  const { selectProps: selectPropsOwnerName } = useSelect({
    resource: "accounts",
    optionLabel: "owner_name",
    optionValue: "owner_name",
  });

  return (
    <>
      <List
        title="Accounts"
        headerButtons={() => {
          return (
            <CreateButton
              size="large"
              onClick={() =>
                go({
                  to: { resource: "accounts", action: "create" },
                  options: { keepQuery: true },
                })
              }
            >
              Add new account
            </CreateButton>
          );
        }}
      >
        <Table
          {...tableProps}
          rowKey={"id"}
          pagination={{
            ...tableProps.pagination,
            showSizeChanger: true,
          }}
          scroll={{ x: 960 }}
        >
          <Table.Column
            title="ID"
            dataIndex="id"
            key="id"
            width={80}
            defaultFilteredValue={getDefaultFilter("id", filters)}
            filterIcon={<SearchOutlined />}
            filterDropdown={(props) => {
              return (
                <FilterDropdown {...props}>
                  <Input placeholder="Search ID" />
                </FilterDropdown>
              );
            }}
          />
          <Table.Column
            title="Title"
            dataIndex="company_name"
            key="company_name"
            sorter
            defaultSortOrder={getDefaultSortOrder("company_name", sorters)}
            defaultFilteredValue={getDefaultFilter(
              "company_name",
              filters,
              "in",
            )}
            filterDropdown={(props) => (
              <FilterDropdown {...props}>
                <Select
                  mode="multiple"
                  placeholder="Search Company Name"
                  style={{ width: 220 }}
                  {...companyNameSelectProps}
                />
              </FilterDropdown>
            )}
            render={(name: string, record: Account) => {
              const logoUrl = record?.logo?.url;
              const src = logoUrl ? `${API_URL}${logoUrl}` : undefined;

              return (
                <Flex align="center" gap={8}>
                  <Avatar
                    alt={name}
                    src={src}
                    shape="square"
                    style={{
                      backgroundColor: getRandomColorFromString(name),
                    }}
                  >
                    <Typography.Text>
                      {name?.[0]?.toUpperCase()}
                    </Typography.Text>
                  </Avatar>
                  <Typography.Text>{name}</Typography.Text>
                </Flex>
              );
            }}
          />
          <Table.Column
            title="Owner"
            dataIndex="owner_name"
            key="owner_name"
            sorter
            defaultSortOrder={getDefaultSortOrder("owner_name", sorters)}
            defaultFilteredValue={getDefaultFilter("owner_name", filters, "in")}
            filterDropdown={(props) => (
              <FilterDropdown {...props}>
                <Select
                  mode="multiple"
                  placeholder="Search Owner Name"
                  style={{ width: 220 }}
                  {...selectPropsOwnerName}
                />
              </FilterDropdown>
            )}
          />
          <Table.Column
            title="Email"
            dataIndex="owner_email"
            key="owner_email"
            defaultFilteredValue={getDefaultFilter(
              "owner_email",
              filters,
              "contains",
            )}
            filterIcon={<SearchOutlined />}
            filterDropdown={(props) => {
              return (
                <FilterDropdown {...props}>
                  <Input placeholder="Search Email" />
                </FilterDropdown>
              );
            }}
          />
          <Table.Column
            title="Phone"
            dataIndex="phone"
            key="phone"
            width={154}
            defaultFilteredValue={getDefaultFilter(
              "phone",
              filters,
              "contains",
            )}
            filterIcon={<SearchOutlined />}
            filterDropdown={(props) => {
              return (
                <FilterDropdown {...props}>
                  <Input placeholder="Search Phone" />
                </FilterDropdown>
              );
            }}
          />
          <Table.Column
            title="Income"
            dataIndex="income"
            key="income"
            width={120}
            align="end"
            render={(_, record: Account) => {
              let total = 0;
              record.invoices?.forEach((invoice) => {
                total += invoice.total;
              });
              return (
                <NumberField
                  value={total}
                  options={{ style: "currency", currency: "USD" }}
                />
              );
            }}
          />
          <Table.Column
            title="Actions"
            key="actions"
            fixed="right"
            align="end"
            width={106}
            render={(_, record: Account) => {
              return (
                <Flex align="center" gap={8}>
                  <EditButton
                    hideText
                    recordItemId={record.id}
                    icon={<EyeOutlined />}
                  />
                  <DeleteButton hideText recordItemId={record.id} />
                </Flex>
              );
            }}
          />
        </Table>
      </List>
      {children}
    </>
  );
};

```

]

Let's break down the code above:

We fetched data using the [`useTable`](https://refine.dev/docs/ui-integrations/ant-design/hooks/use-table/) hook, specifying relationships via `meta.populate`, and displayed it with the [`<Table />`](https://refine.dev/docs/guides-concepts/tables/) component. For Strapi queries, refer to the [Strapi v4 documentation](https://refine.dev/docs/data/packages/strapi-v4/).

The table includes columns like company name, owner name, owner email, phone, and income, following [Ant Design Table](https://ant.design/components/table/) guidelines. We used components like `<FilterDropdown />`, `<Select />`, `<Input />`, `<Avatar />`, `<Flex />`, `<NumberField />`, and `<EditButton />` from `@refinedev/antd` and `antd` for customization.

Search inputs were added to each column for data filtering, using [`getDefaultFilter`](https://refine.dev/docs/data/hooks/use-table/#filtering) and [`getDefaultSortOrder`](https://refine.dev/docs/data/hooks/use-table/#sorting) from `"@refinedev/core"` and `"@refinedev/antd"` to set defaults from query parameters.

The [`useSelect`](https://refine.dev/docs/ui-integrations/ant-design/hooks/use-select/) hook allow us to manage Ant Design's [`<Select />`](https://ant.design/components/select) component when the records in a resource needs to be used as select options. We used it to fetch values for the `company_name` and `owner_name` columns to filter the table data.

We used the `children` prop to render a modal for creating new accounts when the "Add new account" button is clicked.

The [`<CreateButton />`](https://refine.dev/docs/ui-integrations/ant-design/components/buttons/create-button/) normally navigates to the create page but was modified with the `onClick` prop and `go` function from `"@refinedev/core"` to open it as a modal, preserving query parameters.

Finally, the [`<EditButton />`](https://refine.dev/docs/ui-integrations/ant-design/components/buttons/edit-button/) and [`<DeleteButton />`](https://refine.dev/docs/ui-integrations/ant-design/components/buttons/delete-button/) components handle editing and deleting accounts. The `<EditButton />` opens the edit page as a modal, and the `<DeleteButton />` deletes the account when clicked.

To import the company list page from other files, we need to update the `src/pages/accounts/index.tsx` file:

```typescript
export { AccountsPageList } from "./list";
```

Next, import the `<AccountsPageList />` component in `src/App.tsx` and add a route for rendering it.

[details Show `App.tsx` code

```typescript
[label src/App.tsx]
import { Authenticated, Refine } from "@refinedev/core";
import {
  AuthPage,
  ErrorComponent,
  ThemedLayoutV2,
  useNotificationProvider,
} from "@refinedev/antd";
import routerProvider, {
  UnsavedChangesNotifier,
  DocumentTitleHandler,
  <^>NavigateToResource,<^>
  CatchAllNavigate,
} from "@refinedev/react-router-v6";
import { BrowserRouter, Outlet, Route, Routes } from "react-router-dom";
import { DevtoolsPanel, DevtoolsProvider } from "@refinedev/devtools";
import { App as AntdApp } from "antd";
import { dataProvider } from "@/providers/data-provider";
import { authProvider } from "@/providers/auth-provider";
import { ConfigProvider } from "@/providers/config-provider";
import { Logo } from "@/components/logo";
import { Header } from "@/components/header";
<^>import { AccountsPageList } from "@/pages/accounts";<^>
import "@refinedev/antd/dist/reset.css";
import "./styles/custom.css";

const App: React.FC = () => {
  return (
    <DevtoolsProvider>
      <BrowserRouter>
        <ConfigProvider>
          <AntdApp>
            <Refine
              routerProvider={routerProvider}
              authProvider={authProvider}
              dataProvider={dataProvider}
              notificationProvider={useNotificationProvider}
              resources={[
                {
                  name: "accounts",
                  list: "/accounts",
                  create: "/accounts/new",
                  edit: "/accounts/:id/edit",
                },
              ]}
              options={{
                syncWithLocation: true,
                warnWhenUnsavedChanges: true,
                breadcrumb: false,
              }}
            >
              <Routes>
                <Route
                  element={
                    <Authenticated
                      key="authenticated-routes"
                      fallback={<CatchAllNavigate to="/login" />}
                    >
                      <ThemedLayoutV2
                        Header={() => <Header />}
                        Sider={() => null}
                      >
                        <div
                          style={{
                            maxWidth: "1280px",
                            padding: "24px",
                            margin: "0 auto",
                          }}
                        >
                          <Outlet />
                        </div>
                      </ThemedLayoutV2>
                    </Authenticated>
                  }
                >
                  <^><Route index element={<NavigateToResource />} /><^>
                  <^><Route<^>
                    <^>path="/accounts"<^>
                    <^>element={<^>
                      <^><AccountsPageList><^>
                        <^><Outlet /><^>
                      <^></AccountsPageList><^>
                    <^>}<^>
                  <^>><^>
                    <^><Route index element={null} /><^>
                  <^></Route><^>
                <^></Route><^>

                <Route
                  element={
                    <Authenticated key="auth-pages" fallback={<Outlet />}>
                      <NavigateToResource />
                    </Authenticated>
                  }
                >
                  <Route
                    path="/login"
                    element={
                      <AuthPage
                        type="login"
                        registerLink={false}
                        forgotPasswordLink={false}
                        title={
                          <Logo
                            titleProps={{ level: 2 }}
                            svgProps={{
                              width: "48px",
                              height: "40px",
                            }}
                          />
                        }
                        formProps={{
                          initialValues: {
                            email: "demo@refine.dev",
                            password: "demodemo",
                          },
                        }}
                      />
                    }
                  />
                </Route>

                <Route
                  element={
                    <Authenticated key="catch-all">
                      <ThemedLayoutV2
                        Header={() => <Header />}
                        Sider={() => null}
                      >
                        <Outlet />
                      </ThemedLayoutV2>
                    </Authenticated>
                  }
                >
                  <Route path="*" element={<ErrorComponent />} />
                </Route>
              </Routes>
              <UnsavedChangesNotifier />
              <DocumentTitleHandler />
            </Refine>
          </AntdApp>
        </ConfigProvider>
        <DevtoolsPanel />
      </BrowserRouter>
    </DevtoolsProvider>
  );
};

export default App;
```

]

Let's look at the changes we made to the `App.tsx` file:

1. We imported the `<AccountsPageList />` component from `src/pages/accounts/list.tsx` and the [`<NavigateToResource />`](https://refine.dev/docs/routing/integrations/react-router/#navigatetoresource) component from the `@refinedev/react-router-v6` package.
2. We assigned the `<NavigateToResource />` component to the "/" route. This automatically directs users to the first list page available in the resources array, which in this case, leads them to the "/accounts" path when they visit the home page.
3. Main `<Route />` for "/accounts":
   - The `path="/accounts"` indicates that this route configuration applies when the URL matches "/accounts".
   - The `element` property determines which component is displayed when this route is accessed. Here, it is `<AccountsPageList />`.
   - Inside `<AccountsPageList />`, there's an `<Outlet />`. This `<Outlet />` acts as a placeholder that renders the matched child route components. By using this pattern, our `<AccountsPageList />` component acts as a layout component and with this structure, we can easily add nested routes as a modal or drawer to the list page.
4. Nested `<Route />` with index:
   - This nested route under "/accounts" matches exactly when the path is "/accounts". The `element={null}` setting means that when users go directly to "/accounts", they will see only the list page without any additional components. This configuration ensures a clean display of the list alone, without extra UI elements or forms from other nested routes.

Now, if you navigate to the "/accounts" path, you should see the list page.

![Accounts List Page](https://refine.ams3.cdn.digitaloceanspaces.com/blog/invoicer/accounts-list.png)

### Create Page

The create page will show a form to create a new account record.

We will use the [`<Form />`](https://ant.design/components/form) component, and for managing form submissions, the [`useForm`](https://refine.dev/docs/api-reference/antd/hooks/form/useForm/) hook will be utilized.

Let's create a `src/pages/accounts/create.tsx` file with the following code:

[details Show `<AccountsPageCreate />` component

```typescript
[label src/pages/accounts/create.tsx]
import { HttpError, useGo } from "@refinedev/core";
import { useForm } from "@refinedev/antd";
import { Flex, Form, Input, Modal, Select } from "antd";
import InputMask from "react-input-mask";
import { getValueProps } from "@refinedev/strapi-v4";
import { FormItemUploadLogoDraggable } from "@/components/form";
import { API_URL } from "@/utils/constants";
import { Account, AccountForm } from "@/types";

export const AccountsPageCreate = () => {
  const go = useGo();

  const { formProps } = useForm<Account, HttpError, AccountForm>();

  return (
    <Modal
      okButtonProps={{ form: "create-account-form", htmlType: "submit" }}
      title="Add new account"
      open
      onCancel={() => {
        go({
          to: { resource: "accounts", action: "list" },
          options: { keepQuery: true },
        });
      }}
    >
      <Form
        layout="vertical"
        id="create-account-form"
        {...formProps}
        onFinish={(values) => {
          const logoId = values.logo?.file?.response?.[0]?.id;
          return formProps.onFinish?.({
            ...values,
            logo: logoId,
          } as AccountForm);
        }}
      >
        <Flex gap={40}>
          <FormItemUploadLogoDraggable />
          <Flex
            vertical
            style={{
              width: "420px",
            }}
          >
            <Form.Item
              name="company_name"
              label="Company Name"
              rules={[{ required: true }]}
            >
              <Input />
            </Form.Item>
            <Form.Item
              name="owner_name"
              label="Owner Name"
              rules={[{ required: true }]}
            >
              <Input />
            </Form.Item>
            <Form.Item
              name="owner_email"
              label="Owner email"
              rules={[{ required: true, type: "email" }]}
            >
              <Input />
            </Form.Item>
            <Form.Item
              name="address"
              label="Address"
              rules={[{ required: true }]}
            >
              <Input />
            </Form.Item>
            <Form.Item name="phone" label="Phone" rules={[{ required: true }]}>
              <InputMask mask="(999) 999-9999">
                {/* @ts-expect-error  <InputMask /> expects JSX.Element but we are using React.ReactNode */}
                {(props: InputProps) => (
                  <Input {...props} placeholder="Please enter phone number" />
                )}
              </InputMask>
            </Form.Item>
          </Flex>
        </Flex>
      </Form>
    </Modal>
  );
};
```

]

As we explained in the previous step, `<AccountsPageCreate />` will be used as a sub-route of the list page(/accountes/new). Right now, `<AccountsPageList />` has an children prop as [`<Outlet />`](https://reactrouter.com/en/main/components/outlet) to render the nested routes. With this pattern, we can easily add nested routes as a modal or drawer to the list page. That's why we used [`<Modal />`](https://ant.design/components/modal/) from `antd`. The `okButtonProps` prop is used to submit the form when the "OK" button is clicked. The `onCancel` prop is used to navigate back to the list page when the "Cancel" button is clicked.

Let's closer look at the custom components and logics we used in the `<AccountsPageCreate />` component:

- `useForm`: This hook manages form state and handles submission. The `formProps` object from [`useForm`](https://refine.dev/docs/ui-integrations/ant-design/hooks/use-form/) is passed to the [`<Form />`](https://refine.dev/docs/guides-concepts/forms/) component to control these aspects.
  - `action` and `resource` props are inferred from the route parameters of the resource we defined earlier. We don't need to pass them explicitly.
- `<FormItemUploadLogoDraggable />`:
  This component is used to upload a logo for the account. It is a custom component that we copied from the GitHub repository. It uses the `<Upload.Dragger />` component from `antd` to upload the logo. It not contains much logic, we just made couple of changes to the original component to fit our design needs.

  - `customRequest`: prop is used to upload the logo to the Strapi media library. It's just a basic post request to the Strapi media endpoint with the given file from the Ant Design's `<Upload />` component. We also need to catch errors and set the form's error state if the upload fails.
  - `getValueProps`: from `@refinedev/strapi-v4` is used to get the Strapi media's URL. We pass the `data` and `API_URL` as arguments to the get the media URL.
  - `fieldValue`: This state watches the logo field value with `Form.useWatch` hook from `antd`. We use this state to show the uploaded logo in the form.

- Overriding the `onFinish` prop of the `<Form />` component:
  We override the `onFinish` prop of the `<Form />` component to handle the form submission. We extract the uploaded media id from the form values and pass it to the `onFinish` function as `logo` field. When you give id of the media, Strapi will automatically create a relation between the media and the account.

Rest of form fields are basic `antd` form fields. We used the `rules` prop to set the required fields and the type of the email field. [You can refer to the Forms guide for more information.](https://refine.dev/docs/guides-concepts/forms/#relationships-)

To import the company create page from other files, we need to update the `src/pages/accounts/index.tsx` file:

```typescript
[label src/pages/accounts/index.tsx]
export { AccountsPageList } from "./list";
<^>export { AccountsPageCreate } from "./create";<^>
```

Next, import the `<AccountsPageCreate />` component in `src/App.tsx` and add a route for rendering it.

[details Show `App.tsx` code

```typescript
[label src/App.tsx]
//...
<^>import { AccountsPageCreate, AccountsPageList } from '@/pages/accounts'<^>

const App: React.FC = () => {
  return (
    //...
    <Refine
    //...
    >
      <Routes>
        <Route
          element={
            <Authenticated key='authenticated-routes' fallback={<CatchAllNavigate to='/login' />}>
              <ThemedLayoutV2 Header={() => <Header />} Sider={() => null}>
                <div
                  style={{
                    maxWidth: '1280px',
                    padding: '24px',
                    margin: '0 auto',
                  }}>
                  <Outlet />
                </div>
              </ThemedLayoutV2>
            </Authenticated>
          }>
          <Route index element={<NavigateToResource />} />
          <Route
            path='/accounts'
            element={
              <AccountsPageList>
                <Outlet />
              </AccountsPageList>
            }>
            <Route index element={null} />
            <^><Route path='new' element={<AccountsPageCreate />} /><^>
          </Route>
        </Route>
        {/* ... */}
      </Routes>
      {/* ... */}
    </Refine>
    //...
  )
}

export default App

```

]

Now, when you click the "Add new account" button on the list page, you should see the create page as a modal.

After you fill out the form and click the "OK" button, the account will be created and you will be redirected to the list page.

![Accounts Create Page](https://refine.ams3.cdn.digitaloceanspaces.com/blog/invoicer/accounts-new.png)

### Edit Page

The edit page will feature a form for modifying an existing account record. Unlike before, this will be a separate page, not a modal. Additionally, it will display relationship data such as clients and invoices in a non-editable table.

> 🚨 The Invoice and Customers CRUD sheets are not available in this step, we will add them in the next steps, but since we have already prepared the API for this tutorial, these tables will be populated with data.

Let's create a `src/pages/accounts/edit.tsx` file with the following code:

[details Show `<AccountsPageEdit />` component

```typescript
[label src/pages/accounts/edit.tsx]
import { HttpError, useNavigation } from "@refinedev/core";
import {
  DateField,
  DeleteButton,
  EditButton,
  NumberField,
  Show,
  ShowButton,
  useForm,
} from "@refinedev/antd";
import { Card, Divider, Flex, Form, Table, Typography } from "antd";
import {
  BankOutlined,
  UserOutlined,
  MailOutlined,
  EnvironmentOutlined,
  PhoneOutlined,
  ExportOutlined,
  ContainerOutlined,
  ShopOutlined,
} from "@ant-design/icons";
import { Col, Row } from "antd";
import {
  FormItemEditableInputText,
  FormItemEditableText,
  FormItemUploadLogo,
} from "@/components/form";
import { Account, AccountForm } from "@/types";

export const AccountsPageEdit = () => {
  const { list } = useNavigation();

  const { formProps, queryResult } = useForm<Account, HttpError, AccountForm>({
    redirect: false,
    meta: {
      populate: ["logo", "clients", "invoices.client"],
    },
  });
  const account = queryResult?.data?.data;
  const clients = account?.clients || [];
  const invoices = account?.invoices || [];
  const isLoading = queryResult?.isLoading;

  return (
    <Show
      title="Accounts"
      headerButtons={() => false}
      contentProps={{
        styles: {
          body: {
            padding: 0,
          },
        },
        style: {
          background: "transparent",
          boxShadow: "none",
        },
      }}
    >
      <Form
        {...formProps}
        onFinish={(values) => {
          const logoId = values.logo?.file?.response?.[0]?.id;
          return formProps.onFinish?.({
            ...values,
            logo: logoId,
          } as AccountForm);
        }}
        layout="vertical"
      >
        <Row>
          <Col span={24}>
            <Flex gap={16}>
              <FormItemUploadLogo
                isLoading={isLoading}
                label={account?.company_name || " "}
                onUpload={() => {
                  formProps.form?.submit();
                }}
              />
              <FormItemEditableText
                loading={isLoading}
                formItemProps={{
                  name: "company_name",
                  rules: [{ required: true }],
                }}
              />
            </Flex>
          </Col>
        </Row>
        <Row
          gutter={32}
          style={{
            marginTop: "32px",
          }}
        >
          <Col xs={{ span: 24 }} xl={{ span: 8 }}>
            <Card
              bordered={false}
              styles={{ body: { padding: 0 } }}
              title={
                <Flex gap={12} align="center">
                  <BankOutlined />
                  <Typography.Text>Account info</Typography.Text>
                </Flex>
              }
            >
              <FormItemEditableInputText
                loading={isLoading}
                icon={<UserOutlined />}
                placeholder="Add owner name"
                formItemProps={{
                  name: "owner_name",
                  label: "Owner name",
                  rules: [{ required: true }],
                }}
              />
              <Divider style={{ margin: 0 }} />
              <FormItemEditableInputText
                loading={isLoading}
                icon={<MailOutlined />}
                placeholder="Add email"
                formItemProps={{
                  name: "owner_email",
                  label: "Owner email",
                  rules: [{ required: true }],
                }}
              />
              <Divider style={{ margin: 0 }} />
              <Divider style={{ margin: 0 }} />
              <FormItemEditableInputText
                loading={isLoading}
                icon={<EnvironmentOutlined />}
                placeholder="Add address"
                formItemProps={{
                  name: "address",
                  label: "Address",
                  rules: [{ required: true }],
                }}
              />
              <Divider style={{ margin: 0 }} />
              <FormItemEditableInputText
                loading={isLoading}
                icon={<PhoneOutlined />}
                placeholder="Add phone number"
                formItemProps={{
                  name: "phone",
                  label: "Phone",
                  rules: [{ required: true }],
                }}
              />
            </Card>
            <DeleteButton
              type="text"
              style={{
                marginTop: "16px",
              }}
              onSuccess={() => {
                list("clients");
              }}
            >
              Delete account
            </DeleteButton>
          </Col>

          <Col xs={{ span: 24 }} xl={{ span: 16 }}>
            <Card
              bordered={false}
              title={
                <Flex gap={12} align="center">
                  <ShopOutlined />
                  <Typography.Text>Clients</Typography.Text>
                </Flex>
              }
              styles={{
                header: {
                  padding: "0 16px",
                },
                body: {
                  padding: "0",
                },
              }}
            >
              <Table
                dataSource={clients}
                pagination={false}
                loading={isLoading}
                rowKey={"id"}
              >
                <Table.Column title="ID" dataIndex="id" key="id" />
                <Table.Column title="Client" dataIndex="name" key="name" />
                <Table.Column
                  title="Owner"
                  dataIndex="owner_name"
                  key="owner_name"
                />
                <Table.Column
                  title="Email"
                  dataIndex="owner_email"
                  key="owner_email"
                />
                <Table.Column
                  key="actions"
                  width={64}
                  render={(_, record: Account) => {
                    return (
                      <EditButton
                        hideText
                        resource="clients"
                        recordItemId={record.id}
                        icon={<ExportOutlined />}
                      />
                    );
                  }}
                />
              </Table>
            </Card>

            <Card
              bordered={false}
              title={
                <Flex gap={12} align="center">
                  <ContainerOutlined />
                  <Typography.Text>Invoices</Typography.Text>
                </Flex>
              }
              style={{ marginTop: "32px" }}
              styles={{
                header: {
                  padding: "0 16px",
                },
                body: {
                  padding: 0,
                },
              }}
            >
              <Table
                dataSource={invoices}
                pagination={false}
                loading={isLoading}
                rowKey={"id"}
              >
                <Table.Column title="ID" dataIndex="id" key="id" width={72} />
                <Table.Column
                  title="Date"
                  dataIndex="date"
                  key="date"
                  render={(date) => (
                    <DateField value={date} format="D MMM YYYY" />
                  )}
                />
                <Table.Column
                  title="Client"
                  dataIndex="client"
                  key="client"
                  render={(client) => client?.name}
                />
                <Table.Column
                  title="Amount"
                  dataIndex="total"
                  key="total"
                  render={(total) => (
                    <NumberField
                      value={total}
                      options={{ style: "currency", currency: "USD" }}
                    />
                  )}
                />
                <Table.Column
                  key="actions"
                  width={64}
                  render={(_, record: Account) => {
                    return (
                      <ShowButton
                        hideText
                        resource="invoices"
                        recordItemId={record.id}
                        icon={<ExportOutlined />}
                      />
                    );
                  }}
                />
              </Table>
            </Card>
          </Col>
        </Row>
      </Form>
    </Show>
  );
};
```

]

In the `<AccountsPageEdit />` component its'a mix of show and edit page. We used the [`<Show />`](https://refine.dev/docs/ui-integrations/ant-design/components/basic-views/show/) component from `@refinedev/antd` for a layout. With help of `useForm` hook, we fetched the account data and populated the logo, clients, and invoices relationships. To display the clients and invoices, we used the `<Table />` component from `antd`.

Let's closer look at the custom components and logics we used in the `<AccountsPageEdit />` component:

- `useForm`: Similar the create page with couple of differences:
  - We used the `redirect` option to prevent the form from redirecting after submission.
  - The `meta` option is used to populate the logo, clients, and invoices relationships.
  - `action`, `resource`, and `id` props are inferred from the route parameters of the resource we defined earlier. We don't need to pass them explicitly.
- `<FormItemUploadLogo />`: Sames as the create page, it uploads a logo for the account, using the `onUpload` prop to submit the form upon logo upload. The `onFinish` function extracts the uploaded media ID from the form values and passes it as the `logo` field. Providing the media ID allows Strapi to automatically create a relation between the media and the account.
- `<FormItemEditableInputText />`: Is a custom component that we copied from the GitHub repository. It's uses the `<Form.Item />` and `<Input />` component from `antd` with some additional logic to make the input fields editable. It allows us to edit each input field in the form individually.
  - `handleEdit`: This function is used to toggle the input field to editable mode. It sets the `isEditing` state to `true`.
  - `handleOnCancel`: This function is used to cancel the editing mode. It sets the `isEditing` state to `false` and resets the input field value to initial value.
  - `handleOnSave`: This function is used to save the edited value. It's submits the form with the new value and sets the `isEditing` state to `false`.
- [`<DeleteButton />`](https://refine.dev/docs/ui-integrations/ant-design/components/buttons/delete-button/): This component is used to delete the account.
- [`useNavigation`](https://refine.dev/docs/api-reference/core/hooks/use-navigation/): This hook provides the `list` function to navigate to the list page of the resource. We used it to navigate to the clients list page after deleting the account.

To import the company edit page from other files, we need to update the `src/pages/accounts/index.tsx` file:

```typescript
[label src/pages/accounts/index.tsx]
export { AccountsPageList } from "./list";
export { AccountsPageCreate } from "./create";
<^>export { AccountsPageEdit } from "./edit";<^>
```

Next, import the `<AccountsPageEdit />` component in `src/App.tsx` and add a route for rendering it.

[details Show `App.tsx` code

```typescript
[label src/App.tsx]
import { Authenticated, Refine } from "@refinedev/core";
import {
  AuthPage,
  ErrorComponent,
  ThemedLayoutV2,
  useNotificationProvider,
} from "@refinedev/antd";
import routerProvider, {
  UnsavedChangesNotifier,
  DocumentTitleHandler,
  NavigateToResource,
  CatchAllNavigate,
} from "@refinedev/react-router-v6";
import { BrowserRouter, Outlet, Route, Routes } from "react-router-dom";
import { DevtoolsPanel, DevtoolsProvider } from "@refinedev/devtools";
import { App as AntdApp } from "antd";
import { dataProvider } from "@/providers/data-provider";
import { authProvider } from "@/providers/auth-provider";
import { ConfigProvider } from "@/providers/config-provider";
import { Logo } from "@/components/logo";
import { Header } from "@/components/header";
import {
  AccountsPageCreate,
   <^>AccountsPageEdit, <^>
  AccountsPageList,
} from "@/pages/accounts";
import "@refinedev/antd/dist/reset.css";
import "./styles/custom.css";

const App: React.FC = () => {
  return (
    <DevtoolsProvider>
      <BrowserRouter>
        <ConfigProvider>
          <AntdApp>
            <Refine
              routerProvider={routerProvider}
              authProvider={authProvider}
              dataProvider={dataProvider}
              notificationProvider={useNotificationProvider}
              resources={[
                {
                  name: "accounts",
                  list: "/accounts",
                  create: "/accounts/new",
                  edit: "/accounts/:id/edit",
                },
              ]}
              options={{
                syncWithLocation: true,
                warnWhenUnsavedChanges: true,
                breadcrumb: false,
              }}
            >
              <Routes>
                <Route
                  element={
                    <Authenticated
                      key="authenticated-routes"
                      fallback={<CatchAllNavigate to="/login" />}
                    >
                      <ThemedLayoutV2
                        Header={() => <Header />}
                        Sider={() => null}
                      >
                        <div
                          style={{
                            maxWidth: "1280px",
                            padding: "24px",
                            margin: "0 auto",
                          }}
                        >
                          <Outlet />
                        </div>
                      </ThemedLayoutV2>
                    </Authenticated>
                  }
                >
                  <Route index element={<NavigateToResource />} />

                  <Route
                    path="/accounts"
                    element={
                      <AccountsPageList>
                        <Outlet />
                      </AccountsPageList>
                    }
                  >
                    <Route index element={null} />
                    <Route path="new" element={<AccountsPageCreate />} />
                  </Route>
                  <^><Route<^>
                    <^>path="/accounts/:id/edit"<^>
                    <^>element={<AccountsPageEdit />}<^>
                  <^>/><^>
                </Route>

                <Route
                  element={
                    <Authenticated key="auth-pages" fallback={<Outlet />}>
                      <NavigateToResource />
                    </Authenticated>
                  }
                >
                  <Route
                    path="/login"
                    element={
                      <AuthPage
                        type="login"
                        registerLink={false}
                        forgotPasswordLink={false}
                        title={
                          <Logo
                            titleProps={{ level: 2 }}
                            svgProps={{
                              width: "48px",
                              height: "40px",
                            }}
                          />
                        }
                        formProps={{
                          initialValues: {
                            email: "demo@refine.dev",
                            password: "demodemo",
                          },
                        }}
                      />
                    }
                  />
                </Route>

                <Route
                  element={
                    <Authenticated key="catch-all">
                      <ThemedLayoutV2
                        Header={() => <Header />}
                        Sider={() => null}
                      >
                        <Outlet />
                      </ThemedLayoutV2>
                    </Authenticated>
                  }
                >
                  <Route path="*" element={<ErrorComponent />} />
                </Route>
              </Routes>
              <UnsavedChangesNotifier />
              <DocumentTitleHandler />
            </Refine>
          </AntdApp>
        </ConfigProvider>
        <DevtoolsPanel />
      </BrowserRouter>
    </DevtoolsProvider>
  );
};

export default App;
```

]

After clicking the "Edit" button on the list page, you should see the edit page.

![Accounts Edit Page](https://refine.ams3.cdn.digitaloceanspaces.com/blog/invoicer/accounts-edit.png)

## Step 5 — Adding Clients CRUD Pages

Clients will be used to store information about the client receiving invoices from accounts. It will have a one-to-many relationship with the accounts. Each account can have multiple clients, but each client can only belong to one account.

In this step, we will create the "clients" page. This page will list all the clients, and users will be able to create, edit, and delete clients. However, it will be similar to the accounts page, to keep the tutorial easy to follow we don't explain same components and logics again.

Before we start working on these pages, we need to update the `<Refine />` component to include the `clients` resource.

Let's start by defining the `"clients"` resource in `src/App.tsx` file as follows:

[details Show `src/App.tsx` code

```typescript
[label src/App.tsx]
// ...

const App: React.FC = () => {
  return (
    // ...
    <Refine
      // ...
      resources={[
        // ...
        <^>{<^>
          <^>name: 'clients',<^>
          <^>list: '/clients',<^>
          <^>create: '/clients/new',<^>
          <^>edit: '/clients/:id/edit',<^>
        <^>},<^>
      ]}
      // ...
    >
      {/* ... */}
    </Refine>
    // ...
  )
}

export default App

```

]

Let's create CRUD pages for the `"clients"` resource as follows:

Create `src/pages/clients/list.tsx` file with the following code:

[details Show `<ClientsPageList />` code

```typescript
[label src/pages/clients/list.tsx]
import { PropsWithChildren } from "react";
import { getDefaultFilter, useGo } from "@refinedev/core";
import {
  CreateButton,
  DeleteButton,
  EditButton,
  FilterDropdown,
  List,
  NumberField,
  getDefaultSortOrder,
  useSelect,
  useTable,
} from "@refinedev/antd";
import { Avatar, Flex, Input, Select, Table, Typography } from "antd";
import { EyeOutlined, SearchOutlined } from "@ant-design/icons";
import { API_URL } from "@/utils/constants";
import { getRandomColorFromString } from "@/utils/get-random-color";
import { Client } from "@/types";

export const ClientsPageList = ({ children }: PropsWithChildren) => {
  const go = useGo();

  const { tableProps, filters, sorters } = useTable<Client>({
    sorters: {
      initial: [{ field: "updatedAt", order: "desc" }],
    },
    filters: {
      initial: [
        {
          field: "owner_email",
          operator: "contains",
          value: "",
        },
      ],
    },
    meta: {
      populate: ["account.logo", "invoices"],
    },
  });

  const { selectProps: selectPropsName } = useSelect({
    resource: "clients",
    optionLabel: "name",
    optionValue: "name",
  });

  const { selectProps: selectPropsOwnerName } = useSelect({
    resource: "clients",
    optionLabel: "owner_name",
    optionValue: "owner_name",
  });

  const { selectProps: selectPropsAccountName } = useSelect({
    resource: "accounts",
    optionLabel: "company_name",
    optionValue: "company_name",
  });

  return (
    <>
      <List
        title="Clients"
        headerButtons={() => {
          return (
            <CreateButton
              size="large"
              onClick={() =>
                go({
                  to: { resource: "clients", action: "create" },
                  options: { keepQuery: true },
                })
              }
            >
              Add new client
            </CreateButton>
          );
        }}
      >
        <Table
          {...tableProps}
          rowKey={"id"}
          pagination={{
            ...tableProps.pagination,
            showSizeChanger: true,
          }}
          scroll={{ x: 960 }}
        >
          <Table.Column
            title="ID"
            dataIndex="id"
            key="id"
            width={80}
            defaultFilteredValue={getDefaultFilter("id", filters)}
            filterIcon={<SearchOutlined />}
            filterDropdown={(props) => {
              return (
                <FilterDropdown {...props}>
                  <Input placeholder="Search ID" />
                </FilterDropdown>
              );
            }}
          />
          <Table.Column
            title="Title"
            dataIndex="name"
            key="name"
            sorter
            defaultSortOrder={getDefaultSortOrder("name", sorters)}
            defaultFilteredValue={getDefaultFilter("name", filters, "in")}
            filterDropdown={(props) => (
              <FilterDropdown {...props}>
                <Select
                  mode="multiple"
                  placeholder="Search Name"
                  style={{ width: 220 }}
                  {...selectPropsName}
                />
              </FilterDropdown>
            )}
          />
          <Table.Column
            title="Owner"
            dataIndex="owner_name"
            key="owner_name"
            sorter
            defaultSortOrder={getDefaultSortOrder("owner_name", sorters)}
            defaultFilteredValue={getDefaultFilter("owner_name", filters, "in")}
            filterDropdown={(props) => (
              <FilterDropdown {...props}>
                <Select
                  mode="multiple"
                  placeholder="Search Owner"
                  style={{ width: 220 }}
                  {...selectPropsOwnerName}
                />
              </FilterDropdown>
            )}
          />
          <Table.Column
            title="Email"
            dataIndex="owner_email"
            key="owner_email"
            defaultFilteredValue={getDefaultFilter(
              "owner_email",
              filters,
              "contains",
            )}
            filterIcon={<SearchOutlined />}
            filterDropdown={(props) => {
              return (
                <FilterDropdown {...props}>
                  <Input placeholder="Search Email" />
                </FilterDropdown>
              );
            }}
          />
          <Table.Column
            title="Total"
            dataIndex="total"
            key="total"
            width={120}
            align="end"
            render={(_, record: Client) => {
              let total = 0;
              record.invoices?.forEach((invoice) => {
                total += invoice.total;
              });
              return (
                <NumberField
                  value={total}
                  options={{ style: "currency", currency: "USD" }}
                />
              );
            }}
          />
          <Table.Column
            title="Account"
            dataIndex="account.company_name"
            key="account.company_name"
            defaultFilteredValue={getDefaultFilter(
              "account.company_name",
              filters,
              "in",
            )}
            filterDropdown={(props) => (
              <FilterDropdown {...props}>
                <Select
                  mode="multiple"
                  placeholder="Search Account"
                  style={{ width: 220 }}
                  {...selectPropsAccountName}
                />
              </FilterDropdown>
            )}
            render={(_, record: Client) => {
              const logoUrl = record?.account?.logo?.url;
              const src = logoUrl ? `${API_URL}${logoUrl}` : null;
              const name = record?.account?.company_name || "";

              return (
                <Flex align="center" gap={8}>
                  <Avatar
                    alt={name}
                    src={src}
                    shape="square"
                    style={{
                      backgroundColor: getRandomColorFromString(name),
                    }}
                  >
                    <Typography.Text>
                      {name?.[0]?.toUpperCase()}
                    </Typography.Text>
                  </Avatar>
                  <Typography.Text>{name}</Typography.Text>
                </Flex>
              );
            }}
          />
          <Table.Column
            title="Actions"
            key="actions"
            fixed="right"
            align="end"
            width={106}
            render={(_, record: Client) => {
              return (
                <Flex align="center" gap={8}>
                  <EditButton
                    hideText
                    recordItemId={record.id}
                    icon={<EyeOutlined />}
                  />
                  <DeleteButton hideText recordItemId={record.id} />
                </Flex>
              );
            }}
          />
        </Table>
      </List>
      {children}
    </>
  );
};
```

]

Create `src/pages/clients/create.tsx` file with the following code:

[details Show `<ClientsPageCreate />` code

```typescript
[label src/pages/clients/create.tsx]
import { useGo } from "@refinedev/core";
import { useForm, useSelect } from "@refinedev/antd";
import { Flex, Form, Input, Modal, Select } from "antd";
import InputMask from "react-input-mask";

export const ClientsPageCreate = () => {
  const go = useGo();

  const { formProps } = useForm();

  const { selectProps: selectPropsAccount } = useSelect({
    resource: "accounts",
    optionLabel: "company_name",
    optionValue: "id",
  });

  return (
    <Modal
      okButtonProps={{ form: "create-client-form", htmlType: "submit" }}
      title="Add new client"
      open
      onCancel={() => {
        go({
          to: { resource: "accounts", action: "list" },
          options: { keepQuery: true },
        });
      }}
    >
      <Form layout="vertical" id="create-client-form" {...formProps}>
        <Flex
          vertical
          style={{
            margin: "0 auto",
            width: "420px",
          }}
        >
          <Form.Item
            name="account"
            label="Account"
            rules={[{ required: true }]}
          >
            <Select
              {...selectPropsAccount}
              placeholder="Please select an account"
            />
          </Form.Item>
          <Form.Item
            name="name"
            label="Client title"
            rules={[{ required: true }]}
          >
            <Input placeholder="Please enter client title" />
          </Form.Item>
          <Form.Item
            name="owner_name"
            label="Owner name"
            rules={[{ required: true }]}
          >
            <Input placeholder="Please enter owner name" />
          </Form.Item>
          <Form.Item
            name="owner_email"
            label="Owner email"
            rules={[{ required: true, type: "email" }]}
          >
            <Input placeholder="Please enter owner email" />
          </Form.Item>
          <Form.Item
            name="address"
            label="Address"
            rules={[{ required: true }]}
          >
            <Input placeholder="Please enter address" />
          </Form.Item>
          <Form.Item name="phone" label="Phone" rules={[{ required: true }]}>
            <InputMask mask="(999) 999-9999">
              {/* @ts-expect-error  <InputMask /> expects JSX.Element but we are using React.ReactNode */}
              {(props: InputProps) => (
                <Input {...props} placeholder="Please enter phone number" />
              )}
            </InputMask>
          </Form.Item>
        </Flex>
      </Form>
    </Modal>
  );
};

```

]

Create `src/pages/clients/edit.tsx` file with the following code:

[details Show `<ClientsPageEdit />` code

```typescript
[label src/pages/clients/edit.tsx]
import { useNavigation } from "@refinedev/core";
import {
  DateField,
  DeleteButton,
  NumberField,
  Show,
  ShowButton,
  useForm,
  useSelect,
} from "@refinedev/antd";
import { Card, Divider, Flex, Form, Table, Typography } from "antd";
import {
  ShopOutlined,
  UserOutlined,
  ExportOutlined,
  BankOutlined,
  MailOutlined,
  GlobalOutlined,
  EnvironmentOutlined,
  PhoneOutlined,
  ContainerOutlined,
} from "@ant-design/icons";
import { Col, Row } from "antd";
import {
  FormItemEditableInputText,
  FormItemEditableText,
  FormItemEditableSelect,
} from "@/components/form";
import { Invoice } from "@/types";

export const ClientsPageEdit = () => {
  const { listUrl } = useNavigation();

  const { formProps, queryResult } = useForm({
    redirect: false,
    meta: {
      populate: ["account", "invoices.client", "invoices.account.logo"],
    },
  });

  const { selectProps: selectPropsAccount } = useSelect({
    resource: "accounts",
    optionLabel: "company_name",
    optionValue: "id",
  });

  const invoices = queryResult?.data?.data?.invoices || [];
  const isLoading = queryResult?.isLoading;

  return (
    <Show
      title="Clients"
      headerButtons={() => false}
      contentProps={{
        styles: {
          body: {
            padding: 0,
          },
        },
        style: {
          background: "transparent",
          boxShadow: "none",
        },
      }}
    >
      <Form {...formProps} layout="vertical">
        <Row>
          <Col span={24}>
            <Flex gap={16}>
              <FormItemEditableText
                loading={isLoading}
                formItemProps={{
                  name: "name",
                  rules: [{ required: true }],
                }}
              />
            </Flex>
          </Col>
        </Row>
        <Row
          gutter={32}
          style={{
            marginTop: "32px",
          }}
        >
          <Col xs={{ span: 24 }} xl={{ span: 8 }}>
            <Card
              bordered={false}
              styles={{ body: { padding: 0 } }}
              title={
                <Flex gap={12} align="center">
                  <ShopOutlined />
                  <Typography.Text>Client info</Typography.Text>
                </Flex>
              }
            >
              <FormItemEditableSelect
                loading={isLoading}
                icon={<BankOutlined />}
                editIcon={<ExportOutlined />}
                selectProps={{
                  showSearch: true,
                  placeholder: "Select account",
                  ...selectPropsAccount,
                }}
                formItemProps={{
                  name: "account",
                  getValueProps: (value) => {
                    return {
                      value: value?.id,
                      label: value?.company_name,
                    };
                  },
                  label: "Account",
                  rules: [{ required: true }],
                }}
              />
              <FormItemEditableInputText
                loading={isLoading}
                icon={<UserOutlined />}
                placeholder="Add owner name"
                formItemProps={{
                  name: "owner_name",
                  label: "Owner name",
                  rules: [{ required: true }],
                }}
              />
              <Divider style={{ margin: 0 }} />
              <FormItemEditableInputText
                loading={isLoading}
                icon={<MailOutlined />}
                placeholder="Add email"
                formItemProps={{
                  name: "owner_email",
                  label: "Owner email",
                  rules: [{ required: true, type: "email" }],
                }}
              />
              <Divider style={{ margin: 0 }} />
              <Divider style={{ margin: 0 }} />
              <FormItemEditableInputText
                loading={isLoading}
                icon={<EnvironmentOutlined />}
                placeholder="Add address"
                formItemProps={{
                  name: "address",
                  label: "Address",
                  rules: [{ required: true }],
                }}
              />
              <Divider style={{ margin: 0 }} />
              <FormItemEditableInputText
                loading={isLoading}
                icon={<PhoneOutlined />}
                placeholder="Add phone number"
                formItemProps={{
                  name: "phone",
                  label: "Phone",
                  rules: [{ required: true }],
                }}
              />
            </Card>
            <DeleteButton
              type="text"
              style={{
                marginTop: "16px",
              }}
              onSuccess={() => {
                listUrl("clients");
              }}
            >
              Delete client
            </DeleteButton>
          </Col>

          <Col xs={{ span: 24 }} xl={{ span: 16 }}>
            <Card
              bordered={false}
              title={
                <Flex gap={12} align="center">
                  <ContainerOutlined />
                  <Typography.Text>Invoices</Typography.Text>
                </Flex>
              }
              styles={{
                header: {
                  padding: "0 16px",
                },
                body: {
                  padding: 0,
                },
              }}
            >
              <Table
                dataSource={invoices}
                pagination={false}
                loading={isLoading}
                rowKey={"id"}
              >
                <Table.Column title="ID" dataIndex="id" key="id" width={72} />
                <Table.Column
                  title="Date"
                  dataIndex="date"
                  key="date"
                  render={(date) => (
                    <DateField value={date} format="D MMM YYYY" />
                  )}
                />
                <Table.Column
                  title="Client"
                  dataIndex="client"
                  key="client"
                  render={(client) => client?.name}
                />
                <Table.Column
                  title="Amount"
                  dataIndex="total"
                  key="total"
                  render={(total) => (
                    <NumberField
                      value={total}
                      options={{ style: "currency", currency: "USD" }}
                    />
                  )}
                />
                <Table.Column
                  key="actions"
                  width={64}
                  render={(_, record: Invoice) => {
                    return (
                      <Flex align="center" gap={8}>
                        <ShowButton
                          hideText
                          resource="invoices"
                          recordItemId={record.id}
                          icon={<ExportOutlined />}
                        />
                      </Flex>
                    );
                  }}
                />
              </Table>
            </Card>
          </Col>
        </Row>
      </Form>
    </Show>
  );
};


```

]

After creating the CRUD pages, let's create a `src/pages/clients/index.ts` file to export the pages as follows:

```typescript
[label src/pages/clients/index.ts]
export { ClientsPageList } from "./list";
export { ClientsPageCreate } from "./create";
export { ClientsPageEdit } from "./edit";
```

To render the clients CRUD pages, let's update the `src/App.tsx` file with the following code:

[details Show `src/App.tsx` code

```typescript
[label src/App.tsx]

import { Authenticated, Refine } from "@refinedev/core";
import {
  AuthPage,
  ErrorComponent,
  ThemedLayoutV2,
  useNotificationProvider,
} from "@refinedev/antd";
import routerProvider, {
  UnsavedChangesNotifier,
  DocumentTitleHandler,
  NavigateToResource,
  CatchAllNavigate,
} from "@refinedev/react-router-v6";
import { BrowserRouter, Outlet, Route, Routes } from "react-router-dom";
import { DevtoolsPanel, DevtoolsProvider } from "@refinedev/devtools";
import { App as AntdApp } from "antd";
import { dataProvider } from "@/providers/data-provider";
import { authProvider } from "@/providers/auth-provider";
import { ConfigProvider } from "@/providers/config-provider";
import { Logo } from "@/components/logo";
import { Header } from "@/components/header";
import {
  AccountsPageCreate,
  AccountsPageEdit,
  AccountsPageList,
} from "@/pages/accounts";
import {
  ClientsPageCreate,
  ClientsPageEdit,
  ClientsPageList,
} from "@/pages/clients";
import "@refinedev/antd/dist/reset.css";
import "./styles/custom.css";

const App: React.FC = () => {
  return (
    <DevtoolsProvider>
      <BrowserRouter>
        <ConfigProvider>
          <AntdApp>
            <Refine
              routerProvider={routerProvider}
              authProvider={authProvider}
              dataProvider={dataProvider}
              notificationProvider={useNotificationProvider}
              resources={[
                {
                  name: "accounts",
                  list: "/accounts",
                  create: "/accounts/new",
                  edit: "/accounts/:id/edit",
                },
                {
                  name: "clients",
                  list: "/clients",
                  create: "/clients/new",
                  edit: "/clients/:id/edit",
                },
              ]}
              options={{
                syncWithLocation: true,
                warnWhenUnsavedChanges: true,
                breadcrumb: false,
              }}
            >
              <Routes>
                <Route
                  element={
                    <Authenticated
                      key="authenticated-routes"
                      fallback={<CatchAllNavigate to="/login" />}
                    >
                      <ThemedLayoutV2
                        Header={() => <Header />}
                        Sider={() => null}
                      >
                        <div
                          style={{
                            maxWidth: "1280px",
                            padding: "24px",
                            margin: "0 auto",
                          }}
                        >
                          <Outlet />
                        </div>
                      </ThemedLayoutV2>
                    </Authenticated>
                  }
                >
                  <Route index element={<NavigateToResource />} />

                  <Route
                    path="/accounts"
                    element={
                      <AccountsPageList>
                        <Outlet />
                      </AccountsPageList>
                    }
                  >
                    <Route index element={null} />
                    <Route path="new" element={<AccountsPageCreate />} />
                  </Route>
                  <Route
                    path="/accounts/:id/edit"
                    element={<AccountsPageEdit />}
                  />

                  <<^>Route<^>
                    <^>path="/clients"<^>
                    <^>element={<^>
                      <^><ClientsPageList><^>
                        <^><Outlet /><^>
                      <^></ClientsPageList><^>
                    <^>}<^>
                  <^>><^>
                    <^><Route index element={null} /><^>
                    <^><Route path="new" element={<ClientsPageCreate />} /><^>
                  <^></Route><^>
                  <^><Route<^>
                    <^>path="/clients/:id/edit"<^>
                    <^>element={<ClientsPageEdit />}<^>
                  <^>/><^>
                <^></Route><^>

                <Route
                  element={
                    <Authenticated key="auth-pages" fallback={<Outlet />}>
                      <NavigateToResource />
                    </Authenticated>
                  }
                >
                  <Route
                    path="/login"
                    element={
                      <AuthPage
                        type="login"
                        registerLink={false}
                        forgotPasswordLink={false}
                        title={
                          <Logo
                            titleProps={{ level: 2 }}
                            svgProps={{
                              width: "48px",
                              height: "40px",
                            }}
                          />
                        }
                        formProps={{
                          initialValues: {
                            email: "demo@refine.dev",
                            password: "demodemo",
                          },
                        }}
                      />
                    }
                  />
                </Route>

                <Route
                  element={
                    <Authenticated key="catch-all">
                      <ThemedLayoutV2
                        Header={() => <Header />}
                        Sider={() => null}
                      >
                        <Outlet />
                      </ThemedLayoutV2>
                    </Authenticated>
                  }
                >
                  <Route path="*" element={<ErrorComponent />} />
                </Route>
              </Routes>
              <UnsavedChangesNotifier />
              <DocumentTitleHandler />
            </Refine>
          </AntdApp>
        </ConfigProvider>
        <DevtoolsPanel />
      </BrowserRouter>
    </DevtoolsProvider>
  );
};

export default App;
```

]

After these changes, you should be able to navigate to the clients CRUD pages as the below:

[slideshow https://refine.ams3.cdn.digitaloceanspaces.com/blog/invoicer/clients-list.png https://refine.ams3.cdn.digitaloceanspaces.com/blog/invoicer/clients-new.png https://refine.ams3.cdn.digitaloceanspaces.com/blog/invoicer/clients-edit.png]

## Step 6 — Adding Invoices CRUD Pages

In this step we will build the invoices CRUD pages.

Invoices will be used to store information about the invoices sent to clients and accounts. It will have a required relationship with the client and account. Each client and account can have multiple invoices, but each invoice can only belong to one client and account.

Invoices contain the following fields:

- **Client**: The recipient of the invoice.
- **Account**: The sender of the invoice.
- **Date**: The creation date of the invoice.
- **Total**: The amount due after discount and tax.
- **Discount**: The discount applied to the invoice.
- **Tax**: The tax amount on the invoice.
- **Services**: The services listed on the invoice, including:
  - **Title**: The name of the service.
  - **Discount**: The discount applied to the service.
  - **Quantity**: The quantity of the service.
  - **Unit Price**: The unit price of the service.
  - **Total**: The total amount for the service after the discount.

After the user creates an invoice with these fields, they will be able to view, edit, delete, and Export as PDF the invoice.

Let's start by defining the `"invoices"` resource in the `src/App.tsx` file as follows:

[details Show `src/App.tsx` code

```typescript
[label src/App.tsx]
import { Authenticated, Refine } from '@refinedev/core'
import { AuthPage, ErrorComponent, ThemedLayoutV2, useNotificationProvider } from '@refinedev/antd'
import routerProvider, {
  UnsavedChangesNotifier,
  DocumentTitleHandler,
  NavigateToResource,
  CatchAllNavigate,
} from '@refinedev/react-router-v6'
import { BrowserRouter, Outlet, Route, Routes } from 'react-router-dom'
import { DevtoolsPanel, DevtoolsProvider } from '@refinedev/devtools'
import { App as AntdApp } from 'antd'
import { dataProvider } from '@/providers/data-provider'
import { authProvider } from '@/providers/auth-provider'
import { ConfigProvider } from '@/providers/config-provider'
import { Logo } from '@/components/logo'
import { Header } from '@/components/header'
import { AccountsPageCreate, AccountsPageEdit, AccountsPageList } from '@/pages/accounts'
import { ClientsPageCreate, ClientsPageEdit, ClientsPageList } from '@/pages/clients'
import '@refinedev/antd/dist/reset.css'
import './styles/custom.css'

const App: React.FC = () => {
  return (
    <DevtoolsProvider>
      <BrowserRouter>
        <ConfigProvider>
          <AntdApp>
            <Refine
              routerProvider={routerProvider}
              authProvider={authProvider}
              dataProvider={dataProvider}
              notificationProvider={useNotificationProvider}
              resources={[
                {
                  name: "accounts",
                  list: "/accounts",
                  create: "/accounts/new",
                  edit: "/accounts/:id/edit",
                },
                {
                  name: "clients",
                  list: "/clients",
                  create: "/clients/new",
                  edit: "/clients/:id/edit",
                },
                <^>{<^>
                  <^>name: "invoices",<^>
                  <^>list: "/invoices",<^>
                  <^>show: "/invoices/:id",<^>
                  <^>create: "/invoices/new",<^>
                <^>},<^>
              ]}
              options={{
                syncWithLocation: true,
                warnWhenUnsavedChanges: true,
                breadcrumb: false,
              }}>
              <Routes>
                <Route
                  element={
                    <Authenticated key="authenticated-routes" fallback={<CatchAllNavigate to="/login" />}>
                      <ThemedLayoutV2 Header={() => <Header />} Sider={() => null}>
                        <div
                          style={{
                            maxWidth: "1280px",
                            padding: "24px",
                            margin: "0 auto",
                          }}>
                          <Outlet />
                        </div>
                      </ThemedLayoutV2>
                    </Authenticated>
                  }>
                  <Route index element={<NavigateToResource />} />

                  <Route
                    path="/accounts"
                    element={
                      <AccountsPageList>
                        <Outlet />
                      </AccountsPageList>
                    }>
                    <Route index element={null} />
                    <Route path="new" element={<AccountsPageCreate />} />
                  </Route>
                  <Route path="/accounts/:id/edit" element={<AccountsPageEdit />} />

                  <Route
                    path="/clients"
                    element={
                      <ClientsPageList>
                        <Outlet />
                      </ClientsPageList>
                    }>
                    <Route index element={null} />
                    <Route path="new" element={<ClientsPageCreate />} />
                  </Route>
                  <Route path="/clients/:id/edit" element={<ClientsPageEdit />} />
                </Route>

                <Route
                  element={
                    <Authenticated key="auth-pages" fallback={<Outlet />}>
                      <NavigateToResource />
                    </Authenticated>
                  }>
                  <Route
                    path="/login"
                    element={
                      <AuthPage
                        type="login"
                        registerLink={false}
                        forgotPasswordLink={false}
                        title={
                          <Logo
                            titleProps={{ level: 2 }}
                            svgProps={{
                              width: "48px",
                              height: "40px",
                            }}
                          />
                        }
                        formProps={{
                          initialValues: {
                            email: "demo@refine.dev",
                            password: "demodemo",
                          },
                        }}
                      />
                    }
                  />
                </Route>

                <Route
                  element={
                    <Authenticated key="catch-all">
                      <ThemedLayoutV2 Header={() => <Header />} Sider={() => null}>
                        <Outlet />
                      </ThemedLayoutV2>
                    </Authenticated>
                  }>
                  <Route path="*" element={<ErrorComponent />} />
                </Route>
              </Routes>
              <UnsavedChangesNotifier />
              <DocumentTitleHandler />
            </Refine>
          </AntdApp>
        </ConfigProvider>
        <DevtoolsPanel />
      </BrowserRouter>
    </DevtoolsProvider>
  )
}

export default App


```

]

### List Page

We will start by creating the list page to display all created invoices.

Let's create the `src/pages/invoices/list.tsx` file with the following code:

[details Show `<InvoicesPageList />` code

```typescript
[label src/pages/invoices/list.tsx]
import { getDefaultFilter, useGo } from "@refinedev/core";
import {
  CreateButton,
  DateField,
  DeleteButton,
  FilterDropdown,
  List,
  NumberField,
  ShowButton,
  getDefaultSortOrder,
  useSelect,
  useTable,
} from "@refinedev/antd";
import { Avatar, Flex, Input, Select, Table, Typography } from "antd";
import { EyeOutlined, SearchOutlined } from "@ant-design/icons";
import { API_URL } from "@/utils/constants";
import { getRandomColorFromString } from "@/utils/get-random-color";
import { Invoice } from "@/types";

export const InvoicePageList = () => {
  const go = useGo();

  const { tableProps, filters, sorters } = useTable<Invoice>({
    meta: {
      populate: ["client", "account.logo"],
    },
    sorters: {
      initial: [{ field: "updatedAt", order: "desc" }],
    },
  });

  const { selectProps: selectPropsAccounts } = useSelect({
    resource: "accounts",
    optionLabel: "company_name",
    optionValue: "company_name",
  });

  const { selectProps: selectPropsClients } = useSelect({
    resource: "clients",
    optionLabel: "name",
    optionValue: "name",
  });

  return (
    <List
      title="Invoices"
      headerButtons={() => {
        return (
          <CreateButton
            size="large"
            onClick={() =>
              go({
                to: { resource: "invoices", action: "create" },
                options: { keepQuery: true },
              })
            }
          >
            Add new invoice
          </CreateButton>
        );
      }}
    >
      <Table
        {...tableProps}
        rowKey={"id"}
        pagination={{
          ...tableProps.pagination,
          showSizeChanger: true,
        }}
        scroll={{ x: 960 }}
      >
        <Table.Column
          title="ID"
          dataIndex="id"
          key="id"
          width={80}
          defaultFilteredValue={getDefaultFilter("id", filters)}
          filterIcon={<SearchOutlined />}
          filterDropdown={(props) => {
            return (
              <FilterDropdown {...props}>
                <Input placeholder="Search ID" />
              </FilterDropdown>
            );
          }}
        />
        <Table.Column
          title="Account"
          dataIndex="account.company_name"
          key="account.company_name"
          defaultFilteredValue={getDefaultFilter(
            "account.company_name",
            filters,
            "in",
          )}
          filterDropdown={(props) => (
            <FilterDropdown {...props}>
              <Select
                mode="multiple"
                placeholder="Search Account"
                style={{ width: 220 }}
                {...selectPropsAccounts}
              />
            </FilterDropdown>
          )}
          render={(_, record: Invoice) => {
            const logoUrl = record?.account?.logo?.url;
            const src = logoUrl ? `${API_URL}${logoUrl}` : undefined;
            const name = record?.account?.company_name;

            return (
              <Flex align="center" gap={8}>
                <Avatar
                  alt={name}
                  src={src}
                  shape="square"
                  style={{
                    backgroundColor: getRandomColorFromString(name),
                  }}
                >
                  <Typography.Text>{name?.[0]?.toUpperCase()}</Typography.Text>
                </Avatar>
                <Typography.Text>{name}</Typography.Text>
              </Flex>
            );
          }}
        />
        <Table.Column
          title="Client"
          dataIndex="client.name"
          key="client.name"
          render={(_, record: Invoice) => {
            return <Typography.Text>{record.client?.name}</Typography.Text>;
          }}
          defaultFilteredValue={getDefaultFilter("company_name", filters, "in")}
          filterDropdown={(props) => (
            <FilterDropdown {...props}>
              <Select
                mode="multiple"
                placeholder="Search Company Name"
                style={{ width: 220 }}
                {...selectPropsClients}
              />
            </FilterDropdown>
          )}
        />
        <Table.Column
          title="Date"
          dataIndex="date"
          key="date"
          width={120}
          sorter
          defaultSortOrder={getDefaultSortOrder("date", sorters)}
          render={(date) => {
            return <DateField value={date} format="D MMM YYYY" />;
          }}
        />
        <Table.Column
          title="Total"
          dataIndex="total"
          key="total"
          width={132}
          align="end"
          sorter
          defaultSortOrder={getDefaultSortOrder("total", sorters)}
          render={(total) => {
            return (
              <NumberField
                value={total}
                options={{ style: "currency", currency: "USD" }}
              />
            );
          }}
        />
        <Table.Column
          title="Actions"
          key="actions"
          fixed="right"
          align="end"
          width={102}
          render={(_, record: Invoice) => {
            return (
              <Flex align="center" gap={8}>
                <ShowButton
                  hideText
                  recordItemId={record.id}
                  icon={<EyeOutlined />}
                />
                <DeleteButton hideText recordItemId={record.id} />
              </Flex>
            );
          }}
        />
      </Table>
    </List>
  );
};

```

]

After creating the list page, let's create a `src/pages/invoices/index.ts` file to export the pages as follows:

```typescript
[label src/pages/invoices/index.ts]
export { InvoicePageList } from "./list";
```

Now we are ready to add our "list" page to the `src/App.tsx` file as follows:

[details Show `src/App.tsx` code

```typescript
[label src/App.tsx]
import { Authenticated, Refine } from "@refinedev/core";
import {
  AuthPage,
  ErrorComponent,
  ThemedLayoutV2,
  useNotificationProvider,
} from "@refinedev/antd";
import routerProvider, {
  UnsavedChangesNotifier,
  DocumentTitleHandler,
  NavigateToResource,
  CatchAllNavigate,
} from "@refinedev/react-router-v6";
import { BrowserRouter, Outlet, Route, Routes } from "react-router-dom";
import { DevtoolsPanel, DevtoolsProvider } from "@refinedev/devtools";
import { App as AntdApp } from "antd";
import { dataProvider } from "@/providers/data-provider";
import { authProvider } from "@/providers/auth-provider";
import { ConfigProvider } from "@/providers/config-provider";
import { Logo } from "@/components/logo";
import { Header } from "@/components/header";
import {
  AccountsPageCreate,
  AccountsPageEdit,
  AccountsPageList,
} from "@/pages/accounts";
import {
  ClientsPageCreate,
  ClientsPageEdit,
  ClientsPageList,
} from "@/pages/clients";
<^>import { InvoicePageList } from "@/pages/invoices"<^>
import "@refinedev/antd/dist/reset.css";
import "./styles/custom.css";

const App: React.FC = () => {
  return (
    <DevtoolsProvider>
      <BrowserRouter>
        <ConfigProvider>
          <AntdApp>
            <Refine
              routerProvider={routerProvider}
              authProvider={authProvider}
              dataProvider={dataProvider}
              notificationProvider={useNotificationProvider}
              resources={[
                {
                  name: "accounts",
                  list: "/accounts",
                  create: "/accounts/new",
                  edit: "/accounts/:id/edit",
                },
                {
                  name: "clients",
                  list: "/clients",
                  create: "/clients/new",
                  edit: "/clients/:id/edit",
                },
                {
                  name: "invoices",
                  list: "/invoices",
                  show: "/invoices/:id",
                  create: "/invoices/new",
                },
              ]}
              options={{
                syncWithLocation: true,
                warnWhenUnsavedChanges: true,
                breadcrumb: false,
              }}
            >
              <Routes>
                <Route
                  element={
                    <Authenticated
                      key="authenticated-routes"
                      fallback={<CatchAllNavigate to="/login" />}
                    >
                      <ThemedLayoutV2
                        Header={() => <Header />}
                        Sider={() => null}
                      >
                        <div
                          style={{
                            maxWidth: "1280px",
                            padding: "24px",
                            margin: "0 auto",
                          }}
                        >
                          <Outlet />
                        </div>
                      </ThemedLayoutV2>
                    </Authenticated>
                  }
                >
                  <Route index element={<NavigateToResource />} />

                  <Route
                    path="/accounts"
                    element={
                      <AccountsPageList>
                        <Outlet />
                      </AccountsPageList>
                    }
                  >
                    <Route index element={null} />
                    <Route path="new" element={<AccountsPageCreate />} />
                  </Route>
                  <Route
                    path="/accounts/:id/edit"
                    element={<AccountsPageEdit />}
                  />

                  <Route
                    path="/clients"
                    element={
                      <ClientsPageList>
                        <Outlet />
                      </ClientsPageList>
                    }
                  >
                    <Route index element={null} />
                    <Route path="new" element={<ClientsPageCreate />} />
                  </Route>
                  <Route
                    path="/clients/:id/edit"
                    element={<ClientsPageEdit />}
                  />
                </Route>

                <^><Route path="/invoices"><^>
                  <^><Route index element={<InvoicePageList />} /><^>
                <^></Route><^>

                <Route
                  element={
                    <Authenticated key="auth-pages" fallback={<Outlet />}>
                      <NavigateToResource />
                    </Authenticated>
                  }
                >
                  <Route
                    path="/login"
                    element={
                      <AuthPage
                        type="login"
                        registerLink={false}
                        forgotPasswordLink={false}
                        title={
                          <Logo
                            titleProps={{ level: 2 }}
                            svgProps={{
                              width: "48px",
                              height: "40px",
                            }}
                          />
                        }
                        formProps={{
                          initialValues: {
                            email: "demo@refine.dev",
                            password: "demodemo",
                          },
                        }}
                      />
                    }
                  />
                </Route>

                <Route
                  element={
                    <Authenticated key="catch-all">
                      <ThemedLayoutV2
                        Header={() => <Header />}
                        Sider={() => null}
                      >
                        <Outlet />
                      </ThemedLayoutV2>
                    </Authenticated>
                  }
                >
                  <Route path="*" element={<ErrorComponent />} />
                </Route>
              </Routes>
              <UnsavedChangesNotifier />
              <DocumentTitleHandler />
            </Refine>
          </AntdApp>
        </ConfigProvider>
        <DevtoolsPanel />
      </BrowserRouter>
    </DevtoolsProvider>
  );
};

export default App;
```

]

After these changes, you should be able to navigate to the invoice list pages as the below:

![Invoices List page](https://refine.ams3.cdn.digitaloceanspaces.com/blog/invoicer/invoices-list.png)

### Create Page

The `"invoices"` create page is very similar to the `"accounts"` create page, however, it requires specific custom styles and additional business logic to compute the service items for the invoice.

To begin, let's create the `src/pages/invoices/create.styled.tsx` file with the following code:

[details Show `src/pages/invoices/create.styled.tsx` code

```typescript
[label src/pages/invoices/create.styled.tsx]
import { createStyles } from "antd-style";

export const useStyles = createStyles(({ token, isDarkMode }) => {
  return {
    serviceTableWrapper: {
      overflow: "auto",
    },
    serviceTableContainer: {
      minWidth: "960px",
      borderRadius: "8px",
      border: `1px solid ${token.colorBorder}`,
    },
    serviceHeader: {
      background: isDarkMode ? "#1F1F1F" : "#FAFAFA",
      borderRadius: "8px 8px 0 0",
    },
    serviceHeaderDivider: {
      height: "24px",
      marginTop: "auto",
      marginBottom: "auto",
      marginInline: "0",
    },
    serviceHeaderColumn: {
      fontWeight: 600,
      display: "flex",
      alignItems: "center",
      justifyContent: "space-between",
      padding: "12px 16px",
    },
    serviceRowColumn: {
      display: "flex",
      alignItems: "center",
      padding: "12px 16px",
    },
    addNewServiceItemButton: {
      color: token.colorPrimary,
    },
    labelTotal: {
      color: token.colorTextSecondary,
    },
  };
});

```

]

To write CSS we used the `createStyles` function from the [`antd-style` package](https://github.com/ant-design/antd-style). This function accepts a callback function that provides the `token` and `isDarkMode` values. The `token` object contains the color values of the current theme, and the `isDarkMode` value indicates whether the current theme is dark or light.

Let's create the `src/pages/invoices/create.tsx` file with the following code:

[details Show `<InvoicesPageCreate />` code

```typescript
[label src/pages/invoices/create.tsx]
import { Fragment, useState } from "react";
import { NumberField, Show, useForm, useSelect } from "@refinedev/antd";
import {
  Button,
  Card,
  Col,
  Divider,
  Flex,
  Form,
  Input,
  InputNumber,
  Row,
  Select,
  Typography,
} from "antd";
import { DeleteOutlined, PlusCircleOutlined } from "@ant-design/icons";
import { Invoice, Service } from "@/types";
import { useStyles } from "./create.styled";

export const InvoicesPageCreate = () => {
  const [tax, setTax] = useState<number>(0);
  const [services, setServices] = useState<Service[]>([
    {
      title: "",
      unitPrice: 0,
      quantity: 0,
      discount: 0,
      totalPrice: 0,
    },
  ]);
  const subtotal = services.reduce(
    (acc, service) =>
      acc +
      (service.unitPrice * service.quantity * (100 - service.discount)) / 100,
    0,
  );
  const total = subtotal + (subtotal * tax) / 100;

  const { styles } = useStyles();

  const { formProps } = useForm<Invoice>();

  const { selectProps: selectPropsAccounts } = useSelect({
    resource: "accounts",
    optionLabel: "company_name",
    optionValue: "id",
  });

  const { selectProps: selectPropsClients } = useSelect({
    resource: "clients",
    optionLabel: "name",
    optionValue: "id",
  });

  const handleServiceNumbersChange = (
    index: number,
    key: "quantity" | "discount" | "unitPrice",
    value: number,
  ) => {
    setServices((prev) => {
      const currentService = { ...prev[index] };
      currentService[key] = value;
      currentService.totalPrice =
        currentService.unitPrice *
        currentService.quantity *
        ((100 - currentService.discount) / 100);

      return prev.map((item, i) => (i === index ? currentService : item));
    });
  };

  const onFinishHandler = (values: Invoice) => {
    const valuesWithServices = {
      ...values,
      total,
      tax,
      date: new Date().toISOString(),
      services: services.filter((service) => service.title),
    };

    formProps?.onFinish?.(valuesWithServices);
  };

  return (
    <Show
      title="Invoices"
      headerButtons={() => false}
      contentProps={{
        styles: {
          body: {
            padding: 0,
          },
        },
        style: {
          background: "transparent",
          boxShadow: "none",
        },
      }}
    >
      <Form
        {...formProps}
        onFinish={(values) => onFinishHandler(values as Invoice)}
        layout="vertical"
      >
        <Flex vertical gap={32}>
          <Typography.Title level={3}>New Invoice</Typography.Title>
          <Card
            bordered={false}
            styles={{
              body: {
                padding: 0,
              },
            }}
          >
            <Flex
              align="center"
              gap={40}
              wrap="wrap"
              style={{ padding: "32px" }}
            >
              <Form.Item
                label="Account"
                name="account"
                rules={[{ required: true }]}
                style={{ flex: 1 }}
              >
                <Select
                  {...selectPropsAccounts}
                  placeholder="Please select account"
                />
              </Form.Item>
              <Form.Item
                label="Client"
                name="client"
                rules={[{ required: true }]}
                style={{ flex: 1 }}
              >
                <Select
                  {...selectPropsClients}
                  placeholder="Please select client"
                />
              </Form.Item>
            </Flex>
            <Divider style={{ margin: 0 }} />
            <div style={{ padding: "32px" }}>
              <Typography.Title
                level={4}
                style={{ marginBottom: "32px", fontWeight: 400 }}
              >
                Products / Services
              </Typography.Title>
              <div className={styles.serviceTableWrapper}>
                <div className={styles.serviceTableContainer}>
                  <Row className={styles.serviceHeader}>
                    <Col
                      xs={{ span: 7 }}
                      className={styles.serviceHeaderColumn}
                    >
                      Title
                      <Divider
                        type="vertical"
                        className={styles.serviceHeaderDivider}
                      />
                    </Col>
                    <Col
                      xs={{ span: 5 }}
                      className={styles.serviceHeaderColumn}
                    >
                      Unit Price
                      <Divider
                        type="vertical"
                        className={styles.serviceHeaderDivider}
                      />
                    </Col>
                    <Col
                      xs={{ span: 4 }}
                      className={styles.serviceHeaderColumn}
                    >
                      Quantity
                      <Divider
                        type="vertical"
                        className={styles.serviceHeaderDivider}
                      />
                    </Col>
                    <Col
                      xs={{ span: 4 }}
                      className={styles.serviceHeaderColumn}
                    >
                      Discount
                      <Divider
                        type="vertical"
                        className={styles.serviceHeaderDivider}
                      />
                    </Col>
                    <Col
                      xs={{ span: 3 }}
                      style={{
                        display: "flex",
                        alignItems: "center",
                        justifyContent: "flex-end",
                      }}
                      className={styles.serviceHeaderColumn}
                    >
                      Total Price
                    </Col>
                    <Col xs={{ span: 1 }}> </Col>
                  </Row>
                  <Row>
                    {services.map((service, index) => {
                      return (
                        // biome-ignore lint/suspicious/noArrayIndexKey: We don't have a unique key for each service item when we create a new one
                        <Fragment key={index}>
                          <Col
                            xs={{ span: 7 }}
                            className={styles.serviceRowColumn}
                          >
                            <Input
                              placeholder="Title"
                              value={service.title}
                              onChange={(e) => {
                                setServices((prev) =>
                                  prev.map((item, i) =>
                                    i === index
                                      ? { ...item, title: e.target.value }
                                      : item,
                                  ),
                                );
                              }}
                            />
                          </Col>
                          <Col
                            xs={{ span: 5 }}
                            className={styles.serviceRowColumn}
                          >
                            <InputNumber
                              addonBefore="$"
                              style={{ width: "100%" }}
                              placeholder="Unit Price"
                              min={0}
                              value={service.unitPrice}
                              onChange={(value) => {
                                handleServiceNumbersChange(
                                  index,
                                  "unitPrice",
                                  value || 0,
                                );
                              }}
                            />
                          </Col>
                          <Col
                            xs={{ span: 4 }}
                            className={styles.serviceRowColumn}
                          >
                            <InputNumber
                              style={{ width: "100%" }}
                              placeholder="Quantity"
                              min={0}
                              value={service.quantity}
                              onChange={(value) => {
                                handleServiceNumbersChange(
                                  index,
                                  "quantity",
                                  value || 0,
                                );
                              }}
                            />
                          </Col>
                          <Col
                            xs={{ span: 4 }}
                            className={styles.serviceRowColumn}
                          >
                            <InputNumber
                              addonAfter="%"
                              style={{ width: "100%" }}
                              placeholder="Discount"
                              min={0}
                              value={service.discount}
                              onChange={(value) => {
                                handleServiceNumbersChange(
                                  index,
                                  "discount",
                                  value || 0,
                                );
                              }}
                            />
                          </Col>
                          <Col
                            xs={{ span: 3 }}
                            className={styles.serviceRowColumn}
                            style={{
                              justifyContent: "flex-end",
                            }}
                          >
                            <NumberField
                              value={service.totalPrice}
                              options={{ style: "currency", currency: "USD" }}
                            />
                          </Col>
                          <Col
                            xs={{ span: 1 }}
                            className={styles.serviceRowColumn}
                            style={{
                              paddingLeft: "0",
                              justifyContent: "flex-end",
                            }}
                          >
                            <Button
                              danger
                              size="small"
                              icon={<DeleteOutlined />}
                              onClick={() => {
                                setServices((prev) =>
                                  prev.filter((_, i) => i !== index),
                                );
                              }}
                            />
                          </Col>
                        </Fragment>
                      );
                    })}
                  </Row>
                  <Divider
                    style={{
                      margin: "0",
                    }}
                  />
                  <div style={{ padding: "12px" }}>
                    <Button
                      icon={<PlusCircleOutlined />}
                      type="text"
                      className={styles.addNewServiceItemButton}
                      onClick={() => {
                        setServices((prev) => [
                          ...prev,
                          {
                            title: "",
                            unitPrice: 0,
                            quantity: 0,
                            discount: 0,
                            totalPrice: 0,
                          },
                        ]);
                      }}
                    >
                      Add new item
                    </Button>
                  </div>
                </div>
              </div>
              <Flex
                gap={16}
                vertical
                style={{
                  marginLeft: "auto",
                  marginTop: "24px",
                  width: "220px",
                }}
              >
                <Flex
                  justify="space-between"
                  style={{
                    paddingLeft: 32,
                  }}
                >
                  <Typography.Text className={styles.labelTotal}>
                    Subtotal:
                  </Typography.Text>
                  <NumberField
                    value={subtotal}
                    options={{ style: "currency", currency: "USD" }}
                  />
                </Flex>
                <Flex
                  align="center"
                  justify="space-between"
                  style={{
                    paddingLeft: 32,
                  }}
                >
                  <Typography.Text className={styles.labelTotal}>
                    Sales tax:
                  </Typography.Text>
                  <InputNumber
                    addonAfter="%"
                    style={{ width: "96px" }}
                    value={tax}
                    min={0}
                    onChange={(value) => {
                      setTax(value || 0);
                    }}
                  />
                </Flex>
                <Divider
                  style={{
                    margin: "0",
                  }}
                />
                <Flex
                  justify="space-between"
                  style={{
                    paddingLeft: 16,
                  }}
                >
                  <Typography.Text
                    className={styles.labelTotal}
                    style={{
                      fontWeight: 700,
                    }}
                  >
                    Total value:
                  </Typography.Text>
                  <NumberField
                    value={total}
                    options={{ style: "currency", currency: "USD" }}
                  />
                </Flex>
              </Flex>
            </div>
            <Divider style={{ margin: 0 }} />
            <Flex justify="end" gap={8} style={{ padding: "32px" }}>
              <Button>Cancel</Button>
              <Button type="primary" htmlType="submit">
                Save
              </Button>
            </Flex>
          </Card>
        </Flex>
      </Form>
    </Show>
  );
};


```

]

We have created a form to create a new invoice. The form includes the account and client fields, and a table to add service items. The user can add multiple service items to the invoice. The total value of the invoice is calculated based on the subtotal and sales tax. `useSelect` is used to fetch the accounts and clients from the API and populate and manage to `<Select />` components to add the account and client relation to the invoice.

After creating the create page, let's create a `src/pages/invoices/index.ts` file to export the pages as follows:

```typescript
[label src/pages/invoices/index.ts]
export { InvoicePageList } from "./list";
<^>export { InvoicesPageCreate } from "./create";<^>
```

Now we are ready to add our "create" page to the `src/App.tsx` file as follows:

[details Show `src/App.tsx` code

```typescript
[label src/App.tsx]
import { Authenticated, Refine } from "@refinedev/core";
import {
  AuthPage,
  ErrorComponent,
  ThemedLayoutV2,
  useNotificationProvider,
} from "@refinedev/antd";
import routerProvider, {
  UnsavedChangesNotifier,
  DocumentTitleHandler,
  NavigateToResource,
  CatchAllNavigate,
} from "@refinedev/react-router-v6";
import { BrowserRouter, Outlet, Route, Routes } from "react-router-dom";
import { DevtoolsPanel, DevtoolsProvider } from "@refinedev/devtools";
import { App as AntdApp } from "antd";
import { dataProvider } from "@/providers/data-provider";
import { authProvider } from "@/providers/auth-provider";
import { ConfigProvider } from "@/providers/config-provider";
import { Logo } from "@/components/logo";
import { Header } from "@/components/header";
import {
  AccountsPageCreate,
  AccountsPageEdit,
  AccountsPageList,
} from "@/pages/accounts";
import {
  ClientsPageCreate,
  ClientsPageEdit,
  ClientsPageList,
} from "@/pages/clients";
<^>import { InvoicePageList, InvoicesPageCreate } from "@/pages/invoices"<^>
import "@refinedev/antd/dist/reset.css";
import "./styles/custom.css";

const App: React.FC = () => {
  return (
    <DevtoolsProvider>
      <BrowserRouter>
        <ConfigProvider>
          <AntdApp>
            <Refine
              routerProvider={routerProvider}
              authProvider={authProvider}
              dataProvider={dataProvider}
              notificationProvider={useNotificationProvider}
              resources={[
                {
                  name: "accounts",
                  list: "/accounts",
                  create: "/accounts/new",
                  edit: "/accounts/:id/edit",
                },
                {
                  name: "clients",
                  list: "/clients",
                  create: "/clients/new",
                  edit: "/clients/:id/edit",
                },
                {
                  name: "invoices",
                  list: "/invoices",
                  show: "/invoices/:id",
                  create: "/invoices/new",
                },
              ]}
              options={{
                syncWithLocation: true,
                warnWhenUnsavedChanges: true,
                breadcrumb: false,
              }}
            >
              <Routes>
                <Route
                  element={
                    <Authenticated
                      key="authenticated-routes"
                      fallback={<CatchAllNavigate to="/login" />}
                    >
                      <ThemedLayoutV2
                        Header={() => <Header />}
                        Sider={() => null}
                      >
                        <div
                          style={{
                            maxWidth: "1280px",
                            padding: "24px",
                            margin: "0 auto",
                          }}
                        >
                          <Outlet />
                        </div>
                      </ThemedLayoutV2>
                    </Authenticated>
                  }
                >
                  <Route index element={<NavigateToResource />} />

                  <Route
                    path="/accounts"
                    element={
                      <AccountsPageList>
                        <Outlet />
                      </AccountsPageList>
                    }
                  >
                    <Route index element={null} />
                    <Route path="new" element={<AccountsPageCreate />} />
                  </Route>
                  <Route
                    path="/accounts/:id/edit"
                    element={<AccountsPageEdit />}
                  />

                  <Route
                    path="/clients"
                    element={
                      <ClientsPageList>
                        <Outlet />
                      </ClientsPageList>
                    }
                  >
                    <Route index element={null} />
                    <Route path="new" element={<ClientsPageCreate />} />
                  </Route>
                  <Route
                    path="/clients/:id/edit"
                    element={<ClientsPageEdit />}
                  />
                </Route>

                <Route path="/invoices">
                  <Route index element={<InvoicePageList />} />
                  <^><Route path="new" element={<InvoicesPageCreate />} /><^>
                </Route>

                <Route
                  element={
                    <Authenticated key="auth-pages" fallback={<Outlet />}>
                      <NavigateToResource />
                    </Authenticated>
                  }
                >
                  <Route
                    path="/login"
                    element={
                      <AuthPage
                        type="login"
                        registerLink={false}
                        forgotPasswordLink={false}
                        title={
                          <Logo
                            titleProps={{ level: 2 }}
                            svgProps={{
                              width: "48px",
                              height: "40px",
                            }}
                          />
                        }
                        formProps={{
                          initialValues: {
                            email: "demo@refine.dev",
                            password: "demodemo",
                          },
                        }}
                      />
                    }
                  />
                </Route>

                <Route
                  element={
                    <Authenticated key="catch-all">
                      <ThemedLayoutV2
                        Header={() => <Header />}
                        Sider={() => null}
                      >
                        <Outlet />
                      </ThemedLayoutV2>
                    </Authenticated>
                  }
                >
                  <Route path="*" element={<ErrorComponent />} />
                </Route>
              </Routes>
              <UnsavedChangesNotifier />
              <DocumentTitleHandler />
            </Refine>
          </AntdApp>
        </ConfigProvider>
        <DevtoolsPanel />
      </BrowserRouter>
    </DevtoolsProvider>
  );
};

export default App;

```

]

After these changes, you should be able to navigate to the invoice create pages as the below:

![Invoices Create page](https://refine.ams3.cdn.digitaloceanspaces.com/blog/invoicer/invoices-new.png)

### Show Page

The show page includes the invoice's details, such as the account, client, and services. The user can export the invoice as a PDF by clicking the "Export PDF" button.

Let's create the `src/pages/invoices/show.styled.tsx` file with the following code:

[details Show `src/pages/invoices/show.styled.tsx` code

```typescript
[label src/pages/invoices/show.styled.tsx]
import { createStyles } from "antd-style";

export const useStyles = createStyles(({ token }) => {
  return {
    container: {
      ".ant-card-body": {
        padding: "0",
      },

      ".ant-card-head": {
        padding: "32px",
        background: token.colorBgContainer,
      },

      "@media print": {
        margin: "0 auto",
        minHeight: "100dvh",
        maxWidth: "892px",

        ".ant-card": {
          boxShadow: "none",
          border: "none",
        },

        ".ant-card-head": {
          padding: "0 !important",
        },

        ".ant-col": {
          maxWidth: "50% !important",
          flex: "0 0 50% !important",
        },

        table: {
          width: "unset !important",
        },

        ".ant-table-container::after": {
          content: "none",
        },
        ".ant-table-container::before": {
          content: "none",
        },
      },
    },
    fromToContainer: {
      minHeight: "192px",
      flexWrap: "nowrap",
      flexFlow: "row nowrap",
      padding: "32px",

      "@media print": {
        minHeight: "unset",
        padding: "32px 0",
      },
    },
    productServiceContainer: {
      padding: "32px",

      "@media print": {
        padding: "0",
        marginTop: "32px",
      },
    },
    labelTotal: {
      color: token.colorTextSecondary,
    },
  };
});

```

]

Let's create the `src/pages/invoices/show.tsx` file with the following code:

[details Show `<InvoicesPageShow />` code

```typescript
[label src/pages/invoices/show.tsx]
import { useShow } from "@refinedev/core";
import { FilePdfOutlined } from "@ant-design/icons";
import {
  Avatar,
  Card,
  Col,
  Divider,
  Flex,
  Row,
  Skeleton,
  Spin,
  Table,
  Typography,
} from "antd";
import { DateField, NumberField, Show } from "@refinedev/antd";
import { API_URL } from "@/utils/constants";
import { Invoice, Service } from "@/types";
import { Button } from "antd";
import { useStyles } from "./show.styled";
import { getRandomColorFromString } from "@/utils/get-random-color";

export const InvoicesPageShow = () => {
  const { styles } = useStyles();

  const { queryResult } = useShow<Invoice>({
    meta: {
      populate: ["client", "account.logo"],
    },
  });

  const invoice = queryResult?.data?.data;
  const loading = queryResult?.isLoading;

  return (
    <Show
      title="Invoices"
      headerButtons={() => (
        <>
          <Button
            disabled={!invoice}
            icon={<FilePdfOutlined />}
            onClick={() => window.print()}
          >
            Export PDF
          </Button>
        </>
      )}
      contentProps={{
        styles: {
          body: {
            padding: 0,
          },
        },
        style: {
          background: "transparent",
        },
      }}
    >
      <div id="invoice-pdf" className={styles.container}>
        <Card
          bordered={false}
          title={
            <Typography.Text
              style={{
                fontWeight: 400,
              }}
            >
              {loading ? (
                <Skeleton.Button style={{ width: 100, height: 22 }} />
              ) : (
                `Invoice ID #${invoice?.id}`
              )}
            </Typography.Text>
          }
          extra={
            <Flex gap={8} align="center">
              {loading ? (
                <Skeleton.Button style={{ width: 140, height: 22 }} />
              ) : (
                <>
                  <Typography.Text>Date:</Typography.Text>
                  <DateField
                    style={{ width: 84 }}
                    value={invoice?.date}
                    format="D MMM YYYY"
                  />
                </>
              )}
            </Flex>
          }
        >
          <Spin spinning={loading}>
            <Row className={styles.fromToContainer}>
              <Col xs={24} md={12}>
                <Flex vertical gap={24}>
                  <Typography.Text>From:</Typography.Text>
                  <Flex gap={24}>
                    <Avatar
                      alt={invoice?.account?.company_name}
                      size={64}
                      src={
                        invoice?.account?.logo?.url
                          ? `${API_URL}${invoice?.account?.logo?.url}`
                          : undefined
                      }
                      shape="square"
                      style={{
                        backgroundColor: getRandomColorFromString(
                          invoice?.account?.company_name || "",
                        ),
                      }}
                    >
                      <Typography.Text>
                        {invoice?.account?.company_name?.[0]?.toUpperCase()}
                      </Typography.Text>
                    </Avatar>
                    <Flex vertical gap={8}>
                      <Typography.Text
                        style={{
                          fontWeight: 700,
                        }}
                      >
                        {invoice?.account?.company_name}
                      </Typography.Text>
                      <Typography.Text>
                        {invoice?.account?.address}
                      </Typography.Text>
                      <Typography.Text>
                        {invoice?.account?.phone}
                      </Typography.Text>
                    </Flex>
                  </Flex>
                </Flex>
              </Col>
              <Col xs={24} md={12}>
                <Flex vertical gap={24} align="flex-end">
                  <Typography.Text>To:</Typography.Text>
                  <Flex vertical gap={8} align="flex-end">
                    <Typography.Text
                      style={{
                        fontWeight: 700,
                      }}
                    >
                      {invoice?.client?.name}
                    </Typography.Text>
                    <Typography.Text>
                      {invoice?.client?.address}
                    </Typography.Text>
                    <Typography.Text>{invoice?.client?.phone}</Typography.Text>
                  </Flex>
                </Flex>
              </Col>
            </Row>
          </Spin>

          <Divider
            style={{
              margin: 0,
            }}
          />
          <Flex vertical gap={24} className={styles.productServiceContainer}>
            <Typography.Title
              level={4}
              style={{
                margin: 0,
                fontWeight: 400,
              }}
            >
              Product / Services
            </Typography.Title>
            <Table
              dataSource={invoice?.services || []}
              rowKey={"id"}
              pagination={false}
              loading={loading}
              scroll={{ x: 960 }}
            >
              <Table.Column title="Title" dataIndex="title" key="title" />
              <Table.Column
                title="Unit Price"
                dataIndex="unitPrice"
                key="unitPrice"
                render={(unitPrice: number) => (
                  <NumberField
                    value={unitPrice}
                    options={{ style: "currency", currency: "USD" }}
                  />
                )}
              />
              <Table.Column
                title="Quantity"
                dataIndex="quantity"
                key="quantity"
              />
              <Table.Column
                title="Discount"
                dataIndex="discount"
                key="discount"
                render={(discount: number) => (
                  <Typography.Text>{`${discount}%`}</Typography.Text>
                )}
              />

              <Table.Column
                title="Total Price"
                dataIndex="total"
                key="total"
                align="right"
                width={128}
                render={(_, record: Service) => {
                  return (
                    <NumberField
                      value={record.totalPrice}
                      options={{ style: "currency", currency: "USD" }}
                    />
                  );
                }}
              />
            </Table>
            <Flex
              gap={16}
              vertical
              style={{
                marginLeft: "auto",
                marginTop: "24px",
                width: "200px",
              }}
            >
              <Flex
                justify="space-between"
                style={{
                  paddingLeft: 32,
                }}
              >
                <Typography.Text className={styles.labelTotal}>
                  Subtotal:
                </Typography.Text>
                <NumberField
                  value={invoice?.subTotal || 0}
                  options={{ style: "currency", currency: "USD" }}
                />
              </Flex>
              <Flex
                justify="space-between"
                style={{
                  paddingLeft: 32,
                }}
              >
                <Typography.Text className={styles.labelTotal}>
                  Sales tax:
                </Typography.Text>
                <Typography.Text>{invoice?.tax || 0}%</Typography.Text>
              </Flex>
              <Divider
                style={{
                  margin: "0",
                }}
              />
              <Flex
                justify="space-between"
                style={{
                  paddingLeft: 16,
                }}
              >
                <Typography.Text
                  className={styles.labelTotal}
                  style={{
                    fontWeight: 700,
                  }}
                >
                  Total value:
                </Typography.Text>
                <NumberField
                  value={invoice?.total || 0}
                  options={{ style: "currency", currency: "USD" }}
                />
              </Flex>
            </Flex>
          </Flex>
        </Card>
      </div>
    </Show>
  );
};

```

]

#### Exporting Invoice as PDF

We've created a display page for viewing invoice details, which includes information on accounts, clients, service items, and the total invoice amount. Users can convert the invoice to a PDF by clicking the "Export PDF" button. For this, we utilized the browser's native [`window.print`](https://developer.mozilla.org/en-US/docs/Web/API/Window/print) API to avoid the need for a third-party library, enhancing efficiency and reducing complexity. However, print dialog is printing all the content of the page. To ensure that only the relevant invoice information is printed, we applied `@media print` CSS rules with `display: none` to hide unnecessary page content during the printing process.

After creating the show page, let's create a `src/pages/invoices/index.ts` file to export the pages as follows:

```typescript
[label src/pages/invoices/index.ts]
export { InvoicePageList } from "./list";
export { InvoicesPageCreate } from "./create";
<^>export { InvoicesPageShow } from "./show";<^>
```

Now we are ready to add our "show" page to the `src/App.tsx` file as follows:

[details Show `src/App.tsx` code

```typescript
[label src/App.tsx]
import { Authenticated, Refine } from "@refinedev/core";
import {
  AuthPage,
  ErrorComponent,
  ThemedLayoutV2,
  useNotificationProvider,
} from "@refinedev/antd";
import routerProvider, {
  UnsavedChangesNotifier,
  DocumentTitleHandler,
  NavigateToResource,
  CatchAllNavigate,
} from "@refinedev/react-router-v6";
import { BrowserRouter, Outlet, Route, Routes } from "react-router-dom";
import { DevtoolsPanel, DevtoolsProvider } from "@refinedev/devtools";
import { App as AntdApp } from "antd";
import { dataProvider } from "@/providers/data-provider";
import { authProvider } from "@/providers/auth-provider";
import { ConfigProvider } from "@/providers/config-provider";
import { Logo } from "@/components/logo";
import { Header } from "@/components/header";
import {
  AccountsPageCreate,
  AccountsPageEdit,
  AccountsPageList,
} from "@/pages/accounts";
import {
  ClientsPageCreate,
  ClientsPageEdit,
  ClientsPageList,
} from "@/pages/clients";
<^>import { InvoicePageList, InvoicesPageCreate, InvoicesPageShow } from "@/pages/invoices"<^>
import "@refinedev/antd/dist/reset.css";
import "./styles/custom.css";

const App: React.FC = () => {
  return (
    <DevtoolsProvider>
      <BrowserRouter>
        <ConfigProvider>
          <AntdApp>
            <Refine
              routerProvider={routerProvider}
              authProvider={authProvider}
              dataProvider={dataProvider}
              notificationProvider={useNotificationProvider}
              resources={[
                {
                  name: "accounts",
                  list: "/accounts",
                  create: "/accounts/new",
                  edit: "/accounts/:id/edit",
                },
                {
                  name: "clients",
                  list: "/clients",
                  create: "/clients/new",
                  edit: "/clients/:id/edit",
                },
                {
                  name: "invoices",
                  list: "/invoices",
                  show: "/invoices/:id",
                  create: "/invoices/new",
                },
              ]}
              options={{
                syncWithLocation: true,
                warnWhenUnsavedChanges: true,
                breadcrumb: false,
              }}
            >
              <Routes>
                <Route
                  element={
                    <Authenticated
                      key="authenticated-routes"
                      fallback={<CatchAllNavigate to="/login" />}
                    >
                      <ThemedLayoutV2
                        Header={() => <Header />}
                        Sider={() => null}
                      >
                        <div
                          style={{
                            maxWidth: "1280px",
                            padding: "24px",
                            margin: "0 auto",
                          }}
                        >
                          <Outlet />
                        </div>
                      </ThemedLayoutV2>
                    </Authenticated>
                  }
                >
                  <Route index element={<NavigateToResource />} />

                  <Route
                    path="/accounts"
                    element={
                      <AccountsPageList>
                        <Outlet />
                      </AccountsPageList>
                    }
                  >
                    <Route index element={null} />
                    <Route path="new" element={<AccountsPageCreate />} />
                  </Route>
                  <Route
                    path="/accounts/:id/edit"
                    element={<AccountsPageEdit />}
                  />

                  <Route
                    path="/clients"
                    element={
                      <ClientsPageList>
                        <Outlet />
                      </ClientsPageList>
                    }
                  >
                    <Route index element={null} />
                    <Route path="new" element={<ClientsPageCreate />} />
                  </Route>
                  <Route
                    path="/clients/:id/edit"
                    element={<ClientsPageEdit />}
                  />
                </Route>

                <Route path="/invoices">
                  <Route index element={<InvoicePageList />} />
                  <Route path="new" element={<InvoicesPageCreate />} />
                  <^><Route path=":id" element={<InvoicesPageShow />} /><^>
                </Route>

                <Route
                  element={
                    <Authenticated key="auth-pages" fallback={<Outlet />}>
                      <NavigateToResource />
                    </Authenticated>
                  }
                >
                  <Route
                    path="/login"
                    element={
                      <AuthPage
                        type="login"
                        registerLink={false}
                        forgotPasswordLink={false}
                        title={
                          <Logo
                            titleProps={{ level: 2 }}
                            svgProps={{
                              width: "48px",
                              height: "40px",
                            }}
                          />
                        }
                        formProps={{
                          initialValues: {
                            email: "demo@refine.dev",
                            password: "demodemo",
                          },
                        }}
                      />
                    }
                  />
                </Route>

                <Route
                  element={
                    <Authenticated key="catch-all">
                      <ThemedLayoutV2
                        Header={() => <Header />}
                        Sider={() => null}
                      >
                        <Outlet />
                      </ThemedLayoutV2>
                    </Authenticated>
                  }
                >
                  <Route path="*" element={<ErrorComponent />} />
                </Route>
              </Routes>
              <UnsavedChangesNotifier />
              <DocumentTitleHandler />
            </Refine>
          </AntdApp>
        </ConfigProvider>
        <DevtoolsPanel />
      </BrowserRouter>
    </DevtoolsProvider>
  );
};

export default App;
```

]

After these changes, you should be able to navigate to the invoice show page when you click on an show button from the list page.

![Invoices Show page](https://refine.ams3.cdn.digitaloceanspaces.com/blog/invoicer/invoices-show.png)

When you click the "Export PDF" button, the browser's print dialog will open, allowing you to save the invoice as a PDF.

![Invoices Show page](https://refine.ams3.cdn.digitaloceanspaces.com/blog/invoicer/export-as-pdf.gif)

## Step 6 — Deploying to DigitalOcean App platform

In this step, we'll deploy the application to the DigitalOcean App Platform. To do that, we'll host the source code on GitHub and connect the GitHub repository to the App Platform.

### Pushing the Code to GitHub

Log in to your GitHub account and [create a new repository](https://docs.github.com/en/get-started/quickstart/create-a-repo) named `refine-invoicer`. You can make the repository public or private:

![Create an new repository](https://refine.ams3.cdn.digitaloceanspaces.com/blog/invoicer/create-repo.png)

After creating the repository, navigate to the project directory and run the following command to initialize a new Git repository:

```bash
git init
```

Next, add all the files to the Git repository with this command:

```bash
git add .
```

Then, commit the files with this command:

```bash
git commit -m "Initial commit"
```

Next, add the GitHub repository as a remote repository with this command:

```bash
git remote add origin <your-github-repository-url>
```

Next, specify that you want to push your code to the `main` branch with this command:

```bash
git branch -M main
```

Finally, push the code to the GitHub repository with this command:

```bash
git push -u origin main
```

When prompted, enter your GitHub credentials to push your code.

You'll receive a success message after the code is pushed to the GitHub repository.

In this section, you pushed your project to GitHub so that you can access it using DigitalOcean Apps. The next step is to create a new DigitalOcean App using your project and set up automatic deployment.

### Deploying to DigitalOcean App Platform

In this step, you'll take your React application and set it up on the DigitalOcean App Platform. You'll link your GitHub repository to DigitalOcean, set up the building process, and create your initial project deployment. Once your project is live, any future changes you make will automatically trigger a new build and update.

By the end of this step, you'll have successfully deployed your application on DigitalOcean with continuous delivery capabilities.

Log in to your DigitalOcean account and navigate to the [Apps](https://cloud.digitalocean.com/apps) page. Click the **Create App** button:

![Digital Ocean create a new app](https://refine.ams3.cdn.digitaloceanspaces.com/blog/invoicer/digital-ocean-create-a-new-app.png)

If you haven't connected your GitHub account to DigitalOcean, you'll be prompted to do so. Click the **Connect to GitHub** button. A new window will open, asking you to authorize DigitalOcean to access your GitHub account.

After you authorize DigitalOcean, you'll be redirected back to the DigitalOcean Apps page. The next step is to select your GitHub repository. After you select your repository, you'll be prompted to select a branch to deploy. Select the `main` branch and click the **Next** button.

![DigitalOcean select repository](https://refine.ams3.cdn.digitaloceanspaces.com/blog/invoicer/digital-ocean-select-repository.png)

After that, you'll see the configuration steps for your application. In this tutorial, you can click the **Next** button to skip the configuration steps. However, you can also configure your application as you wish.

Wait for the build to complete. After the build is complete, press **Live App** to access your project in the browser. It will be the same as the project you tested locally, but this will be live on the web with a secure URL. Also, you can follow [this tutorial](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-react-application-to-digitalocean-app-platform) available on DigitalOcean community site to learn how to deploy react based applications to App Platform.

## Conclusion

In this tutorial, we built a Invoicer application using Refine from scratch and got familiar with how to build a fully-functional CRUD app.

Also we'll demonstrate how to deploy your application to the DigitalOcean App Platform.

If you want to learn more about Refine, you can check out the [documentation](https://refine.dev/docs/introduction/overview/) and if you have any questions or feedback, you can join the [Refine Discord Server](https://discord.gg/refine).
