# Notes for Mini Hugo Course

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

## Optimizing

### Images
This section is a mess, to the point of being incoherent.

It involves leveraging the [image processing](https://gohugo.io/content-management/image-processing/) with [Hugo Pipes](https://gohugo.io/hugo-pipes/introduction/), although how these work together is never explained. The poor quality of the cited documentation may be partly to blame.

Basically, you're supposed to create a folder named "assets" at the root of your project and put your images under there rather than under "static". Then you invoke Pipes through some magical code that isn't explained, and trigger its use in an unidentified file.

This is a better tutorial:

Eric Murphy. "How to Automatically Optimzize Images in Hugo". _YouTube_, 27 October 2021, https://youtu.be/6qy4Aht1xsE.

Still, Eric is using HTML for his pages: not markdown. To get an better understanding of how things work I found Alex Lakatos. "How to Add Image Processing to Your Hugo Website and Improve Performance". _AlexLakatos_, 17 July 2020, https://alexlakatos.com/web/2020/07/17/hugo-image-processing/.

Basically, you create an assets/ folder at the root of your project and put whatever images you want under there.

But Eric is using HTML, not markdown.

To use markdown, you have to set up a shortcode.

The shortcode will be under layouts/shortcodes, and look like this:

layouts/shortcodes/little-duck-opt.html
```html
{{ $image := resources.Get "little-duck.png" }}
<img src="{{ ( $image.Resize "300x q50" ).RelPermalink }}" />
```
Important to note that you don't put any path info in the resources.Get configuration. Just enter the file name, Hugo will find it wherever it is under assets/.

There are lots of variations on the above, like fitting to a specific size (```$image.Fit "200x200"```) or fill (```$image.Fill "1000x300"```). You can also add Center, Bottom, Top (```$image.Fill "1000x300 Center"```).

Call the shortcode in the markdown file like this:

```
{{< little-duck-opt >}}

```

### Shortcodes
Given that shortcodes are _required_ to make image optimization available in markdown files, it's odd that the author decided that shortcodes were outside the scope of this tutorial. No wonder the Images section is so messed up.

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
