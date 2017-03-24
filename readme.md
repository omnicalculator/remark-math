# Remark Math

[![npm](https://img.shields.io/npm/v/remark-math.svg)](https://www.npmjs.com/package/remark-math)
[![Build Status](https://travis-ci.org/Rokt33r/remark-math.svg?branch=master)](https://travis-ci.org/Rokt33r/remark-math)

Math Inline and Block supporting for Remark

## What does Remark Math?

`remark-math` parses `$` for `inlineMath` node and `$$` for `math` node.

Also, you can transform the tex content of `inlineMath` and `math` nodes into html by `rehype-katex` or `remark-html-katex`.

![intro](resources/intro.png)

## Usages

There are two examples for server-side([`examples/nodejs`](examples/nodejs)) and browser-side([`examples/webpack`](examples/webpack), via webpack).

> You can run the demo by `npm run demo:nodejs` and `npm run demo:webpack`.

### Basic usages(Using `rehype-katex`, a little verbose but recommended)

Install dependencies

```sh
npm i -S remark remark-math remark-rehype rehype-katex rehype-stringify
```

```js
const remark = require('remark')
const math = require('remark-math')
const remark2rehype = require('remark-rehype')
const katex = require('rehype-katex')
const stringify = require('rehype-stringify')

// Raw String => MDAST => HAST => transformed HAST => HTML
const processor = remark()
  .use(math)
  .use(remark2rehype)
  .use(katex)
  .use(stringify)

// https://en.wikipedia.org/wiki/Lift_(force)#Lift_coefficient
const rawString = `Lift($L$) can be determined by Lift Coeeficient ($C_L$) like the following equation.

$$
L = \\frac{1}{2} \\rho v^2 S C_L
$$
`

const result = processor.processSync(rawString).toString()
/* result
<p>
  Lift(<span class="inlineMath"><span class="katex">...</span></span>) can be determined by Lift Coeeficient (<span class="inlineMath"><span class="katex">...</span></span>) like the following equation.
</p>
<div class="math">
  <span class="katex-display"><span class="katex">...</span></span>
</div>
*/
```

### Another usages(Using `remark-html-katex`)

```sh
npm i -S remark remark-math remark-html-katex remark-html
```

```js
const remark = require('remark')
const math = require('remark-math')
const katex = require('remark-html-katex') // Use remark-html-katex
const html = require('remark-html')

// Raw String => MDAST => transformed MDAST => HTML
const processor = remark()
  .use(math)
  .use(katex)
  .use(html)
```

### Using only math inline(or math block)

You can access separated processors by `remark-math/inline` and `remark-math/block`

```js
const remark = require('remark')
const remark2rehype = require('remark-rehype')
const katex = require('rehype-katex')
const stringify = require('rehype-stringify')

const mathInline = require('remark-math/inline')
// const mathBlock = require('remark-math/block')

// Parse only inline
const processor = remark()
  .use(mathInline)
  .use(remark2rehype)
  .use(katex)
  .use(stringify)
```

## API

### `remark-math`

`remark-math` does not handle any option.

### `rehype-katex` and `remark-html-katex`

```js
const katex = require('rehype-katex')

const processor = remark()
  .use(math)
  .use(remark2rehype)
  .use(katex, {
    throwOnError: false,
    errorColor: '#FF0000',
    inlineDoubleDisplay: false
  })
  .use(stringify)

```

#### `options.throwOnError`

Throw if a KaTeX parse error occurs. (default: `false`)

#### `options.errorColor`

This is a same option of KaTeX. As long as `options.throwOnError` is not `true`, Malformed TeX will be colored by `options.errorColor`. (default: #cc0000)

> [KaTeX#rendering-options](https://github.com/Khan/KaTeX#rendering-options)

#### `options.inlineDoubleDisplay`

Parses inline `$$` math as `inlineMath` but displays it like `\displaystyle` or `math` display mode. (default: `false`)

This option, together with a CSS rule like `.inlineMathDouble {display: block; text-align: center;}` allows authors to have equations inside paragraphs on a separate line:

![Example](https://cloud.githubusercontent.com/assets/2022803/24314687/26c96bb8-10e3-11e7-928e-f93cff49b456.png)

## Specs

### Escaped Dollars

Dollar signs can be escaped by back slash, `\`.

```
\$\alpha\$
```

![Escaped dollars](resources/escaped-dollars.png)

### Escaped dollars in math block/inline ([Super factorial](https://en.wikipedia.org/wiki/Factorial#Superfactorial))

```
$\alpha\$$

$$
\beta\$
$$
```

![Super factorials](resources/super-factorial.png)

### Double dollars in inline

Some TeX packages and Markdown processors use double dollars, `$$`, as a inline token. Remark Math will parse it also properly.

```
$$\alpha$$
```

![Double dollars as a inline token](resources/double-dollars.png)

## License

MIT © Junyoung Choi

[katex]: https://github.com/Khan/KaTeX
