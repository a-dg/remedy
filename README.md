# Remedy

The Remedy mixin and function allow you to convert any pixel-like CSS value (pixels or points) to its `rem` equivalent. They convert what they’re supposed to, and leave everything else alone.


## Capabilities

- Automatically remove units from zero values: `0px` becomes `0`
- Optionally round values: `6.25px` becomes `6px` and `0.375rem`
- Handy shortcut aliases: use `remedy()` or `r()`, and `remedy_round()` or `rr()`
- Pass any values whatsoever, Remedy *will not* choke on anything:
  - Values without units
  - Percentages and other non-convertible numeric values
  - Strings, like `auto`, `inherit`, and background image URLs
  - Multiple lists of multiple values, such as multiple background sizes
  - The dreaded `!important`
- *Thorough-ass* documentation

#### Additional mixin features
- Optionally provides a `px` fallback
- Removes duplicate lines if `px` fallback matches `rem` output
- Automatically [rounds values intelligently](#mixin-rounding) based on property name

#### Additional function features
- [Shortest possible syntax](#function-syntax) that is not plain CSS
- Won’t wreak havoc on your syntax highlighting
- It’s really short


## Mixin versus function

Based on your needs, you can choose to use either the mixin or the function. You will likely not need to use both.

#### Mixin syntax

```scss
div {
  @include remedy(margin, 7px 0 12px 20%);
  @include r(left, 56px); // Shortcut!
}
```

If you require a `px` fallback for browsers that don’t support `rem` units (IE 8), you should use the mixin. It can be configured to output two lines of compiled CSS: the first with `px` values, the second with `rem` values. Browsers that don’t support `rem` units will simply ignore the second line.

#### Function syntax

```scss
div {
  margin: remedy(7px 0 12px 20%);
  left: r(56px); // Shortcut!
}
```

Let’s face it, the `@include` syntax for the mixin is long and tedious and you will hate looking at it. If you’re not developing for IE 8, you probably don’t need a `px` fallback, and you should therefore consider using the function. It’s nice and tidy.

> **Bonus confusion:** If you’re really sharp, you can compile two different CSS files — one for `px` and one for `rem` — then selectively load either file depending on the environment. Easily tweak `px` values locally, while delighting nerds with your `rem` usage on production. So while the `remedy()` function can’t generate a `px` fallback, compiling multiple CSS files makes working with different units possible.


## Configuration

Begin by setting the output types and the base font size, then import `_remedy.scss`.

```scss
$output_px: true;
$output_rem: true;
$global_font_size: 16px;
@import 'remedy';
```


## Intended usage

To use the mixin, specify the property, followed by the desired values, all within the parentheses:

```scss
div {
  @include remedy(margin, 20px auto);
  @include remedy(font-size, 28pt);
  @include remedy(border-top, 7px solid #f90 !important);
  @include remedy(background, #fff url('../img/icon.png') 15px 10px no-repeat);
  @include remedy(background-size, 44px auto, 50% 312px);
}
```

Or combine multiple properties in one map, separated by commas:

```scss
div {
  @include remedy((
    margin: 20px auto,
    border-top: 7px solid #f90 !important,
    background-size: (44px auto, 50% 312px),
  ));
}
```

To use the function, wrap as much or as little of the value as you want in the parentheses:

```scss
div {
  margin: r(20px auto);
  font-size: r(28pt);
  border-top: r(7px) solid #f90 !important;
  background: r(#fff url('../img/icon.png') 15px 10px no-repeat);
  background-size: r(44px auto, 50% 312px);
}
```

See [example.scss](scss/example.scss) for a complete example of the configuration and usage.

> Remember, Remedy leaves anything it can’t convert alone, so if you change a value from `13px` to `0`, or `50px` to `2.7`, or `24pt 2em` to `auto`, it doesn’t matter. You don’t need to go to the trouble of removing the mixin or function, and it will still work.


## Flagrantly irresponsible usage

Since Remedy will allow any garbage you throw its way to pass through unharmed, it is basically unbreakable.

```scss
.nonsense {
  zoom: r(1);
  content: r('\2014 \0020');
  font-family: r('Highland Sans');
  vertical-align: r(middle);
  border-bottom: r(12vh) r(groove) r(papayawhip) r(!important);
  background-image: r(none, none, none, none, r(inherit));
}
```


## Rounding values

The mixin and the function handle rounding in slightly different ways.

> Note: these methods *do not* round the converted `rem` values, they only snap the `px` values to integers before converting them. Rounding `rem` values would be considered uncouth.

#### Function rounding

By default, `rem` values are calculated from the `px` values as they are passed in, without modifying them. If there’s a chance that the original `px` values are not integers, you can optionally round them before converting to `rem` values. Do this by calling the `remedy_round()` function as such:

```scss
div {
  padding-left: remedy_round($column_gutter / 3);
  border-width: rr($offset * 0.7); // Shortcut!
}
```

When working with the function, *you* decide which values need to be rounded.

#### Mixin rounding

For once, the mixin has the upper hand here. Since it has access to the property that’s being converted, it can make informed decisions about which values to round, and which ones to leave alone.

Most pixel-like values on dimensional properties — properties related to an element’s size, position, and box model — should be rounded. This applies to properties like `padding`, `width`, and `border`, but also to `text-indent`, `background-size`, and `marker-offset`.

On the other hand, certain properties actually benefit from the subtle adjustments afforded by floating point values:

- `font-size`
- `letter-spacing`
- `word-spacing`

Something like `letter-spacing: 0.2px` can have a huge impact on legibility, so it’s worth preserving that value. Since the `remedy()` mixin knows which property you’re converting, it can automatically preserve these exceptions and round the rest.

```scss
div {
  @include remedy((
    margin: 20.3px,    // Rounded to 20px / 1.25rem
    font-size: 20.3px, // Not rounded / 1.26875rem
  ));
}
```

Of course, you can use the `remedy_round()` mixin to explicitly round all pixel-like values, regardless of which property they belong to:

```scss
div {
  @include remedy_round((
    margin: 20.3px,    // Rounded to 20px / 1.25rem
    font-size: 20.3px, // Rounded to 20px / 1.25rem
  ));
}
```

Using the `remedy()` mixin provides the exact same output as the `remedy_round()` mixin for all properties but the three exceptions: `font-size`, `letter-spacing`, and `word-spacing`.


## Credit where credit is due

Mixin based on the [rem mixin by Ray Brown](https://github.com/bitmanic/rem). `strip_units()` function lifted from [Zurb Foundation](https://github.com/zurb/foundation). Map syntax inspired by the [rem mixin by Pierre Burel](https://github.com/pierreburel/sass-rem).
