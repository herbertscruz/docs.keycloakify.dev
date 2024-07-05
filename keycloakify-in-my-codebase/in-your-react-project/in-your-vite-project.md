# In your Vite Project

If you have a Vite/React/TypeScript project you can integrate Keycloakify directly inside it. &#x20;

&#x20;In this guide we're going to work with a vanilla Vite project.

<figure><img src="../../.gitbook/assets/image (56).png" alt="" width="375"><figcaption><p>Creating a new vite project with yarn create vite. You don't need to create a new project. Just use your existing codebase.</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (57).png" alt="" width="368"><figcaption><p>Our codebase before installing Keycloakify</p></figcaption></figure>

{% hint style="info" %}
Before anything make sure to commit all your pending changes so you can easily revert changes if need be.
{% endhint %}

Let's start by installing Keycloakify (and optionally Storybook) to our project:

{% tabs %}
{% tab title="yarn" %}
```bash
yarn add keycloakify@next
yarn add --dev storybook @storybook/react @storybook/react-vite
```
{% endtab %}

{% tab title="pnpm" %}
```bash
pnpm add keycloakify@next 
pnpm add --dev storybook @storybook/react @storybook/react-vite
```
{% endtab %}

{% tab title="bun" %}
```bash
bun add keycloakify@next
bun add --dev storybook @storybook/react @storybook/react-vite
```
{% endtab %}

{% tab title="npm" %}
```bash
npm install --save keycloakify@next
npm install --save-dev storybook @storybook/react @storybook/react-vite
```
{% endtab %}
{% endtabs %}

Next we want to repatriate the relevent files from [the starter template](https://github.com/keycloakify/keycloakify-starter) into our project:

```bash
cd my-react-app
git clone https://github.com/keycloakify/keycloakify-starter tmp
mv tmp/src src/keycloak-theme
mv tmp/.storybook .
rm -r tmp
rm src/keycloak-theme/vite-env.d.ts
mv src/keycloak-theme/main.tsx src/main.tsx
```

<figure><img src="../../.gitbook/assets/image (58).png" alt="" width="370"><figcaption><p>State of your codebase after bringin in the Keycloakify boilerplate code.<br>Note thate the keycloak-theme (or keycloak_theme) directory can be located anywhere under your src directory.</p></figcaption></figure>

Now you want to modify your entry point so that: &#x20;

* If the kcContext global is defined, render your Keycloakify theme
* Else, reder your App as usual. &#x20;

<pre class="language-tsx" data-title="src/main.tsx"><code class="lang-tsx">/* eslint-disable react-refresh/only-export-components */
import { createRoot } from "react-dom/client";
import { StrictMode, lazy, Suspense } from "react";

// The following block can be uncommented to test a specific page with `yarn dev`
// Don't forget to comment back or your bundle size will increase
/*
<strong>import { getKcContextMock } from "./keycloak-theme/login/KcPageStory";
</strong>
if (import.meta.env.DEV) {
    window.kcContext = getKcContextMock({
        pageId: "register.ftl",
        overrides: {}
    });
}
*/

const KcLoginThemePage = lazy(() => import("./keycloak-theme/login/KcPage"));
const KcAccountThemePage = lazy(() => import("./keycloak-theme/account/KcPage"));
<strong>const App = lazy(() => import("./App"));
</strong>
createRoot(document.getElementById("root")!).render(
    &#x3C;StrictMode>
        &#x3C;Suspense>
            {(() => {
                switch (window.kcContext?.themeType) {
                    case "login":
                        return &#x3C;KcLoginThemePage kcContext={window.kcContext} />;
                    case "account":
                        return &#x3C;KcAccountThemePage kcContext={window.kcContext} />;
                }
<strong>                return &#x3C;App />;
</strong>            })()}
        &#x3C;/Suspense>
    &#x3C;/StrictMode>
);

declare global {
    interface Window {
        kcContext?:
<strong>            | import("./keycloak-theme/login/KcContext").KcContext
</strong><strong>            | import("./keycloak-theme/account/KcContext").KcContext;
</strong>    }
}
</code></pre>

You also need to use Keycloakify's Vite plugin. Here we don't provide any [build options](../../build-options/) but you probably at least want to define [keycloakVersionTargets](../../build-options/keycloakversiontargets.md).

<pre class="language-tsx" data-title="vite.config.ts"><code class="lang-tsx">import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
<strong>import { keycloakify } from "keycloakify/vite-plugin";
</strong>
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    react(), 
<strong>    keycloakify({})
</strong>  ],
})
</code></pre>

Finally you want to add to your package.json a script for building the theme and another one to start storybook.

<pre class="language-json" data-title="package.json"><code class="lang-json">{
  "name": "my-react-app",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc -b &#x26;&#x26; vite build",
    "lint": "eslint . --ext ts,tsx --report-unused-disable-directives --max-warnings 0",
    "preview": "vite preview",
<strong>    "build-keycloak-theme": "npm run build &#x26;&#x26; keycloakify build",
</strong><strong>    "storybook": "storybook dev -p 6006"
</strong>  },
  // ...
</code></pre>

That's it, your project is ready to go! &#x20;

You can run npm run build-keycloak-theme, the JAR distribution of your Keycloak theme will be generated in dist\_keycloak.

You're now able to use all the Keycloakify commands (`npx keycloakify --help`) from the root of your project.

{% hint style="success" %}
If you're currently using [keycloak-js](https://www.npmjs.com/package/keycloak-js) or [react-oidc-context](https://github.com/authts/react-oidc-context) to manage user authentication in your app you might want to checkout [oidc-spa](https://www.oidc-spa.dev/), the alternative from the Keycloakify team.

If you have any issues [reach out on Discord](https://discord.gg/mJdYJSdcm4)! We're here to help!
{% endhint %}

{% content-ref url="../../testing-your-theme/" %}
[testing-your-theme](../../testing-your-theme/)
{% endcontent-ref %}

{% content-ref url="../../customization-strategies/" %}
[customization-strategies](../../customization-strategies/)
{% endcontent-ref %}