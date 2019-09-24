# Vue Griddle
A helpful grid system for your front-end development. Comes with a nifty overlay to help you visualize your project's grid.

# Installation & Configuration
Vue Griddle depends on your vue project having a webpack build process with the following dependencies:
- `node-sass`
- `sass-loader`

Once you have those installed and properly configured with webpack,install `@braid/vue-griddle` with npm:
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
@import "../../node_modules/@braid/griddle-scss/scss/griddle";
```

You can override the following variables by declaring your own values *before* the Griddle `.scss` import.

```scss
$g-breakpoints: (
  'xs': 375px,
  's': 575px,
  'm': 768px,
  'l': 1024px,
  'xl': 1380px,
  'xxl': 1600px
) !default;

// these values can usually be found in your design program of choice under "layout" or "grid" settings
$g-max-body-width: 1216px !default;
$g-max-column-width: 72px !default;
$g-max-gutter-width: 32px !default;
$g-column-count: 12 !default;
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
Griddle comes with a `g-container` mixin creates a root element constrained to the defined max width (`$g-max-body-width`). `g-container()` takes an argument to choose margin or padding for the area outside of the grid (defaults to margin).

```scss
.row {
  @include g-container();
  // will use margin for outside grid area
}
```

```scss
.row {
  @include g-container(padding);
  // will use padding for the outside grid area. Useful for full-width section backgrounds.
}
```

## Spans (aka Columns)
In order to align items to your grid, use the `g-span()` function. The `g-span()` function takes three arguments:

```scss
.my-div {
  width: g-span($columns, $extra_gutters (optional), $context (optional));
}
```

Using `g-span()` tells your element how many column-widths and (optionally) how many _extra_ gutter-widths you'd like to use for various positional properties. Griddle will do the math and give your properties the correct percentage value. Spans include the gutters between declared columns, so a `g-span(4)` will include 4 column-widths and 3 gutter-widths automatically.

The context (optional) is assumed to be the full `$g-max-body-width` unless you specify otherwise. The context argument is used to calculate root grid-column and grid-gutter widths while inside of a non-`$g-max-body-width` element.

Examples:
```scss
// assuming a 12-column grid, create a full width element
.my-div {
  width: g-span(12);
}

// Assuming a 12-column grid, create an element that is centered at 1/3 the grid width
.my-div {
  width: g-span(4);
  margin-left: g-span(4);
}

// assuming a 12-column grid, create an element that spans 4 columns and one extra gutter width
.my-div {
  width: g-span(4, 1);
}

// assuming a 12-column grid, create an element that spans 6 columns but is pulled to the left by a negative gutter width
.my-div {
  width: g-span(6, 1);
  margin-left: - $g-gutter;
}

// Sometimes you want a parent that is X root columns wide, with children that are each Y root columns wide. This is where the context argument applies.
.my-parent-div {
  // set a value that is less that the max grid width
  width: g-span(9);

  .my-children {
    // in order to calculate the correct percentage width of root columns (e.g. 3-of-12 root columns) rather than getting a percent of the 9-of-12 column parent element, pass in the correct context width as a third argument
    width: g-span(3, 0, g-span(9))
  }
}
```

In the event you ever need a decimal value to work with rather than a percent, simply use `g-span-decimal()` which takes the same arguments as `g-span()`.

## Breakpoints
In order to use the breakpoints that come with Griddle use the provided `gbp()` (griddle-breakpoint) function which will access a breakpoint value by name from the `$g-breakpoints` map (you can define your own by declaring the value of `$g-breakpoints` before importing the griddle `.scss` file).

example:
```scss
.my-div {
  // assuming a 12-column grid, define an element that gets progressively narrower as screen size increases, but remains centered.
  width: span(12);

  @media (min-width: gbp(m)) {
    width: span(10);
    margin-left: span(1, 1);
  }

  @media (min-width: gbp(xl)) {
    width: span(8);
    margin-left: span(2, 1);
  }
}
```

Griddle uses the default breakpoint values to define various level of insetting from the edge of the browser viewport across screen sizes. If needed though, there's no reason you can't use arbitrary breakpoint values as well in one-off scenarios.

```scss
.my-special-div {
  // assuming a 12-column grid, define an element that gets progressively narrower as screen size increases, but remains centered.
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