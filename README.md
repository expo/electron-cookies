# electron-cookies
Provides document.cookie support for Electron

## Installation

```sh
npm install @exponent/electron-cookies --save
```

## Usage

```js
import ElectronCookies from '@exponent/electron-cookies';

// Add support for document.cookie, using the given origin (protocol, host, and
// port)
ElectronCookies.enable({
  origin: 'https://example.com',
});

// Remove support for document.cookie. Cookies are not cleared from the
// underlying storage.
ElectronCookies.disable();
```

## Behavior

When getting or setting a cookie, the browser needs to know the current URL of the page.

In Electron, HTML files are usually served from the local filesystem, which has no domain. electron-cookies provides you a way to specify the origin of the URL to use:

```js
ElectronCookies.enable({ origin: 'https://example.com' });
```

This tells electron-cookies the origin of the URL to use when accessing cookies. The path of the URL is the relative path from the app's root to the path of the HTML file. So if the app is at `/Users/you/Desktop/` and the HTML file is at `/Users/you/Desktop/web/index.html`, the synthesized URL will be `https://example.com/web/index.html`.

Alternatively, if you omit an origin, the full `file:` URL of the HTML file is used instead.
