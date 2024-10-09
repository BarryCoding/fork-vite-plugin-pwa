# Troubleshooting and logging

Debugging a service worker is tough. You're dealing with the lifecycle, updates, caches, and the interaction between all of these things. Fortunately, just as Workbox makes service worker development easier, it also makes debugging easier through its informative logging. This page will touch on some of the available debugging tools, and how Workbox's logging works and how it can be configured.

## Available troubleshooting tools

There are loads of tools available in the browser for debugging and troubleshooting while developing a service worker. Here's a few resources to get you started with your browser of choice.

### Chrome and Edge

Chrome (and recent versions of Edge based on the Blink engine) have a robust set of developer tools. Some of those tools—specifically in Chrome's DevTools, but there's more to discover:

- [Debug Progressive Web Apps](https://developer.chrome.com/docs/devtools/progressive-web-apps)
- [Inspect Network Activity](https://developer.chrome.com/docs/devtools/network) in Chrome DevTools
- [Video](https://www.youtube.com/watch?v=tuRPSaSiK_c): Debugging Service Workers in Chrome
- Codelab: [Debugging Service Workers](https://developers.google.com/codelabs/pwa-training/pwa03--going-offline#0)

## Workbox logging

A key developer experience improvement that Workbox offers is in its informative logging. When logging is enabled, Workbox logs nearly all of its activity in a distinctive and functional way.

Development builds of Workbox turn logging on by default, whereas production builds turn it off. There are different steps for switching between the development and production builds, depending on whether you're creating a custom Workbox bundle, or using a pre-bundled copy via `workbox-sw`.

### With or without a bundler

Bundlers are tools that take code from individual modules and create JavaScript output that's ready to run in the browser. When using a bundler, you might also use a bundler-specific Workbox plugin that helps with precaching, like [workbox-webpack-plugin](https://developer.chrome.com/docs/workbox/modules/workbox-webpack-plugin), or you might just be bundling up Workbox runtime caching logic. Either way, Workbox's logging is influenced by setting a production mode in the bundler's configuration:

- In webpack, the `mode` [configuration option](https://webpack.js.org/configuration/mode/) can be set to 'production' or 'development'. workbox-webpack-plugin will use the production or development logging in Workbox based on this value.
- For Rollup, [rollup-plugin-workbox](https://www.npmjs.com/package/rollup-plugin-workbox) accepts a `mode` configuration option that also affects whether Workbox logs anything to the console. If you're using Rollup without the Workbox-specific plugin, you'll need to configure @rollup/plugin-replace to substitute process.env.NODE_ENV with 'development' or 'production'.

Suppose the default logging behavior must be overridden in development. In that case, the appropriate Workbox plugin for your bundler should allow you to hardcode a preference for debugging logs in its configuration. For example, you could **disable logging** in Workbox via workbox-webpack-plugin's mode option for the [GenerateSW method](https://developer.chrome.com/docs/workbox/modules/workbox-webpack-plugin#type-GenerateSW).

### Without a bundler

While bundlers are great, not every project needs them. If you find yourself in a situation where you want to add Workbox to a project that doesn't use a bundler, [workbox-sw](https://developer.chrome.com/docs/workbox/modules/workbox-sw) is the way to go.

pass

### Turn off logging in development builds in any workflow

Whether you use a bundler or not, you can turn off all logging in development builds by assigning `true` to a special `self.__WB_DISABLE_DEV_LOGS` variable into your service worker:

```js
self.__WB_DISABLE_DEV_LOGS = true;

// The rest of your Workbox service worker code goes here
```

One advantage of this approach is that it's completely independent of your bundler configuration, and will work whether you use workbox-sw directly, or depend on a bundler to package up your Workbox-powered service worker for you.