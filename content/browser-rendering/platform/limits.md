---
pcx_content_type: configuration
title: Limits
weight: 30
---

# Limits

- Two new browsers per minute per account.
- Two concurrent browsers per account.
- By default, a browser instance gets killed if it does not get any [devtools](https://chromedevtools.github.io/devtools-protocol/) command for 60 seconds, freeing one instance. Users can optionally increase this by using the `keep_alive` [option](../../platform/puppeteer/#keep-alive).
- `browser.close()` releases the browser instance.
