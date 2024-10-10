# Faster multipage applications with streams

Pending Status

These days, websites or web apps if you prefer, tend to use one of two navigation schemes:

- The navigation scheme browsers provide by default that is, you enter a URL in your browser's address bar and a [navigation request](https://web.dev/articles/handling-navigation-requests#:%7E:text=Navigation%20requests%20are%20requests%20for,you%20to%20a%20new%20URL.) returns a document as a response. Then you click on a link, which unloads the current document for another one, ad infinitum.
- The single page application pattern, which involves an initial navigation request to load the application shell and relies on JavaScript to populate the application shell with client-rendered markup with content from a back-end API for each "navigation".

The benefits of each approach have been touted by their proponents:

- The navigation scheme that browsers provide by default is resilient, as routes don't require JavaScript to be accessible. Client-rendering of markup by way of JavaScript can also be a potentially expensive process, meaning that lower-end devices may end up in a situation where content is delayed because the device is blocked processing scripts that provide content.
- On the other hand, Single Page Applications (SPAs) may provide faster navigations after the initial load. Rather than relying on the browser to unload a document for an entirely brand new one (and repeating this for every navigation) they can offer what feels like a faster, more "app-like" experience—even if that requires JavaScript to function.

In this post, we're going to talk about a third method that strikes a balance between the two approaches described above: relying on a service worker to precache the common elements of a website—such as header and footer markup—and using streams to provide an HTML response to the client as fast as possible, all while still using the browser's default navigation scheme.

