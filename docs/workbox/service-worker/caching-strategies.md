# Strategies for service worker caching

Until now, there have only been mentions and tiny code snippets of the [`Cache` interface](https://developer.mozilla.org/en-US/docs/Web/API/Cache). To use service workers effectively, it's necessary to adopt one or more caching strategies, which requires a bit of familiarity with the `Cache` interface.

A caching strategy is an interaction between a service worker's `fetch` event and the `Cache` interface. How a caching strategy is written depends; for example, it may be preferable to handle requests for static assets differently than documents, and this affects how a caching strategy is composed.

Before we get into the strategies themselves, let's take a second to talk about what the `Cache` interface isn't, what it is, and a quick rundown of some of the methods it offers to manage service worker caches.

## The Cache interface versus the HTTP cache

If you haven't worked with the `Cache` interface before, it might be tempting to think of it as the same as, or at least related to the HTTP cache. This is not the case.

- The `Cache` interface is a caching mechanism entirely separate from the HTTP cache.
- Whatever [Cache-Control](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control) configuration you use to influence the HTTP cache has no influence on what assets get stored in the `Cache` interface.

It helps to think of browser caches as layered. The HTTP cache is a low-level cache driven by key-value pairs with directives expressed in HTTP headers.

By contrast, the `Cache` interface is a high-level cache driven by a JavaScript API. This offers more flexibility than when using relatively simplistic HTTP key-value pairs, and is **one half** of what makes caching strategies possible. Some important API methods around service worker caches are:

- [CacheStorage.open](https://developer.mozilla.org/en-US/docs/Web/API/CacheStorage/open) to create a new Cache instance.
- [Cache.add](https://developer.mozilla.org/en-US/docs/Web/API/Cache/add) and [Cache.put](https://developer.mozilla.org/en-US/docs/Web/API/Cache/put) to store network responses in a service worker cache.
- [Cache.match](https://developer.mozilla.org/en-US/docs/Web/API/Cache/match) to locate a cached response in a Cache instance.
- [Cache.delete](https://developer.mozilla.org/en-US/docs/Web/API/Cache/delete) to remove a cached response from a Cache instance.

These are just a few. There are other useful methods, but these are the basic ones you'll see used later on in this guide.

## The humble fetch event

**The other half** of a caching strategy is the service worker's [`fetch` event](https://developer.mozilla.org/en-US/docs/Web/API/FetchEvent). So far in this documentation, you've heard a bit about "intercepting network requests", and the `fetch` event inside of a service worker is where this happens:

```js
// Establish a cache name
const cacheName = 'MyFancyCacheName_v1';

self.addEventListener('install', (event) => {
  event.waitUntil(caches.open(cacheName));
});

self.addEventListener('fetch', async (event) => {
  // Is this a request for an image?
  if (event.request.destination === 'image') {
    // Open the cache
    event.respondWith(caches.open(cacheName).then((cache) => {
      // Respond with the image from the cache or from the network
      return cache.match(event.request).then((cachedResponse) => {
        return cachedResponse || fetch(event.request.url).then((fetchedResponse) => {
          // Add the network response to the cache for future visits.
          // Note: we need to make a copy of the response to save it in
          // the cache and use the original as the request response.
          cache.put(event.request, fetchedResponse.clone());

          // Return the network response
          return fetchedResponse;
        });
      });
    }));
  } else {
    return;
  }
});
```

This is a [toy example](https://service-worker-fetch-event-example.glitch.me/)—and one you can see in action for yourself—but it's one that offers a glimpse into what service workers can do. The above code does the following:

1. Inspect the request's `destination` property to see if this is an image request.
2. If the image is in the service worker cache, serve it from there. If not, fetch the image from the network, store the response in the cache, and return the network response.
3. All other requests are passed through the service worker with no interaction with the cache.

A fetch's `event` object contains a [request](https://developer.mozilla.org/en-US/docs/Web/API/FetchEvent/request) property which some useful bits of information to help you identify the type of each request:

- [url](https://developer.mozilla.org/en-US/docs/Web/API/Request/url), which is the URL for the network request currently being handled by the `fetch` event.
- [method](https://developer.mozilla.org/en-US/docs/Web/API/Request/method), which is the request method (e.g., `GET` or `POST`).
- [mode](https://developer.mozilla.org/en-US/docs/Web/API/Request/mode), which describes the request's mode. A value of `'navigate'` is often used to distinguish requests for HTML documents from other requests.
- [destination](https://developer.mozilla.org/en-US/docs/Web/API/Request/destination), which describes the type of content being requested in a way that avoids using the requested asset's file extension.

Once again, asynchrony is the name of the game. You'll recall that the `install` event offers an [`event.waitUntil`](https://developer.mozilla.org/en-US/docs/Web/API/ExtendableEvent/waitUntil) method that takes a promise, and waits for it to resolve before continuing on to activation. The `fetch` event offers a similar [`event.respondWith`](https://developer.mozilla.org/en-US/docs/Web/API/FetchEvent/respondWith) method that you can use to return the result of an asynchronous [fetch request](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) or a response returned by the Cache interface's match method.

## Caching strategies

### Cache only

### Network only

### Cache first, falling back to network

### Network first, falling back to cache

### Stale-while-revalidate