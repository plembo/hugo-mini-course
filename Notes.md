# Notes for Mini Hugo Course
Hoping this will help me understand Hugo better, especially some of the more advanced features introduced since Mike Dane's course was released in 2017.
## Markdown Introduction

A program:

```bash
#!/bin/bash
echo "Hello, world!"
```

Here's a table:

 Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 |


## Templating
Everything worked as described.
## CSS Styling
Worked as described.
## Optimizing
### Images
This section is a mess, to the point of being incoherent.

It involves leveraging the [image processing](https://gohugo.io/content-management/image-processing/) with [Hugo Pipes](https://gohugo.io/hugo-pipes/introduction/), although how these work together is never explained. The poor quality of the cited documentation may be partly to blame.

Basically, you're supposed to create a folder named "assets" at the root of your project and put your images under there rather than under "static". Then you invoke Pipes through some magical code that isn't explained, and trigger its use in an unidentified file.

This is a better tutorial:

Eric Murphy. "How to Automatically Optimzize Images in Hugo". _YouTube_, 27 October 2021, https://youtu.be/6qy4Aht1xsE.

Still, Eric is using HTML for his pages: not markdown. To get an better understanding of how things work I found Alex Lakatos. "How to Add Image Processing to Your Hugo Website and Improve Performance". _AlexLakatos_, 17 July 2020, https://alexlakatos.com/web/2020/07/17/hugo-image-processing/.

Basically, you create an /assets folder at the root of your project and put whatever images you want under there.

But Eric is using HTML, not markdown.

To use markdown, you have to set up a shortcode.

The shortcode will be under layouts/shortcodes, and look like this:

layouts/shortcodes/little-duck-opt.html
```html
{{ $image := resources.Get "little-duck.png" }}
<img src="{{ ( $image.Resize "300x q50" ).RelPermalink }}" />
```
If you use subdirectories under /assets (e.g. /assets/images), you need to add that to the ```resources.Get``` directive:

```html
{{ $image := resources.Get "/images/little-duck.png" }}
<img src="{{ ( $image.Resize "300x q50" ).RelPermalink }}" />
```

There are lots of variations on the above, like fitting to a specific size (```$image.Fit "200x200"```) or fill (```$image.Fill "1000x300"```). You can also add Center, Bottom, Top (```$image.Fill "1000x300 Center"```).

Call the shortcode in the markdown file like this:

```
{{< little-duck-opt >}}

```

What I don't know how to do (yet), is make this more universal: basically perform processing on _every_ image under /assets. The code provided in the documentation simply doesn't work.

### Shortcodes
Given that shortcodes are _required_ to make image optimization available in markdown files, it's odd that the author decided that shortcodes were outside the scope of this tutorial. No wonder the Images section is so messed up. I don't think the author actually understands either topic very well (and the official documentation is also a mess).

Mike Dane. "Shortcode Templates | Hugo Static Site Generator | Tutorial 22". _YouTube_, 10 September 2017, https://youtu.be/Eu4zSaKOY4A.

Simple example is to embed some text and make it blue.

Shortcode files go under layouts/shortcodes:

layouts/shortcode/colormytext.html
```html
<p style="color:{{.Get 0}}">This is my colored text</p>
```

Embed in the page:

```
{{< colormytext blue >}}
```
### Search Engine Optimization (SEO)

Add robots.txt with change to config.toml and meta tags with new code in <head> of baseof.html _default layout.

Turn on automatic robots.txt:

config.toml
```
enableRobotsTXT = true
```

Meta tags (insert in <head> of layouts/_default/baseof.html):

```html
<meta name="keywords" content="blog,travel,food,coffee,bread,canada" />
<meta property="og:site_name" content="{{ $.Site.Title }}" />
<meta property="og:title" content="{{ $.Site.Title }}" />
<meta name="twitter:site" content="@your-twitter-handle" />
<meta name="twitter:creator" content="@your-twitter-handle" />
<meta name="theme-color" content="#1a202c">

{{ if .IsPage }}
  <title>{{ .Title }} | {{ $.Site.Title }}</title>
  <meta property="og:type" content="article" />
  <meta property="og:title" content="{{ .Title }}" />
  <meta property="og:description" content="{{ .Summary }}" />
  <meta name="description" content="{{ .Summary }}" />
  <meta property="twitter:title" content="{{ .Title }}" />
  <meta property="twitter:description" content="{{ .Summary }}" />
  <meta property="article:author" content="your-name" />
  <meta property="article:published_time" content="{{ .Date }}" />
{{ else }}
  <title>{{ $.Site.Title }}</title>
  <meta name="twitter:card" content="summary" />
  <meta property="og:title" content="{{ $.Site.Title }}" />
  <meta property="og:description" content="blog about dancing" />
  <meta name="description" content="blog about dancing" />
{{ end }}
```

### CSS Minification and fingerprinting

Another learning experience. The exercise is to move styles.css from /static to /assets. Except of course I deleted the sample styles.css as instructed many lessons ago.

Fingerprinting prevents browsers from caching stale css by appending a random string to the end of the filename so the browser is alerted when it is updated.

The important point is that Hugo will automatically minify any custom css if it is under /assets and the following code is added to baseof.html:

```html
{{ $style := resources.Get "styles.css" | minify | fingerprint }}
<link rel="stylesheet" href="{{ $style.Permalink }}">
```
If using subdirectories off assets, e.g. "/assets/css/styles.css", then that path needs to be in the ```resources.Get``` line:


```html
{{ $style := resources.Get "/css/styles.css" | minify | fingerprint }}
<link rel="stylesheet" href="{{ $style.Permalink }}">
```
