# gsap-video-export

_Expertly and easily export GreenSock (GSAP) animation to video._

`gsap-video-export` is a simple tool for exporting your [GreenSock (GSAP)](https://www.greensock.com) animations to video. Create video animations with the framework you know and love and use them in your video projects or share them on social media with ease.

What makes `gsap-video-export` different from other solutions is rather than simply recording an animation as it plays, it instead steps through exporting frame by frame to ensure the result is seamless.

> **Support this project** <br/> Help support the work that goes into creating and maintaining my projects and sponsor me via [GitHub Sponsors](https://github.com/sponsors/workeffortwaste/).

## Contents

- [gsap-video-export](#gsap-video-export)
  - [Contents](#contents)
  - [What's New](#whats-new)
    - [2.0.0 🆕](#200-)
  - [Getting Started](#getting-started)
    - [Installation](#installation)
    - [Command Line](#command-line)
      - [Options](#options)
    - [ESM Module 🆕](#esm-module-)
      - [Options](#options-1)
  - [Examples](#examples)
    - [Page Export](#page-export)
    - [Custom Timeline](#custom-timeline)
    - [Export Element](#export-element)
    - [Twitter Export](#twitter-export)
    - [Coloured Background](#coloured-background)
    - [Lossless\* Export](#lossless-export)
    - [Timeweb Frame Advancement 🆕](#timeweb-frame-advancement-)
  - [Advanced](#advanced)
    - [Cookies 🆕](#cookies-)
    - [Chrome 🆕](#chrome-)
    - [Headless 🆕](#headless-)
  - [FAQ](#faq)
  - [Sponsors](#sponsors)
    - [Bonus](#bonus)
  - [Author](#author)


## What's New

### 2.0.0 🆕

* ESM module support.
* Puppeteer updated to the latest version, and  `puppeteer-stealth` has been swapped for `rebrowser-puppeteer` for better bot detection avoidance.
* Added support for `timeweb` frame advancement to allow for capturing of elements animated outside of the GSAP timeline.
* Optionally use the system installed version of Chrome for future compatibility.
* Optionally disable headless mode for debugging.
* Supply your own cookie JSON file to bypass cookie pop-ups and authentication.

## Getting Started

### Installation

`gsap-video-export` is a command line tool that can be installed directly via NPM.

```
npm install -g gsap-video-export
```


### Command Line

Once installed the tool can be used as per the following example.

```
gsap-video-export <url>
```

> When using CodePen URLs `gsap-video-export` will automatically redirect to the full page debug preview.

#### Options

```
Options:
      --help            Show help                                                                    [boolean]
      --version         Show version number                                                          [boolean]
  -s, --script          [browser] Custom script                                                       [string]
  -S, --selector        [browser] DOM selector                                  [string] [default: "document"]
  -t, --timeline        [browser] GSAP timeline object                              [string] [default: "gsap"]
  -z, --scale           [browser] Scale factor                                           [number] [default: 1]
  -V, --viewport        [browser] Viewport size                                [string] [default: "1920x1080"]
  -i, --info            [browser] Info only                                         [boolean] [default: false]
      --frame-start     [browser] Start frame
      --frame-end       [browser] End frame
      --chrome          [browser] Use the system installed Chrome                   [boolean] [default: false]
      --cookies         [browser] Cookie JSON  file                                                   [string]
  -r, --advance         [browser] Frame advance method                              [string] [default: "gsap"]
  -p, --color           [video] Auto padding color                                  [string] [default: "auto"]
  -c, --codec           [video] Codec                                            [string] [default: "libx264"]
  -e, --input-options   [video] FFmpeg input options                                                  [string]
  -E, --output-options  [video] FFmpeg output options         [string] [default: ""-pix_fmt yuv420p -crf 18""]
  -o, --output          [video] Filename                                       [string] [default: "video.mp4"]
  -f, --fps             [video] Framerate                                               [number] [default: 60]
  -v, --resolution      [video] Output resolution                                   [string] [default: "auto"]
      --verbose         [tool] Verbose output                                        [boolean] [default: true]
  ```


### ESM Module 🆕

This library can now be imported as an ESM module, opening up `gsap-video-export` to be used seamlessly as part of a production pipeline.

```javascript
import { videoExport } from 'gsap-video-export'

const videoDetails = await videoExport({
  url: <url>,
})

console.log(videoDetails)
/* { file: filename (string), exportTime: seconds (number), renderTime: seconds (number) */
```

When running as an ESM module the output will be silent and any issues willl throw an error. You can check for problems ahead of time with the `info` option and wrapping the function with a try/catch block.

```javascript
import { videoExport } from 'gsap-video-export'

let videoDetails
try {
  videoDetails = await videoExport({
    url: <url>,
    info: true
  })
} catch (err) {
  console.log(err)
}

console.log(videoDetails)
/**
 * {
 *  duration: seconds (number),
 *  frames: frames (number),
 *  gsap: version (string),
 *  timeline: timeline (string)
 * }
 */
```

#### Options

All options available via the Command Line tool are exposed as an option in the ESM module using their long name.

## Examples

> A huge thanks to [@cassiecodes](https://twitter.com/cassiecodes) for letting me use her incredible GreenSock pens to demonstrate `gsap-video-export`.

### Page Export

Supplying `gsap-video-export` with a URL will generate a `1920x1080` video file of the viewport, scrubbing through the GSAP global timeline object at `60fps`.

```bash
# Animation by @cassiecodes
gsap-video-export https://codepen.io/cassie-codes/pen/RwGEewq
```

https://user-images.githubusercontent.com/49479599/154277839-551542f6-9236-48cf-b3e1-b55484475e22.mp4

### Custom Timeline

By default `gsap-video-export` will scrub through the global GSAP timeline object, although there may be instances where you want to specify which timeline you want to record.

In the example below the global timeline fails due an infinite loop.

```bash
# Animation by @SeeMax
gsap-video-export https://codepen.io/SeeMax/pen/bGoxMwj
```

Using the `--timeline` `-t` argument you can specify a different timeline variable to use instead.

```bash
# Animation by @SeeMax
gsap-video-export https://codepen.io/SeeMax/pen/bGoxMwj -t tl
```

https://user-images.githubusercontent.com/49479599/154277884-148c21b5-2d23-48bf-8e2f-5321c64c0c62.mp4

### Export Element

With the `--selector` `-S` argument you can specifiy a DOM selector to capture a specific element. The resulting output video will be the same dimensions as the as the selected element.

`gsap-video-export` also allows you to run custom JavaScript on the page before the video capture begins with the `--script` `-s` argument. In this example a `custom.js` file is supplied with a snippet to remove the corner banner from the DOM.

```js
// custom.js
document.querySelector('img[alt="HTML5"]').remove()
```

```bash
# Animation by GreenSock
gsap-video-export https://codepen.io/GreenSock/pen/DzXpme -S "#featureBox" -s custom.js 
```

https://user-images.githubusercontent.com/49479599/154277903-fd6cfa40-af95-4ef9-83c6-89db2e8a098a.mp4

### Twitter Export

> In this example if you visit the pen you might notice the animation is offscreen. This isn't an issue as `gsap-video-export` will automatically scroll the selected element into the viewport.

It's possible to easily export videos for social media such as Twitter. Using the default settings `gsap-video-export` will automatically output video in a format that conforms to Twitter's video specifications.

To render your video at the desired resolution use the `--resolution` `-v` argument with a `<width>x<height>` string. For Twitter I recommend using `1080x1080`.

```bash
# Video by @cassiecodes
https://codepen.io/cassie-codes/pen/mNWxpL -S svg -v 1080x1080
```

The example above will select the `SVG` element on the page, with the resulting video resized and automatically padded to `1080x1080`.

As the `SVG` element itself is not 1080 pixels in either direction it will ultimately be scaled up to hit the target resolution losing quality.

Using the `--scale` `-z` you can supply a scale factor allowing you to capture the element at a much higher resolution resulting in better video quality.

```bash
# Video by @cassiecodes
https://codepen.io/cassie-codes/pen/mNWxpL -S svg -v 1080x1080 -z 2
```

https://user-images.githubusercontent.com/49479599/154277921-0c5dfb39-9012-43c8-ac76-416a95c9bab0.mp4

### Coloured Background

`gsap-video-export` will automatically detect the background colour to autopad the animation with.

> It uses the first pixel of the first frame to determine colour of the background. You can override this with `--color` `-p` and supply a custom hex value.

```bash
# Video by @cassiecodes
https://codepen.io/cassie-codes/pen/VwZBjRq -S svg -z 2 -v 1080x1080
```

https://user-images.githubusercontent.com/49479599/154277938-1db498b8-661b-4772-ad56-a50964d5c93e.mp4

### Lossless* Export

>*When creating a video with the true lossless setting `-crf 0` it will preserve the colour space of the source PNGs and won't be compatible with some media players. <br><br>For compatibility simply setting the best lossy setting `-crf 1` is enough to create a near lossless video that's compatible with most media players.

The `--output-options` `-E` argument will take a *double quoted string* of FFmpeg output arguments to allow a lot of flexability over the final render.

```bash
# Video by @cassiecodes
gsap-video-export https://codepen.io/cassie-codes/pen/VweQjBw -S svg -z 2 -v 1920x1080 -E '"-pix_fmt yuv420p -crf 1"'
```

https://user-images.githubusercontent.com/49479599/154278049-ae6d585b-9491-45a8-bd2a-ea1f741580e2.mp4

### Timeweb Frame Advancement 🆕

The default frame advancement method for `gsap-video-export` steps through a GSAP timeline to create a silky smooth video.

Unfortunately if there are animations present that are not tied to the GSAP timeline then it may render incorrectly. [Timeweb](https://github.com/tungs/timeweb) is now included as an alternative method for advancing frames which largely resolves this issue.

Using https://nodcoding.com/ as an example, even though the site is built with GSAP there is no exposed timeline so the standard usage of `gsap-video-export` will fail to start.

Let's inject a simple GSAP timeline that scrolls the page to the bottom and use the default `gsap` frame advancement to render the video.

```bash
gsap-video-export http://nodcoding.com/ --script "./scroll.js"
```

The video below has issues with the timing of animations that exist outside of the scroll timeline.

https://github.com/user-attachments/assets/be461336-3605-4a81-af35-de962e5671bc


Using the `timeweb` frame advancement option the native time handling is overwritten allowing us to globally advance the browser frame by frame.

```bash
gsap-video-export http://nodcoding.com/ --script "./scroll.js" --advance timeweb
```

In the output below the scroll timeline and other animated elements are now captured perfectly.

https://github.com/user-attachments/assets/b8a5b4c6-33ab-4e56-8218-b1761ab7b1c0

## Advanced

### Cookies 🆕

If you need to authenticate your session or disable a cookie popup then it's possible to supply your own cookies as a JSON file.

I recommend using this Chrome Extension to export them in a compatible format.
https://chromewebstore.google.com/detail/export-cookie-json-file-f/nmckokihipjgplolmcmjakknndddifde?hl=en

### Chrome 🆕

It's now possible to use the system installed version of Chrome by adding the `--chrome` flag. The library will automatically find the Chrome install location and use that instead of the Chrome for Testing binary that's supplied with Puppeteer.

```bash
gsap-video-export <url> --chrome
```

### Headless 🆕

If you need to see what's happening 'on page' to debug issues you can disable headless mode to inspect the Chrome window.

```bash
gsap-video-export <url> --headless false
```

## FAQ

**Why does my video fail with the duration error `INFINITE`?**

This can happen on some videos where the selected timeline infinitely repeats and GSAP reports a duration in the thousands of hours.

`gsap-video-export` will not attempt to capture any video over an hour and will report the `INFINITE` error.

**How can I disable other on screen elements?**

You can supply a custom .js file with the `--script` argument which runs before the capture begins giving you the ability to manipulate the DOM.

**Why does my video not render as expected?**

`gsap-video-export` works by stepping manually through the specified timeline exporting each individual frame. As a rule of thumb if you can scrub through your timeline manually in GSAP you're not going to have any issues with the default `gsap` frame advance method. 

If you're triggering animations that are not locked to the GSAP timeline, or your page contains other active elements such as video then try the `timeweb` frame advance method, which instead overwrites native time handling in a web page to allow the capture of each frame.

**Why does my timeline fail?**

`gsap-video-export` can access block scoped `let` and `const` variables and variables on the global scope. If your timeline variable is not exposed at that level then `gsap-video-export` will not be able to access it.

Consider moving your timeline to a scope the tool can access.


## Sponsors

If you find this project useful please considering sponsoring me on [GitHub Sponsors](https://github.com/sponsors/workeffortwaste/) and help support the work that goes into creating and maintaining my projects.

### Bonus

Sponsors are able to remove the project support message from all my CLI projects, as well as access other additional perks.

## Author

Chris Johnson - [defaced.dev](https://defaced.dev) - [ꕤdefaced.dev](https://bsky.app/profile/defaced.dev)
            

