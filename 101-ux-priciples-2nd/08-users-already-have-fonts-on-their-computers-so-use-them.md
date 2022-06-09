### Users already have fonts on their computers, so use them

First of all, these fonts have to be loaded from somewhere, whether it’s Google Fonts, Typekit, or your own **content delivery network** (**CDN**). This means that there is an overhead in getting the font files down to the user’s machine. Content-heavy pages will often break while the correct fonts are downloaded and rendered—the dreaded **Flash of unstyled content** or **Flash of unstyled text**.

Secondly, if, by specifying wild and wonderful body copy typefaces, you think you’re exerting some control over the end result, then think again: responsive design, assistive technologies for disabled people, and thousands of different devices out in the wild mean your pages will look different for everyone.

In most cases, using system-native fonts makes pages appear more quickly, and the type looks sharper and more readable.

The CSS code below tells the browser to display the text in one of three com- monly used styles, only using the built-in font files on the device.

- Sans-serif typefaces like Helvetica or Futura—most modern sites and applications use sans-serif typefaces.

```
font-family: -apple-system, BlinkMacSystemFont,
avenir next, avenir, segoe ui, helvetica neue,
helvetica, Ubuntu, roboto, noto, arial, sans-serif;
```

- Serif typefaces—the more “traditional” looking typeface with little “hooks” (called serifs) hanging off each letter.

```
font-family: Iowan Old Style, Apple Garamond,
Baskerville, Times New Roman, Droid Serif, Times,
Source Serif Pro, serif, Apple Color Emoji, Segoe
UI Emoji, Segoe UI Symbol;
```

- Monospace typefaces—so called because the spacing between each letter is the same, designed to increase readability for code examples.

```
font-family: Menlo, Consolas, Monaco, Liberation
Mono, Lucida Console, monospace;
```

Please, just use the system font stack.