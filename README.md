# electron-cookies
Provides document.cookie support for Electron. For use with Amplitude and Google Analytics in Electron.

The underlying cookie implementation is Salesforce's [`tough-cookie`](https://github.com/SalesforceEng/tough-cookie) library and Exponent's [WebStorageCookieStore](https://github.com/exponentjs/tough-cookie-web-storage-store) for persistence.

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

## Google Analytics

To use Google Analytics in Electron, we use code like this:

```html
<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','https://www.google-analytics.com/analytics.js','ga');

  (() => {
    'use strict';
    let ElectronCookies = require('@exponent/electron-cookies');
    ElectronCookies.enable({ origin: 'https://example.com' });

    ga('create', GOOGLE_ANALYTICS_ID, 'auto');
    ga('set', 'location', 'https://example.com/');
    ga('set', 'checkProtocolTask', null);
    ga('send', 'pageview');
  })();
</script>
```

Notable changes to the standard code include:
- The analytics script URL is prefixed with `https:` instead of inheriting the page's protocol
- We enable `electron-cookies`
- We set the `location` field in Google Analytics
- The `checkProtocolTask` field is set to null to disable checking for a `file:` protocol
