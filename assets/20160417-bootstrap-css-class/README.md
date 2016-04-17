# Bootstrap CSS classes

Bootstrap is a framework for building beautifully consistent web pages, easily. It has many aspects, but one such aspect is a convenient set of classes that can be applied to your markup. Just add a `link` to the bootstrap css, and start applying classes. You can either grab the source files yourself, or link from a CDN, as per the [documentation](http://getbootstrap.com/getting-started/)

```html
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css" integrity="sha384-1q8mTJOASx8j1Au+a5WDVnPi2lkFfwwEAa8hDDdjZlpLegxhjVME1fgjWPGmkzs7" crossorigin="anonymous">
```

I won't cover the whole aspects of the class framework, but after creating a `container` or `container-fluid` class, a couple of features are.


## Grid system

When using bootstrap, it comes with a grid with 12 available columns. You start by wrapping a row with the `row` class, then each element in the grid can be aligned depending on which classes you supply.

You can add classes specific to the screen size you are targeting.

* `xs` (Extra small, i.e. phones. Less that 768px wide)
* `sm` (Small, i.e. tablets. Greater than or equal to 768px)
* `md` (Medium, i.e. desktops. Greater than or equal to 992px)
* `lg` (Large, i.e. desktops. Greater than or equal to 1200px)

Once you know which screen size you are targeting, and apply a class prefixed with `col`, then the screen size, then the width (keeping in mind the grid system includes 12 columns), separated by a hyphen. So, if I wanted to target a large screen and use a width of 2 columns, my class would become `col-lg-2`.

Additionally, you can offset the column if you don't want it flush against the left. In this case, you use `col`, then the screen size, `offset`, and finally, the number of columns to offset by. So, if I wanted to targt a large screen and offset by 2 columns, my class would become `col-lg-offset-2`.

This, would give something to the effect:

```html
<div class="row">

    <p class="col-lg-2 col-lg-offset-2">Lorem ipsum</p>

</div>
```

## Style buttons

Apply the base `btn` class, then the optionally classes to choose a particular style:

* btn-default
* btn-primary (blue background)
* btn-success (green background)
* btn-info (blue background)
* btn-warning (orange background)
* btn-danger (red background)

e.g.

```html
<button class="btn btn-default">Click Me</button>
```

## More Details

For full details on the class options, I encourage you to check out the documentation.
