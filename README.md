# ILST >= 24.3 is not returning evalScript callback if any iframe is present

Only code present here is within `./src/App.vue` and completely barebones, only vanilla JS with a bit of Vue mounting cycles.

### Expected behavior:

The button should be invoking evalScript with JSX returning the text "Hello world" to populate the textarea

### What's happening:

The moment an iframe is present, evalScript no longer returns values from JSX to receive in the `callback` arguments, even when evalScript is not being used within the iframe (as it is in this repo).

---

```bash
# Clone and install dependencies
git clone https://github.com/Inventsable/ILST24.3-Iframe-Issue.git
npm i
```

## Test production build

```bash
# Must be run at least once
npm run build
```

For production context (no iframe, no issues) ensure that lines 19 - 20 in `./CSXS/manifest.xml` point to `/dist/` as being entry point:

```xml
<!-- <MainPath>./public/index-dev.html</MainPath> -->
<MainPath>./dist/index.html</MainPath>
```

## Test iframe issue:

```bash
npm run serve
```

Ensure that lines 19 - 20 in `./CSXS/manifest.xml` point to `/public/index-dev.html` as being entry point:

```xml
<MainPath>./public/index-dev.html</MainPath>
<!-- <MainPath>./dist/index.html</MainPath> -->
```

## Important notes:

While this is being invoked from within the iframe for this repo, my target repo with the same behavior is not. It follows the recommended solution of `parent.postMessage` and `window.addEventListener` to send the `evalScript` value as a string, then `evalScript` at the parent (non-iframe), await the result, then `postMessage` back to the child iframe. The issue is the same between both repos however -- if an iframe is present within the document at all, _even if evalScript is not being called from within the iframe_, no return values are being sent from JSX back to CEP. The callback is never invoked regardless. CEP is invoking the scripts (alerts show) but is not obeying any return values to receive as the callback argument.

I've already tried recommended flags (then rebooting the app for the manifest to be read) in all combinations to no effect:

```xml
+/- <Parameter>--disable-site-isolation-trials</Parameter>
+/- <Parameter>--disable-features=SameSiteByDefaultCookies,CookiesWithoutSameSiteMustBeSecure,NetworkService</Parameter>
```
