# workbox-core

Workbox has been built to be modular, allowing developers to select the pieces they want to use without forcing them to download everything in a single file.

There is however overlap between modules, for example, each module will like need to interact with the console, throw meaningful errors and make use of the network or cache. To avoid each module implementing the same logic, `workbox-core` contains this common code which each module relies on.

This module does provide some functionality to developers, but beyond log levels and caching, `workbox-core` offers internal logic to each module, rather than the end developer.


---

<!--  -->
<!--  -->

## Types

- [types.ts](https://github.com/GoogleChrome/workbox/blob/v7/packages/workbox-core/src/types.ts)


### RouteHandler

```ts
/**
 * Either a `RouteHandlerCallback` or a `RouteHandlerObject`.
 * Most APIs in `workbox-routing` that accept route handlers take either.
 */
export type RouteHandler = RouteHandlerCallback | RouteHandlerObject

/**
 * The "handler" callback is invoked whenever a `Router` matches a URL/Request
 * to a `Route` via its `RouteMatchCallback`. This handler callback should
 * return a `Promise` that resolves with a `Response`.
 *
 * If a non-empty array or object is returned by the `RouteMatchCallback` it
 * will be passed in as this handler's `options.params` argument.
 */
export interface RouteHandlerCallback {
  (options: RouteHandlerCallbackOptions): Promise<Response>
}

export declare interface RouteHandlerCallbackOptions {
  event: ExtendableEvent
  request: Request
  url: URL
  params?: string[] | MapLikeObject
}

export interface RouteHandlerObject {
  handle: RouteHandlerCallback
}
```

### RouteMatchCallback

```ts
/**
 * Options passed to a `RouteMatchCallback` function.
 */
export interface RouteMatchCallbackOptions {
  event: ExtendableEvent
  request: Request
  sameOrigin: boolean
  url: URL
}

/**
 * The "match" callback is used to determine if a `Route` should apply for a
 * particular URL and request. When matching occurs in response to a fetch
 * event from the client, the `event` object is also supplied. However, since
 * the match callback can be invoked outside of a fetch event, matching logic
 * should not assume the `event` object will always be available.
 * If the match callback returns a truthy value, the matching route's
 * `RouteHandlerCallback` will be invoked immediately. If the value returned
 * is a non-empty array or object, that value will be set on the handler's
 * `options.params` argument.
 */
export interface RouteMatchCallback {
  (options: RouteMatchCallbackOptions): any
}
```
