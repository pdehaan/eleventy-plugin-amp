# Eleventy AMP Plugin

Quickly build interactive websites with [Eleventy](https://www.11ty.dev) & [AMP](https://amp.dev).

## Installation

Available on [npm](https://www.npmjs.com/package/@ampproject/eleventy-plugin-amp).

```
npm install @ampproject/eleventy-plugin-amp --save-dev
```

## Usage

Edit your Eleventy config file (probably `.eleventy.js`) and use [`addPlugin`](https://www.11ty.dev/docs/plugins/#add-the-plugin-to-eleventy-in-your-config-file) for integrating the AMP plugin:

```js
const ampPlugin = require('@ampproject/eleventy-plugin-amp');
module.exports = function(eleventyConfig) {
  eleventyConfig.addPlugin(ampPlugin);
};
```

This will turn all pages generated by Eleventy into AMP pages.

### Using AMP Components in your templates

You can use AMP components out-of-the-box, For example, for creating an image carousel:

```html
<amp-carousel layout="responsive" width="300" height="200">
  <amp-img src="image1.png" alt="an image"></amp-img>
  <amp-img src="image2.png" alt="another image"></amp-img>
  <amp-img src="image3.png" alt="and another another image"></amp-img>
</amp-carousel>
```

There's no need to explicitly import the `amp-carousel` extension script as it will be added automatically by the AMP plugin:

```html
<head>
  ...
  <!-- this will be added automatically -->
  <script async custom-element="amp-analytics" src="https://cdn.ampproject.org/v0/amp-analytics-0.1.js"></script>
  ...
</head>
<body>
  <amp-carousel layout="responsive" width="300" height="200">
    <amp-img src="image1.png" alt="an image"></amp-img>
    <amp-img src="image2.png" alt="another image"></amp-img>
    <amp-img src="image3.png" alt="and another another image"></amp-img>
  </amp-carousel>
</body>
```

Checkout the [AMP documentation for more components](https://amp.dev/documentation/components/) to use.

### Markdown Support

Markdown images are automatically converted to `amp-img`:

```
![image](https://unsplash.it/500/400)
```

The AMP Plugin needs to be able to determine the image dimensions from the image file. See [Options](#Options) for how to resolve local image files. 

### Shortcodes

AMP offers a rich set of third-party embeds. Some of these embeds are available via [shortcodes](https://www.11ty.dev/docs/shortcodes/) (in all templating languages). They all follow the same pattern:

```
{% shortCodeName ['shortCodeParams']* layoutDefinition? %}
```

Shortcodes support all [AMP layouts](https://amp.dev/documentation/guides-and-tutorials/learn/amp-html-layout/?format=websites). Layouts are always defined last and use the same parameter order: `LAYOUT_NAME? WIDTH? HEIGHT?`. Whereas `LAYOUT`, `WIDTH` and `HEIGHT` are optional depending on the layout.

Here are a few examples:

* `fixed` layout with `width=300` and `height=200`:
  ```
  {% video 'video.mp4' 300 200 %}
  ```
* `responsive` layout with `width=300` and `height=200`:
  ```
  {% video 'video.mp4' 'responsive' 300 200 %}
  ```
* `fixed-height` layout with `height=200`:
  ```
  {% video 'video.mp4' 200 %}
  ```
* `fill` layout:
  ```
  {% video 'video.mp4' 'fill' %}
  ```

#### Video

Embed a video. The first parameter needs to be the video `src`. Controls are enabled by default. The default layout is `responsive` with an aspect ratio of `16:9`.

```
{% video 'https://amp.dev/static/inline-examples/videos/kitten-playing.mp4' %}
```

#### Twitter

Embed a Tweet. The first parameter needs to be the tweet `id`. There is no default layout.

```
{% twitter "1182321926473162752" "responsive" 375 472 %}
```

#### YouTube

Embed a YouTube video. The first parameter needs to be the youtube video `id`. The default layout is `responsive` with an aspect ratio of `16:9`.

```
{% youtube "v0BVLgEEuMY" %}
```

#### Instagram

Embed an Instagram post. The first parameter needs to be the post `id`. The default layout is `responsive` with an aspect ratio of `1:1`.

```
{% instagram "BMQ8i4lBTlb" %}
```

#### Github Gists

Embed a [Github Gist](https://gist.github.com/). The first parameter needs to be the fist `id`. There is no default layout, the recommended layout is `fixed-height`.

```
{% gist 'b9bb35bc68df68259af94430f012425f' 197 %}
```

### CSS

AMP requires all CSS to be inlined:

```
<style>
  {% include "sample.css" %}
</style>
```

`<style>` elements don't need to be defined in the `head`, but can be added anywhere on the page. This means, it's easy to modularize your CSS. For example, you can define style and markup in the same file, e.g. `components/greeter.njk`:

```
<style>
.fancy-button {
  background: black;
  color: white;
  border-radius: 4px;
}
</style>
<button class="fancy-button">
  {{ buttonTitle }}
</button>
```

...and then use it in a different template like this:

```
{% set greeting = 'hello world' %}
{% include "components/greeter.njk" %}
```

A good idea is to use this approach to modularize your CSS to ensure that your pages will only pull in the CSS that is actually needed.

**Tip:** Modularizing CSS helps to stay within AMP’s 75kb CSS limit. The AMP plugin will automatically perform minification.

### Options

Optionally pass in an options object as the second argument to `addPlugin` to further customize this plugin.

```js
const ampPlugin = require('@ampproject/eleventy-plugin-amp');
module.exports = function(eleventyConfig) {
  eleventyConfig.addPlugin(ampPlugin, {
    // Disable AMP validation (enabled by default)
    validation: false,
    // Disable CSS minification (enabled by default)
    minifyCss: false,
    // For customizing the location of images assets, pass either a directory 
    imageBasePath: `${__dirname}/img`,
    // ... or a function that returns the correct path based on img src and outputPath.
    imageBasePath: (imageSrc, outputPath) => `${outputPath}/../img`,
  });
};
```

## Development

Run tests via:

```
$ npm test

```

### Adding new Shortcodes

You can add new shortcodes in [src/shortcodes/](src/shortcodes). It's best to copy and modify one of the existing shortcodes.

# License

Copyright 2020 The AMPHTML Authors

Licensed to the Apache Software Foundation (ASF) under one or more contributor license agreements. See the NOTICE file distributed with this work for additional information regarding copyright ownership. The ASF licenses this file to you under the Apache License, Version 2.0 (the “License”); you may not use this file except in compliance with the License. You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an “AS IS” BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.
