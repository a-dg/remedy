# rem()

The `rem()` mixin and function allow you to convert any pixel-like CSS value (pixels or points) to its `rem` equivalent. They convert what they’re supposed to, and leave everything else alone.

They *will not* choke on:

- Values without units
- Percentages and other non-convertible numeric values
- Strings, like `auto`, `inherit`, and background image URLs
- Multiple lists with multiple values, such as multiple background sizes
- The dreaded `!important`


## Mixin versus function

Based on your needs, you can choose to use either the mixin or the function. You will likely not need to use both.

#### Mixin

```scss
div {
  @include rem(margin, 7px 0 12px 20%);
}
```

If you require a `px` fallback for browsers that don’t support `rem` units (IE 8), you should use the mixin. It will output two lines of compiled CSS: the first with `px` values, the second with `rem` values. Browsers that don’t support `rem` units will simply ignore the second line.

#### Function

```scss
div {
  margin: rem(7px 0 12px 20%);
}
```

Let’s face it, the `@include` syntax for the mixin is long and tedious and you will hate looking at it. If you’re not developing for IE 8, you probably don’t need a `px` fallback, and you should therefore consider using the function. It’s nice and tidy.

```scss
div {
  margin: r(7px 0 12px 20%); // Bonus shortcut!
}
```

Both the mixin and the function make use of various attendant utility functions for proper operation.


## Configuration

Begin by setting the output types and the base font size, then importing the functions and/or mixin. Note: the mixin requires several functions in [_functions.scss](scss/_functions.scss).

```scss
$output_rem: true;
$base_font_size: 16px;
@import 'functions';
@import 'mixins';
```


## Intended usage

To use the mixin, specify the property, followed by the desired values, all within the parentheses:

```scss
.with-px-fallback {
  @include rem(margin, 20px auto);
  @include rem(font-size, 28pt);
  @include rem(border-top, 7px solid #f90 !important);
  @include rem(background, #fff url('../img/icon.png') 15px 10px no-repeat);
  @include rem(background-size, 44px auto, 50% 312px);
}
```

Or combine multiple properties in one map, separated by commas:

```scss
.combined {
  @include rem((
    margin: 20px auto,
    border-top: 7px solid #f90 !important,
    background-size: (44px auto, 50% 312px)
  ));
}
```

To use the function, wrap as much or as little of the value as you want in the parentheses:

```scss
.no-px-fallback {
  margin: r(20px auto);
  font-size: r(28pt);
  border-top: r(7px) solid #f90 !important;
  background: r(#fff url('../img/icon.png') 15px 10px no-repeat);
  background-size: r(44px auto, 50% 312px);
}
```

See [example.scss](scss/example.scss) for a complete example of the configuration, imports, and usage.

Remember, `rem()` leaves anything it can’t convert alone, so if you change a value from `13px` to `0`, or `50px` to `2.7`, or `24pt 2em` to `auto`, it doesn’t matter. You don’t need to go to the trouble of removing the mixin or function, and it will still work (by not doing any).


## Flagrantly irresponsible usage

Since `rem()` will allow any garbage you throw its way to pass through unharmed, it is basically unbreakable.

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

By default, `rem` values are calculated from the `px` values as they are passed in, without modifying them. If there’s a chance that the original `px` values are not integers, you can optionally round them off before converting to `rem` values. Do this by calling the `rem_round()` mixin or function as such:

```scss
div {
  @include rem_round(margin, 20.3px 8.6px);
  padding-left: rem_round($column_gutter / 3);
  border-width: rr($offset * 0.7); // Shortcut!
}
```

Note: this *does not* round off the converted `rem` values, it only snaps the `px` values to integers before converting them.


## Credit where credit is due

Mixin based on the [rem mixin by Ray Brown](https://github.com/bitmanic/rem). `strip_units()` function lifted from [Zurb Foundation](https://github.com/zurb/foundation). Map syntax inspired by the [rem mixin by Pierre Burel](https://github.com/pierreburel/sass-rem).
