<!-- .slide: data-background="images/css-modules-logo.png" data-background-size="contain" -->
# 



## About me
* Veit Lehmann
* Front-end Developer at LOVOO
* [github.com/levito](https://github.com/levito)



# <small>What are</small><br/>Modules?



## Modules
* Composable, reusable pieces of code
* Do one thing well
* Loose coupling
* Easy to maintain, replace and remove



## JS Modules
* CommonJS in Node.js
* Core Node.js modules and NPM<br/>
  **&rarr;&nbsp; New age of JS Development**



## Before JS Modules
* Everything global by default
* 3rd party scripts could break everything
* AMD/UMD for modular JS in the browser



### Browserify/Webpack
* CommonJS Modules for the browser
* ES6 modules via Babel<br/>
  **&rarr;&nbsp; Boilerplate-free modules for the browser!**



## CSS before Modules
* Everything global
* How to deal with it?<br/>
  **&rarr;&nbsp; Naming Conventions**



# [<small>What happens with</small><br/>Sloppy CSS?](http://localhost:8080)


## WTF!?
_Two CSS properties walk into a bar._<br/>
_A barstool in a completely different bar falls over._<br/>
<small>- @thomasfuchs</small>
<!-- .slide: data-background="images/giphy-frustrated.gif" data-background-size="cover" style="color:#fff"-->



# CSS Naming Conventions



## Deep Nesting
* CSS Selectors mirror the DOM structure
* Popular in the 2000s


### Deep Nesting Pros
* Isolation
* Clean, semantic markup


### Deep Nesting Cons
* No CSS reusability
* CSS bloat (append-only code)
* Lots of overwriting CSS
* Specifity wars
* Visual inconsistencies



## BEM
* Block__Element--Modifier
* Add more classes to the markup
* Reuse lots of CSS rules
* Short selector chains
* Naming convention to avoid name clashes
* e. g. `button button--danger`, <br/>
  `button__icon button__icon--after`


### BEM Pros
* Easy to write and reason about in markup
* No CSS bloat but reuse
* Naming convention makes it safe


### BEM Cons
* Lots of discipline needed to write CSS
* Fine-tuning is hard
* Verbose and annoying to write
* Markup bloat
* Corner cases hard to name



## Atomic CSS
* Combine single purpose classes
* Aim: No need to write new CSS once implemented
* e. g. `padding-small color-red text-center`


### Atomic CSS Pros
* Easy to write and reason about in markup
* Even more CSS reuse than BEM
* Fine-tuning is easier


### Atomic CSS Cons
* Rebrushing is hard
* Even more markup bloat than BEM
* Not component-oriented but huge pile of globals
* Feels like inline-styling
* Fine-tuning has its limits
* Pseudo selectors/attributes hard to style
* Responsive styling is painful



## Inline styling in JS (JSS)
* Came up in the React community
* No external stylesheets
* Dynamic style attributes instead


### JSS Pros
* Everything is in the component
* No class name clashes
* No specifity wars
* Code sharing between JS and Style
* Dynamic styling via JS opens possibilities


### JSS Cons
* No pseudo elements &rarr; markup needed
* No @media queries and pseudo selectors<br/>
  &rarr;&nbsp; reimplement with JS
* Responsive styling is really painful
* Styles in objects &rarr; undefined order of properties<br/>
  &rarr;&nbsp; `margin` might overwrite `margin-top` defined later
* Awkward syntax
* Browsers are not optimized for that



## State-of-the-Art today
* Combine BEM with *trumps* for corner-case adjustments
* Trumps are like Atomic CSS, but `!important` to always win
* Visual-semantic naming:
    - not `button--checkout`, but `button--primary`
    - not `text--lightgray`, but `text--weak`
* Thoughtful use of Pre-/Postprocessing


### *Side note:* Preprocessors
* Variables, mixins, extends, functions and nesting<br/>
  &rarr;&nbsp; DRY CSS authoring
* **But: Markup is unaffected**



## What if we could
* Ensure *non-conflicting class names*?
* Keep source *markup clean* like with deep-nesting?
* Keep CSS *small and maintainable*?
* Make CSS pieces easily *removable/replacable*?


### The missing piece
* Rewrite the *application* of styles in the *markup*
* i. e. rewrite `class="..."`<br/>



# <small>Enter</small><br/>CSS Modules
<!-- .slide: data-background="images/giphy-smart.gif" data-background-size="cover" style="color:#fff"-->



## CSS Modules
* Put CSS *into the component*
* *Import* it into the view
* Class names remain *local* to the component
* @media queries, pseudo elements/classes, animations etc. still work


# [<small>How to use</small><br/>CSS Modules](http://localhost:8080)



## *:local* by default

```css
/* my-component.css */
.foo { color: red; }
```

```js
// my-component.js
import styles from 'my-component.css';

export `<div class="${styles.foo}">
            Styles of .foo are applied
        </div>`;
```

### Output
```css
.s0m3_rnd-Ha5h { color: red; }
```

```html
<div class="s0m3_rnd-Ha5h">
    Styles of .foo are applied
</div>
```


## *:local* by default
* Prevents naming clashes like BEM
* *... but without its problems*
    - No verbose naming
    - No accidental clashes
    - No head-scratching in corner cases



## *:global* styles
```css
:global .foo { color: red; }
.bar { font-weight: bold; }
.foo :global .baz { text-decoration: underline; }
```

```js
export `<div class="${styles.foo}">
            <span class="${styles.bar}">
                Lorem <span class="${styles.baz}">ipsum</span>
            </span>
        </div>`;
```

### Output
```css
.foo { color: red; }
.DFGDFG { font-weight: bold; }
.DFGDFG .baz { text-decoration: underline; }
```

```html
<div class="foo">
    <span class="DFGDFG">
        Lorem <span class="baz">ipsum</span>
    </span>
</div>
```


## *:global* styles
* When you need to break out
    - like content from a RTE
    - 3rd party code like date-pickers etc.



## Composition

```css
.foo { color: green; }
.baz {
    composes: foo;
    composes: bar from '../some-shared.css';
    font-weight: bold;
}
```

```js
export `<div class="${styles.baz}">
            Styles of .foo, .bar and .baz are applied
        </div>`;
```

### Output
```css
.ASDASD { color: green; }
.QWEQWE { font-weight: bold; }
```

```html
<div class="ASDASD YXCYXC QWEQWE">
    Styles of .foo, .bar and .baz are applied
</div>
```


## Composition
* Code reads like Sass `@extend`s
* *... but without its problems*
* Output like Atomic CSS
    - No massive selector chains
    - No attribute duplication
    - But more classes on elements



## Values/Variables
```css
/* colors.css */
@value blue: #0c77f8;
@value red: #d00020;
@value green: #aaf200;
```

```css
@value colors: "./colors.css";
@value blue, red, green from colors;

.button { color: blue; }
```

### Output
```
.BTN987XY { color: #0c77f8; }
```



## What have we gained?
* *Clean source* markup (like deep nesting)
* *Non-conflicting* class names (like BEM)
* *Slim CSS* via composition (like Atomic CSS)
* *DRY Code* with variables etc. (like preprocessors)



# Usage advices


## Preprocessors?
* Possible, but not needed
    - you don’t want to nest
    - variables built-in
    - better alternatives to mixins and extends


## Alternative
* More syntax needed? Add PostCSS plugins
* Awesome companion: CSSnext
* But remember: KISS


## Good fit
* SPAs, expecially React and Angular
* Complex, modular UIs
* Sustaining projects
* Bigger frontend teams


## Maybe not
* CMS projects
* Simple campaign pages



## Can I use it?
* Webpack: *css-loader* in modules mode
* Browserify: *css-modulesify*
* JSPM: *jspm-loader-css-modules*
* Node.js: *css-modules-require-hook*
* Other: *postcss-modules* (generates mapping json)



## Useful resources
* [glenmaddern.com/articles/css-modules](http://glenmaddern.com/articles/css-modules)
* [github.com/css-modules/css-modules](https://github.com/css-modules/css-modules)
* [medium.com/seek-ui-engineering/the-end-of-global-css-90d2a4a06284](https://medium.com/seek-ui-engineering/the-end-of-global-css-90d2a4a06284)
* [medium.com/@webPapaya/css-architectures-in-2016-fab5d14c9b6e](https://medium.com/@webPapaya/css-architectures-in-2016-fab5d14c9b6e)



# <small>That’s it</small><br/>Thanks!
