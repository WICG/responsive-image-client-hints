# Viewport Height Client Hint

## Motivation

Currently, [Responsive Image Client Hints](https://wicg.github.io/responsive-image-client-hints/)
provide a way for origins to obtain the viewport’s width. However, no such attribute exists for
viewport height. We’ve observed that to optimize the loading of content that appears in viewport,
it is essential for the origins to adapt HTML response based on viewport height as well. Current
methods of doing that include content-negotiation through the User-Agent string, but that is suboptimal
as it’s indirect, and doesn’t cover all platforms.

We’d like to propose adding a new image hint `Sec-CH-Viewport-Height` (similar to the existing
[`Sec-CH-Viewport-Width`](https://wicg.github.io/responsive-image-client-hints/#sec-ch-viewport-width)).
Similar to other client hints, origins would be able to register the opt-in for viewport-height client-hint
via _Accept-CH_ header, and receive the attribute value as part of HTTP request headers. This would enable
origins to e.g., inline all the content that’s expected to appear in the viewport, or avoid lazy-loading it.

Even if the above mechanism is an optimization, using Client Hints would enable us to benefit from Client Hints
Reliability and have that information available on the very-first load.

## Usage & Specification

Usage of the `Sec-CH-Viewport-Height` client hint would look like so:

```
Accept-CH: Sec-CH-DPR, Sec-CH-Width, Sec-CH-Viewport-Height
Permissions-Policy: ch-dpr=(self "https://media.shoeshoppe.biz"), ch-width=(self "https://media.shoeshoppe.biz"), ch-viewport-height=(self "https://media.shoeshoppe.biz")
```

The `Sec-CH-Viewport-Height` request header field would give the server information about the user-agent’s
current [viewport](https://www.w3.org/TR/CSS2/visuren.html#viewport) height. Similar to
[`Sec-CH-Viewport-Width`](https://wicg.github.io/responsive-image-client-hints/#sec-ch-viewport-width), it
is a [Structured Header](https://tools.ietf.org/html/draft-ietf-httpbis-header-structure-19) whose value MUST
be an [integer](https://tools.ietf.org/html/draft-ietf-httpbis-header-structure-19#section-3.3.1) greater than
or equal to 0.

If the value is zero, it indicates that there is no [viewport](https://www.w3.org/TR/CSS2/visuren.html#viewport).

For [fetches](https://fetch.spec.whatwg.org/#concept-fetch) within web contexts, its value SHOULD be the
[`Window`](https://html.spec.whatwg.org/multipage/window-object.html#window)'s current
[`innerHeight`](https://drafts.csswg.org/cssom-view-1/#dom-window-innerheight).

## Security and Privacy Considerations

Similar to [other image hints](https://wicg.github.io/responsive-image-client-hints/#secure-transport), viewport
height will not be delivered to non-secure endpoints and  this client hint will also be
[delegated](https://wicg.github.io/responsive-image-client-hints/#delegation) from top-level pages via
[Permissions Policy](https://w3c.github.io/webappsec-permissions-policy/).
