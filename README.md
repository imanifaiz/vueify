# vueify

> [Browserify](http://browserify.org/) transform for [Vue.js](http://vuejs.org/) components

This is just a thin adaptor on top of [vue-component-compiler](https://github.com/vuejs/vue-component-compiler). It allows you to write your components in this format:

``` html
// app.vue
<style>
  .red {
    color: #f00;
  }
</style>

<template>
  <h1 class="red">{{msg}}</h1>
</template>

<script>
  module.exports = {
    data: function () {
      return {
        msg: 'Hello world!'
      }
    }
  }
</script>
```

You can also mix preprocessor languages in the component file:

``` html
// app.vue
<style lang="stylus">
.red
  color #f00
</style>

<template lang="jade">
h1(class="red") {{msg}}
</template>

<script lang="coffee">
module.exports =
  data: ->
    msg: 'Hello world!'
</script>
```

And you can import using the `src` attribute (note you'll have to save the vue file to trigger a rebuild since the imported file is not tracked by Browserify as a dependency):

``` html
<style lang="stylus" src="style.styl"></style>
```

Under the hood, the transform will:

- extract the styles, compile them and insert them with the `insert-css` module.
- extract the template, compile it and add it to your exported options.

You can `require()` other stuff in the `<script>` as usual. Note that for CSS-preprocessor @imports, the path should be relative to your project root directory.

## Usage

``` bash
npm install vueify --save-dev
browserify -t vueify -e src/main.js -o build/build.js
```

And this is all you need to do in your main entry file:

``` js
// main.js
var Vue = require('vue')
var appOptions = require('./app.vue')
var app = new Vue(appOptions).$mount('#app')
```

## Enabling Pre-Processors

You need to install the corresponding node modules to enable the compilation. e.g. to get stylus compiled in your Vue components, do `npm install stylus --save-dev`.

These are the built-in preprocessors:

- stylus
- less
- scss (via `node-sass`)
- jade
- coffee-script
- myth
- es6 (via `6to5` aka `babel`)

## Registering Custom Pre-Processors

Create a `vue.config.js` file at where your build command is run (usually y the root level of your project):

``` js
module.exports = function (compiler) {
  
  // register a compile function for <script lang="es">
  compiler.register({
    lang: 'es',
    type: 'script',
    compile: function (content, cb) {
      // transform the content...
      cb(null, content)
    }
  })

}
```


## Syntax Highlighting

And here's a [SublimeText package](https://github.com/vuejs/vue-syntax-highlight) for enabling language highlighting/support in these embbeded code blocks.

## Example

For an example setup, see [vuejs/vueify-example](https://github.com/vuejs/vueify-example).

---

If you use Webpack, there's also [vue-loader](https://github.com/vuejs/vue-loader) that does the same thing.
