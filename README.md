# rem()

The `rem()` mixin and function allow you to convert any pixel-like CSS value (pixels or points) to its `rem` equivalent. They convert what they’re supposed to, and leave everything else alone.

They *will not* choke on:

- Values without units
- Percentages and other non-convertible numeric values
- Strings, like `auto`, `inherit`, and background image URLs
- Multiple values, such as multiple background sizes
- The dreaded `!important`


## Mixin versus function

Based on your needs, you can choose to use either the mixin or the function. You will likely not need to use both.

*Mixin:*

```scss
@include rem(margin, 7px 0 12px 20%);
```

If you require a `px` fallback for browsers that don’t support `rem` units (IE 8), you should use the mixin. It will output two lines of compiled CSS: the first with `px` values, the second with `rem` values. Browsers that don’t support `rem` units will simply ignore the second line.

*Function:*

```scss
margin: rem(7px 0 12px 20%);
```

Let’s face it, the `@include` syntax for the mixin is long and tedious and you will hate looking at it. If you’re not developing for IE 8, you probably don’t need a `px` fallback, and you should therefore consider using the function. It’s nice and tidy.

```scss
margin: r(7px 0 12px 20%); // Bonus shortcut!
```

Both the mixin and the function make use of an included `strip_units()` function for proper operation.


## Configuration

Begin by setting the output types and the base font size, then importing the function and/or mixin. Note: the mixin requires the `strip_units()` function in [_function.scss](scss/_function.scss).

```scss
$output_rem: true;
$base_font_size: 16px;
@import 'function';
@import 'mixin';
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


## Credit where credit is due

Mixin based on the [rem mixin by Ray Brown](https://github.com/bitmanic/rem). `strip_units()` function lifted from [Zurb Foundation](https://github.com/zurb/foundation).
