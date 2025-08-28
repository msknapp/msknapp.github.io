---
title: Web
weight: 10
description: 
summary: 
lastmod: 2025-08-13
date: 2025-08-13
tags: []
categories: []
series: []
keywords: []
---

# HTML
- Like XML, elements are enclosed in angle brackets, closing tags have a prefixing “/”.  
  Attributes are in the elements opening block, they are like name=”value”.
- Main format: `<html><head></head><body></body></html>`
- Some tags allow a trailing “/”, but only if they cannot contain a body.  `<br>` has no slash, nor any body, it is called an empty element.  
  `<br>` is a line break.
- `<!DOCTYPE html>` means it is html5
- Head can have a title block, which shows in the browser tab.  Head is not visible on the page.
- Elements: h1 (heading 1), h2, …h6, p (paragraph), a (anchor, for a link). Img.  Elements are not case sensitive, but conventionally people use - lowercase.  pre=preformatted. b=bold, i=italic, sub=subscript, sup=superscript.
- Links: <a href=”url.”>displayed text</a>
- URLs can be absolute, or relative.  An absolute URL has a domain, which is the combination of the protocol, host, and optionally the port.  If a - relative URL starts with a forward slash, then it is relative to the domain.  If a relative URL does not start with a forward slash, then it is - relative to the current page.  If a URL has a hash “#”, the text that follows refers to a section of the page.  Relative URLs are more recommended.
- Some things to keep in mind when creating web pages:
- Robots may scan your page, non-visible attributes may guide them, and the choice of element may have extra meaning to them.  
- SEO: search engine optimization.
- Blind people may use screen readers to review your page.  Assign alt text to images.
- The page may be viewed from tablets or phones, it needs to scale down for them.
- Comments: <!-- text –>
- Most elements can have a style attribute, defined with CSS.  You can also add a style element to the head, or link to a style sheet.  The link type - is an empty element.  Example: <link rel="stylesheet" href="styles.css">
- HTML Entities: &lt; &gt: &amp; &nbsp;
- Table elements: table, tr=table row, th=table header, td=table data (a cell)
- List elements: ul=unordered list, ol=ordered list, li=list item
- Favicon is the icon next to the title in the tab.  You add a link of rel icon in the head.

## CSS

Cascading style sheets, specifies how to style an HTML page.  Elements can have attributes for "id", and "class".  
The ID should be unique in the document.  Elements can have multiple classes, separated by spaces in the attribute value.
IDs and classes must not start with a digit.  Add CSS in the head like
`<link rel="stylesheet" href="foo.css">`.

The CSS Format is:
```
<selector>[, <selector> ...] {
  <property>: <value>; /* statement */
  ...
}

...
```

Selectors:
- "*": all things
- element name: selects all instances of html element type, e.g. "p", "img", "h1", etc.
- "."+class: selects a class
- element "." class: selects elements of the given type with the specified class.
- "#"+id: selects an element with an ID
- you can specify multiple selectors by separating them with commas and spaces.
- parentElement descendentElement: selects all descendentElements that are descendents of parentElements
- parentElement > childElement: selects all childElement that are immediate children of parentElements
- element + nextElement: selects any nextElement that immediately follows elements and has the same parent.
- element ~ nextElement: selects any nextElement that comes after elements and has the same parent.
- selector ":" pseudo-class: selects something only if it is in a specified state, like being hovered, having been clicked, etc.
- selector "::" pseudo-element: selects one part of an alement, like the first line, first letter, etc.

Box Model:
- from outside to middle is: margin, border, padding, content
- you can set these individually, or append "-top", "-right", "-bottom", or "-left" for one side.
- border also supports "-width", "-style", and "-color"
- for margin and padding, you can specify four values, which sets a width for the top, right, bottom, and left.
- For widths, you can specify a length in pixels (px), a percent, auto, or inherit.
- height and width properties: apply to the content of a box, can be px, percent, auto, inherit, initial, etc.
- you can also prefix width and height with "min-" or "max-".
- margins above or below elements may overlap with margins of other elements, they collapse so the margin is the maximum of
  the margins between the two elements, as opposed to their sum.  Margins will not collapse between horizontal elements.

Properties:
- color: of text
- background-color: color of background
- font-family
- font-size
- text-align
- opacity: float from 0 to 1.
- display: block or inline.  block starts on a new line and consumes the full width.  You probably will not need to mess with the defaults.
- float: left, right, none

Layout:
Primarily controlled by the "position" property.  Position can be:
- static: normal (default) layout.  The top, bottom, left, and right properties are not used.
- relative: The top, bottom, left, and right properties control deviations from where static positioning would have put things.
- fixed: stays in a spot relative to the window or viewport.  top, bottom, left, right specify its position relative to the viewport.
- absolute: relative to its nearest positioned ancestor
- sticky: is relative until the page scrolls to a threshold, then becomes fixed.

Center align: use the margin value auto

# Single Page Application (SPA)

- Most or all of the page content is loaded as soon as the user enters the webpage.
- If the user interacts with the page, it does not result in navigating to a different webpage.  
  It may navigate to content, show or hide content, or 
- it may asynchronously load in the content needed, e.g. with AJAX (asynchronous javascript and XML), or RESTful calls.
- The important difference here is that full web pages will not need to be retrieved or loaded while the user interacts with the page.  
  At worst, some javascript is retrieving JSON data from the server and injecting it into the page.
- This gives the user a much more responsive experience.
- Note that being a SPA does not necessarily mean that all of the page content is loaded in a single HTTP request.  
  It does not mean that tons of information is read and non-visible.

# JSON Path

- Far more limited than jq expressions, but is often supported in other command lines or in APIs as a query parameter.
- “$” represents the root of an object.  In kubectl, this is optional because it’s implied.
- Select sub-items with “.” then their key, or .[“key”]
- “@” refers to the current object
- “*” is the wildcard, it selects everything,
- “..” is recursive descent.
- Select slices of arrays with “[start:stop:step]”, stop is exclusive.
- “[... , …]” is the union operator, it selects multiple sub items of the object.
- You can easily filter arrays with “[?(expression)]”, and the expression often uses “@” to refer to the current object, 
  then it tends to compare a field it has with expectations.  You can use normal comparisons.
- Different implementations may extend these expressions.

# REST

- Request types
  - GET: no body
  - POST: has body, creating content.
  - PUT: updates data, usually delivers the full record
  - PATCH: updates data, delivers just the changed part of the record.
  - DELETE: no body, deletes a record.
  - HEAD: tells you what headers would be responded with.  Often not supported.
  - OPTIONS: Indicates what other methods are supported.
- Response Status Codes:
  - 200: OK
  - 201: Created
  - 202: Accepted: like if it is being asynchronously processed.
  - 204: No Content.  Headers may be useful.
  - 206: partial content: when a range header was given and doesn’t cover the whole possible range.
  - 301: Moved Permanently.  Location response header says where.
  - 304: not modified: indicates the client can use its cached value.
  - 307: temporary redirect
  - 308: Permanent redirect, same as 301, but request method must not change.
  - 400: Bad request
  - 401: Not authorized, actually not authenticated, user must identify themself.
  - 403: Forbidden: user is identified but does not have permission.
  - 404: not found
  - 405: method not allowed: most common when a wrong endpoint was used, or you just removed a supported method from an endpoint.
  - 409: conflict with current state of server.
  - 429: Too many requests, being throttled
  - 500: internal server error
  - 503: No healthy upstream, server unavailable

# URLs

- Protocol: http, https, s3, gs, etc.  Usually https
- Host: address of the server, used by DNS to find it.
- Port: number of service on the host.
- Path: selects a resource in the service.
- Query parameters: configures options for most requests.

# Headers

- Content-Type: used in post requests, specifies what the body is
- Accept: specifies what format the response body should be in.
- Cookies
- Middleware: logic that is run before and/or after the server handles each request. 
- Endpoint: one path on the service, and the logic to handle it.

# Web Frameworks

- Chi: Golang web server.
- Django: Heavy-weight Python web server, for more complex tasks.  Supports layouts for a view.  
  More like an MVC framework, or MVT, model view template.  Has some ORM support for the database layer.  Uses jinja2 for templating.
- Flask: light-weight Python web server framework, faster.  No built-in view support.

# JSON Path

- Is a simple way of selecting parts of a JSON object. 
- Similar to jq expressions but simpler.
- Kubectl supports it.

# JSON Patching

- Is a json document describing how to change another json object.
- Is a list of change operations.
- Change operations have: op, path, value.
- Op can be: add, replace, remove, move, copy, test.
- Path is like a directory, delimited with forward slashes, starting with a forward slash.
- Refer to items in an array using their index, between slashes, don’t use square brackets for indexing.  
  Like “myobject/mylist/1/name”.  It’s zero based.  Use “-” to refer to the last item in an array.
- Move and copy operations take a “from” field and not a value.
- Test operation is like an assertion, later changes won’t run if the test fails.
- Remove does not take a value.
- When using “add” with an array, the value is inserted before the index, or at the end of the array if “-” is given as the index.

# Web APIs

- GRPC: Uses protobuf over HTTP
- REST: JSON documents exchanged.  Can also be XML.
Gateway - forwards requests to other microservices.  Envoy, Apache, NGinx.
API Gateway
Service Mesh - controls traffic between containers.
Can manage security, TLS, throttling, service discovery, routing, etc.
Istio, 
LinkerD
GraphQL
DNS - domain name service
Proxy

WSGI: web server gateway interface, specific to Python, so web servers can call on python web applications.

Web Assembly


MVC: model, view, control.  A pattern of splitting up aspects of web design.

# Trailing Slashes In URLs

If a URL route is registered with a trailing slash, you can access it with or without the trailing slash.  If
it is registered without a trailing slash, then you cannot access it if you add a trailing slash.  It's better
to register without a trailing slash, so that search engines don't index the page twice.