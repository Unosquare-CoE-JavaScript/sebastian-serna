# SASS Fundamentals

### *Why would we use SASS?*

CSS has some pitfalls it does provide us many features but it particularly doesn't allow us to encapsulate, define variables, it is not composable, bad modularity primitives and many other pitfalls that force us to write a lot more code

SASS is a preprocessor and it means we're not really writing CSS but more like an abstraction of it. The benefits of a processor is that we can compile it to CSS, it allows us to parameterize and create variables, it is composable and allows us to modularize our styles.

# SASS Basics

Sass follows identical css syntax, you define a selector and declares styles however it facilitates us to write less code with nesting and scoping. With normal css you would have to write selectors to each nested elements let's see and example

## CSS

```css
.container .main {
	margin-left: 220px;
}

.container .sidebar {
	width: 200px;
}
```

## SASS

```scss
.container {
	// container styles
	.main {
		margin-left: 220px;
	}
	// The ampersand refers to the scope of the parent block
	&.sidebar {
		width: 200px;
	}
}
```

As you can see sass styles can be placed in the declaration block of a parent element and this already allows us to write optimize code within its own scope.

# Variables, partials and @import

As mentioned before one of the benefits of using sass is that it makes our code modular, with sass we can import partials which are separate files that'll turn to css files and be imported. We can declare variables of different types like numbers, colors, strings, booleans and built in structures like list and maps to be reused in many styles

```scss 
// _alert-styles.css

// declare the variable
$error-color: #f00;


.alert-error {
	$text-color: #ddd;
	// use the variable
	background-color: $error-color;
}

// separate file will add the styles already defined in the partial
@import 'alert-styles';
```

# Mixins

Mixins are sets of styles to be re-used and included in block scopes of our selectors

```scss
@mixin alert-text {
	background-color: #f00;
	color: white;
}

// arguments
@mixin sucess-text($color) {
	background-color: $color;
	color: white;
}

.error-text {
	@include alert-text;
}
```

With mixins we can encapsulate styles with the @content keyword

```scss
@mixin foo($color) {
	color: $color;
	.inner {
		// encapsulate styles
		@content;
	}
}

.btn {
	@include foo {
		// encapsulate styles
		color: red;
	}
}
```

# Functions

Functions in sass are basically values, they'll always return a value based on inputs

```scss
```scss
@function remove-where($list, $condition) {
  $new-list: ();
  $separator: list.separator($list);
  @each $element in $list {
    @if not meta.call($condition, $element) {
      $new-list: list.append($new-list, $element, $separator: $separator);
    }
  }
  @return $new-list;
}

$fonts: Tahoma, Geneva, "Helvetica Neue", Helvetica, Arial, sans-serif;

.content {
  @function contains-helvetica($string) {
    @return string.index($string, "Helvetica");
  }
  font-family: remove-where($fonts, meta.get-function("contains-helvetica"));
}
```

# Control flow

As a preprocessor sass provides us with directives for control flow like a programming language

#### @if directive

This directive allows us to take decisions based on inputs

```scss
@mixin foo($size) {
	font-size: $size;
	@if $size > 20 {
		line-height: $size;
	}
}
```

#### @for @while @each directives

These directives allow us to iterate over lists and maps

```scss
// @for
$base-color: #036;

@for $i from 1 through 3 {
  ul:nth-child(3n + #{$i}) {
    background-color: lighten($base-color, $i * 5%);
  }
}

// @while
@function scale-below($value, $base, $ratio: 1.618) {
  @while $value > $base {
    $value: math.div($value, $ratio);
  }
  @return $value;
}

$sizes: 40px, 50px, 80px;

// @each
@each $size in $sizes {
  .icon-#{$size} {
    font-size: $size;
    height: $size;
    width: $size;
  }
}
```

# CSS Architecture - BEM

BEM stands for Block Element Modifier

- Block is standalone entity like a component
- Element: Is a piece of a block defined internally
- Modifier: Is a decorator used to change the behavior appearance or behavior of an element
