# Vue Griddle
A helpful grid system for your front-end development. Comes with a nifty overlay to help you visualize your project's grid as you're working.

![Griddle Example](/img/braid-grid.mov.gif "Griddle on https://www.wearebraid.com")

Live Examples (`control + shift + L` to toggle grid):
- https://wearebraid.com
- https://www.gardenary.com


# Installation & Configuration
Vue Griddle is a semantic grid leveraging Sass meaning it depends on your vue project having a webpack build process with the following dependencies:
- `node-sass`
- `sass-loader`

Once those dependencies are installed and properly configured with webpack, install `@braid/vue-griddle` with npm:
```bash
npm install @braid/vue-griddle
```
then import and include the .vue component directly it in your project.
```js
import Griddle from '@braid/vue-griddle/src/Griddle.vue'
```

nuxt.js `layouts/default.vue` example:
```html
<template>
  <div>
    <nuxt />
    <Griddle />
  </div>
</template>

<script>
import Griddle from '@braid/vue-griddle/src/Griddle.vue'

export default {
  components: {
    Griddle
  }
}
</script>
```

In your global styles, include the `.scss` file for supporting styles and variable definitions:

```scss
@import "path/to/node_modules/@braid/griddle-scss/scss/griddle";
```

In order for the overlay to show, you probably want to include the base overlay styling as well:

```scss
@import "path/to/node_modules/@braid/griddle-scss/scss/griddle-default-overlay";
```

All combined together, your structure will probably look something like this:

```scss
@import "griddle-overrides";
@import "path/to/node_modules/@braid/griddle-scss/scss/griddle";
@import "path/to/node_modules/@braid/griddle-scss/scss/griddle-default-overlay";
@import "another-sass-file";
@import "another-sass-file";
// etc
```

You can override the following variables by declaring your own values *before* the Griddle `.scss` import. it is recommended to put these overrides into their own file so that you can easily ensure that they come immediately before the main `griddle.scss` file.

```scss
// these values can usually be found in your design program of choice under
// "layout" or "grid" settings
$g-max-column-width: 72px !default;
$g-max-gutter-width: 32px !default;
$g-column-count: 12 !default;
$g-max-body-width: ($g-column-count * $g-max-column-width) + (($g-column-count - 1) * $g-max-gutter-width) !default;

$g-breakpoints: (
  'base': (
    'inset': 1em
  ),
  'xs': (
    'width': 375px,
    'inset': 1em,
  ),
  's': (
    'width': 575px,
    'inset': 1.5em
  ),
  'm': (
    'width': 768px,
    'inset': 2em
  ),
  'l': (
    'width': 1024px,
    'inset': ($g-max-column-width / $g-max-body-width) * 100%
  ),
  'xl': (
    'width': 1380px,
    'inset': auto
  ),
  'xxl': (
    'width': 1600px,
    'inset': auto
  ),
  'max-body': (
    'width': $g-max-body-width + ($g-max-column-width * 2),
    'inset': auto
  )
) !default;
```

The following variables are available for your use and are calculated based on the values defined above

```scss
$g-column //percentage
$g-gutter //percentage
$g-column-decimal
$g-gutter-decimal
```

# Useage
Once the component is included in your project (as close to the root `html` element as possible) you can toggle the grid visually by hitting `control + shift + L` in your browser. You should see the grid displayed over top of your project.

## Containers
Griddle comes with a `container()` mixin which creates a root element constrained to the defined max width (`$g-max-body-width`).

```scss
.row {
  @include container();
  // will use margin for outside grid area
}
```

## Spans (aka Columns)
In order to align items to your grid, use the `span()` function. The `span()` function takes three arguments:

```scss
.my-div {
  width: span($columns, $extra_gutters (optional), $context (optional));
}
```

Using `span()` tells your element how many column-widths and (optionally) how many _extra_ gutter-widths you'd like to use for various positional properties. Griddle will do the math and give your properties the correct percentage value. Spans include the gutters between declared columns, so a `span(4)` will be the sum of 4 column-widths and the 3 gutter-widths that exist between those 4 columns.

The context (optional) is assumed to be the full `$g-max-body-width` unless you specify otherwise. The context argument is used to calculate root grid-column and grid-gutter widths while inside of a non-`$g-max-body-width` element.

Examples:
```scss
// assuming a 12-column grid, create a full width element
.my-div {
  width: span(12);
}

// Assuming a 12-column grid, create an element that is centered at
// 1/3 the grid width
.my-div {
  width: span(4);
  margin-left: span(4);
}

// assuming a 12-column grid, create an element that spans 4 columns
// and one extra gutter width
.my-div {
  width: span(4, 1);
}

// assuming a 12-column grid, create an element that spans 6 columns
// but is pulled to the left by a negative gutter width
.my-div {
  width: span(6, 1);
  margin-left: - span(0, 1);
}

// Sometimes you want a parent that is X root columns wide, with children
// that are each Y root columns wide. This is where the context argument applies.
.my-parent-div {
  // set a value that is less that the max grid width
  width: span(9);

  .my-children {
    // in order to calculate the correct percentage width of root columns
    // (e.g. 3-of-12 root columns) rather than getting a percent of the
    // 9-of-12 column parent element, pass in the correct context width
    // as a third argument
    width: span(3, 0, span(9))
  }
}
```

In the event you ever need a decimal value to work with rather than a percent, simply use `span-decimal()` which takes the same arguments as `span()`.

## Breakpoints
In order to use the breakpoints that come with Griddle use the provided `bp()` function which will access a breakpoint value by name from the `$g-breakpoints` map (you can define your own by declaring the value of `$g-breakpoints` before importing the griddle `.scss` file).

example:
```scss
.my-div {
  // assuming a 12-column grid, define an element that gets progressively
  // narrower as screen size increases, but remains centered.
  width: span(12);

  @media (min-width: bp(m)) {
    width: span(10);
    margin-left: span(1, 1);
  }

  @media (min-width: bp(xl)) {
    width: span(8);
    margin-left: span(2, 1);
  }
}
```

Griddle uses the default breakpoint values to define various level of insetting from the edge of the browser viewport across screen sizes. If needed though, there's no reason you can't use arbitrary breakpoint values as well in one-off scenarios.

```scss
.my-special-div {
  // assuming a 12-column grid, define an element that gets progressively
  // narrower as screen size increases, but remains centered.
  width: span(12);

  @media (min-width: 600px) {
    width: span(10);
    margin-left: span(1, 1);
  }

  @media (min-width: 1200px) {
    width: span(8);
    margin-left: span(2, 1);
  }
}
```