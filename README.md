# Power Apps Code (PAC) App – with React, TypeScript & Vite

This template provides a minimal setup to get React working in Vite with HMR and some ESLint rules – all wrapped in a Power App (Code App)!

> [!NOTE]
> See project information on Github <https://github.com/microsoft/PowerAppsCodeApps>.

## Additional information on React application

The React Compiler is not enabled on this template because of its impact on dev & build performances. To add it, see [this documentation](https://react.dev/learn/react-compiler/installation).

### Expanding the ESLint configuration (not mandatory)

If you are developing a production application, we recommend updating the configuration to enable type-aware lint rules:

```js
export default defineConfig([
  globalIgnores(['dist']),
  {
    files: ['**/*.{ts,tsx}'],
    extends: [
      // Other configs...

      // Remove tseslint.configs.recommended and replace with this
      tseslint.configs.recommendedTypeChecked,
      // Alternatively, use this for stricter rules
      tseslint.configs.strictTypeChecked,
      // Optionally, add this for stylistic rules
      tseslint.configs.stylisticTypeChecked,

      // Other configs...
    ],
    languageOptions: {
      parserOptions: {
        project: ['./tsconfig.node.json', './tsconfig.app.json'],
        tsconfigRootDir: import.meta.dirname,
      },
      // other options...
    },
  },
])
```

You can also install [eslint-plugin-react-x](https://github.com/Rel1cx/eslint-react/tree/main/packages/plugins/eslint-plugin-react-x) and [eslint-plugin-react-dom](https://github.com/Rel1cx/eslint-react/tree/main/packages/plugins/eslint-plugin-react-dom) for React-specific lint rules:

```js
// eslint.config.js
import reactX from 'eslint-plugin-react-x'
import reactDom from 'eslint-plugin-react-dom'

export default defineConfig([
  globalIgnores(['dist']),
  {
    files: ['**/*.{ts,tsx}'],
    extends: [
      // Other configs...
      // Enable lint rules for React
      reactX.configs['recommended-typescript'],
      // Enable lint rules for React DOM
      reactDom.configs.recommended,
    ],
    languageOptions: {
      parserOptions: {
        project: ['./tsconfig.node.json', './tsconfig.app.json'],
        tsconfigRootDir: import.meta.dirname,
      },
      // other options...
    },
  },
])
```

## Four steps to get started

Follow this minimal path to awesome to create an app from scratch – by using a real code first approach 💪! <br>
See installation guidelines on <https://learn.microsoft.com/en-us/power-apps/developer/code-apps/> for further information.

### ⓵ Power Platform environment with «code apps» enabled

Code Apps must be enabled in all dedicated Power Platform environments. This can be done via an environment setting `Product` > `Features` > Power Apps Code Apps

![Enable Power Apps Code Apps](https://learn.microsoft.com/en-us/power-apps/developer/code-apps/media/enable-code-apps.png)

> [!IMPORTANT]
> End-users that run code apps need a Power Apps Premium license.

### ⓶ Install dev tools locally (local device)

Make sure you have your local machine ready with this tooling:

- **Visual Studio Code**: 👉 https://code.visualstudio.com/
- **NodeJS server (latest)**: 👉 https://nodejs.org/en
- **Power Platform Tools for VS Code (including PPL CLI)***: 👉 https://learn.microsoft.com/en-us/power-platform/developer/cli/introduction

### ⓷ Initalize a new React app (with Vite) and a Code app

#### React app with Vite

- Create a new React app with vite and install the node type definitions:
  ```bash
  npm create vite@latest "PAC Sample" -- --template react-ts
  npm i --save-dev @types/node
  ```

- As the Power SDK requires the port `3000` in the default configuration, make sure to adjust `vite.config.ts`:

  ```typescript
  import { defineConfig } from 'vite'
  import react from '@vitejs/plugin-react'
  import * as path from 'path'

  // https://vite.dev/config/
  export default defineConfig({
    base: "./",
    server: {
      host: "::",
      port: 3000,
    },
    plugins: [react()],
    resolve: {
      alias: {
        "@": path.resolve(__dirname, "./src"),
      },
    },
  });
  ```

- Try to run the boilerplate code (make sure you have all dependent packages / libraries installed locally by invoking `npm install`) to test if vite is running on port `3000`:

  ```bash
  npm run dev
  ```

  ![Run app on localhost:3000](https://learn.microsoft.com/en-us/power-apps/developer/code-apps/how-to/media/sql-localhost.png)

#### Power Apps Code (PAC) app

- Initialize your project to «transform» it into a PAC app by using the `pac code init` command from the PAC CLI:

  > [!NOTE]
  > Notice that after this action, there's a new file in your project: `power.config.json`.

  ```bash
  pac code init --displayName "PAC Sample"
  ```

- Test the app if the «Power Platform binding» is setup properly.

  > [!NOTE]
  > It will not work yet – it will cause a Timeout! (because we need to bring the "binding" in the app by installing the Power Platform *Power SDK* in the next step)

  ```bash
  pac code run
  ```

  > [!TIP]
  > Kill the running process if port `8080` is already bound with this command: `sudo lsof -ti:8080 | xargs kill -9`

### ⓸ Adjust the app, run and it 🚀

#### Adjust the app

- Install the *Power SDK*. The *Power SDK* brings all the Power Platform capabilities to your code in order to make the Power Platform ressources accessible (most of all necessary for data connections or data connectors):

  ```bash
  npm install --save-dev "@microsoft/power-apps"
  ```

- Create the `PowerProvider.tsx` component. Add a new React component within the `src` folder named `PowerProvider.tsx`. <br>
  Grab the code from  <https://github.com/microsoft/PowerAppsCodeApps/blob/main/docs/assets/PowerProvider.tsx>.<br>
  This step will bring the Power from the Power Platform into our React (or Vite) app! 💪

- Import and add the `PowerProvider` component to the app. This step is very crucial: it's about making the necessary Power Platform assets – mainly the connectors accessible within the code.<br>
  Wrap the `App` component in `main.tsx` with the `PowerProvider`:

  ```typescript
  <PowerProvider>
    <App />
  </PowerProvider>
  ```

- Adjust node `dev` scripts in `package.json` to run app (in Vite) as well as the pac code app service locally:
  ```json
  "scripts": {
    "dev": "pac code run & vite",
    //...
  },
  ```

  > [!NOTE]
  > If you're developing on Windows, you have to add `start` before the `vite` and `pac` commands.

#### Run (and test it)

- Run the app locally by invoking

  ```bash
  npm run dev
  ```

  This will spin up the app in Vite on port 3000 (by executing `vite`) and publishes it to the Power Platform environment (by executing `pac code run`).

- Open the Power Platform resource URL in the same browser profile as your configured Power Platform tenant (environment).<br>
  You should see the app open similar to:
  ![Vite app running in the Power Platform](https://learn.microsoft.com/en-us/power-apps/developer/code-apps/how-to/media/sql-vite-running-powerapps.png)

## Deploy

To deploy your app to be executed in the Power Platform, simply build the app and then publish it via PAC CLI:

```bash
npm run build | pac code push
```

If successful, this command should return a Power Apps URL to run the app.

## Troubleshooting

If you get stuck on the «fetching your app» loading screen or see an «App timed out» error screen, double check:

- That you ran `npm run build` (when the app was deployed) or `npm run dev` (when running the app locally in dev mode)
- There are no issues in `PowerProvider.tsx`
