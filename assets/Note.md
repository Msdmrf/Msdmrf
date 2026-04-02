## Quick Heads Up:
* In my [Github Intro](https://github.com/Msdmrf) page, all the animations and icons work correctly.
* If you pressed the image you would be taken to the [Blob View](https://github.com/Msdmrf/Msdmrf/blob/main/assets/LinkedIn_Banner_Animated.svg) page, where the animations and icons would still work correctly.
* If you wanted to have a better look and clicked "Open Image in New Tab" instead, you would be taken to the [Raw View](https://raw.githubusercontent.com/Msdmrf/Msdmrf/main/assets/LinkedIn_Banner_Animated.svg) page, where a few icons and animations wouldn't work.
* But if you used the "Download Raw File" button in the [Blob View](https://github.com/Msdmrf/Msdmrf/blob/main/assets/LinkedIn_Banner_Animated.svg) page, you would be able to see everything correctly.

## Why does this happen?
This is a known quirk of how GitHub serves raw files! The issue comes down to content security headers.<br>
When GitHub serves the SVG from  ```raw.githubusercontent.com```, it sends it with ```Content-Type: text/plain``` (or sometimes ```image/svg+xml``` but with a restrictive ```Content-Security-Policy header```). This is a deliberate security measure to prevent SVGs from executing malicious scripts in the context of a GitHub domain.<br>
For example, the embedded PNG icons are all base64-encoded ```<image>``` elements with ```xlink:href="data:image/png;base64,..."``` attributes. When ```raw.githubusercontent.com``` serves the SVG with its restrictive CSP, the browser blocks those inline data URIs from loading as a security precaution — it treats them similarly to how it would treat embedded content that could be an attack vector.<br>
That's why you would see the glowing circles (pure SVG shapes — no external/embedded resources) and the SMIL animations (also pure SVG) working fine, but the raster images vanish (the Full Stack and volleyball icons).

## Here's the breakdown of each context:
* GitHub README rendering — GitHub's Markdown renderer proxies and sanitizes the SVG through ```camo.githubusercontent.com```, which actually re-serves it in a way that allows the embedded images to render.
* [Blob View](https://github.com/Msdmrf/Msdmrf/blob/main/assets/LinkedIn_Banner_Animated.svg) — GitHub renders the SVG inside its own page using an ```<iframe>``` with more permissive settings, so everything works.
* [Raw View](https://raw.githubusercontent.com/Msdmrf/Msdmrf/main/assets/LinkedIn_Banner_Animated.svg) — The browser gets the SVG directly with restrictive headers, and blocks the ```data:``` URI images.
* Downloaded file — No server headers involved at all; the browser just opens a local file, so everything renders perfectly.

## Conclusion
In short, it's not a bug in my SVG — it's GitHub's security policy intentionally restricting embedded data URIs when SVGs are served raw. There's no real fix for this since it's server-side behavior, but it doesn't affect the actual use case (my [Github Intro](https://github.com/Msdmrf) page) where everything looks great.

### Thank you for your curiosity!