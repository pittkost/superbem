# Superbem
Superbem is a very simple Sass tool for writing BEM-ish CSS with ease.

## The problem
There's a couple of tricks in Sass that community uses to simplify writing styles in the BEM fashion. Maybe you've been using `&` to avoid repeating yourself?
```
.my-block {
    &__my-element {
        // ...
    }

    &--my-modifier {
        // ...
    }
}
```
It compiles to:
```
.my-block__my-element {
    // ...
}

.my-block--my-modifier {
    // ...
}
```
It's fine, at least until you'd like your block's modifier to affect it's element:
```
.my-block {
    &--my-modifier {
        &__element {
            // ...
        }
    }
}
```
Above effects in:
```
.my-block--my-modifier__my-element {}
```
Not cool. In this case, to modify "my-block", you'd not only have to add a modifier class to itself, but also to all of it's elements you want to change in some way.

There's a simple trick we use to overcome this issue - assigning the block's root class to a variable and using like so:
```
.my-block {
    $block: &;

    &--my-modifier {
        #{$block}__element {
            // ...
        }
    }
}
```
Now on the output we have:
```
.my-block--my-modifier .my-block__my-element {
    // ...
}
```
Above method does the job, however this syntax is pretty imperative. Superbem gives you another, easy and declarative way.

## The solution
To achieve the same result, Superbem brings a syntax like this:
```
@include block(my-block) {
    @include modifier(my-modifier) {
        @include element(my-element) {
            // ...
        }
    }
}
```
The blocks and the element makes the "closures", when modifiers only lives inside the scope of they targets. Everywhere in the hierarchy you can access the current scope through the `$this` variable. Let's look:
```
@include block(my-block) {
    // $this called in here is "my-block"

    @include modifier(my-modifier) {
        // $this called in here is "my-block"

        @include element(my-element) {
            // $this called in here is "my-block__my-element"

            @include modifier(my-modifier) {
                // $this called in here is "my-block__my-element"
            }
        }
    }
}
```
Moreover, your modifiers inherit all of the features of the original blocks and elements. It means no more `class="my-block my-block--my-modifier"`. With Superbem `class="my-block--my-modifier"` is just enough.

## Using
At first install Superbem with NPM:
```
npm install superbem --save
```
Then import it in your Sass file:
```
@import 'path/to/node_modules/superbem/superbem';
```
That's it!

## Extras
In addition Superbem comes with three mixins supporting styling with CSS combinators:
```
@include block(my-block) {
    @include element(my-element) {
        // ...
    }

    @include element(my-second-element) {
        @include is-after(my-element) {
            // ...
        }

        @include is-preceded-by(my-element) {
            // ...
        }

        @include is-inside(my-element) {
            // ...
        }
    }
}
```
Which output is:
```
.my-block__my-element {
    // ...
}

.my-block__my-element + .my-block__my-second-element {
    // ...
}

.my-block__my-element ~ .my-block__my-second-element {
    // ...
}

.my-block__my-element > .my-block__my-second-element {
    // ...
}
```
## License
MIT
