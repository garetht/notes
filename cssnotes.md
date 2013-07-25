#CSS

##HTML

* Can get away with not using long DOCTYPE tags - a relic from the XML days
  * `<!DOCTYPE html>`
  * `<html class = "webfonts">`

* `<head>` should not show up on the page, but the browser is forgiving

* Not a good idea to put `<script>` tags at the start of the page - JS takes time to load
    * Preferred way: put `<script>` tags at the very end of the page
    * And even then continue placing jQuery in `$(document).ready()`
    * jQuery cannot find tags that come after a jQuery statement

* `<meta charset="utf-8">`
 
The browser calls itself the user agent.

Two philosophies about fixing CSS: strip all the styles (reset stylesheet - Eric Meyer), or insist on consistency (normalized stylesheets).

##CSS3

The core of CSS is CSS 2.1. CSS3 has drop shadows and rounded corners, etc. There is a [CSS2.1 property table](http://www.w3.org/TR/CSS21/propidx.html).

## jQuery

Always use the uncompressed version of jQuery when writing code, so that error messages will make sense.

Get jQuery from Google, but fall back on a script hosted on your site if Google is down for some reason.

```html
<script src="//ajax.googleapis.com/ajax/libs/jquery/1.7.2/jquery.min.js"></script>
<script>window.jQuery || document.write('<script src="f/js/jquery.min.js"><\/script>')</script>
```

##Precedence

CSS a giant bag of redefining things.

`!important` tag 

Use as little precedence as necessary - only when there is an eception, or some state is different.

###Lowest precedence - declaration of tag
```css
body {
    background-color: green;
}

h1 {
    background-color: red;
}
```
###Class Declarations

Classes are useful because they can contain state.

```css
.hi{
    color: black;
}
```

###ID Declarations - high precedence
Generally, the more specific the selector, the higher the precedence.
```css
#hi{
    color: black;
}
```

To create something with equally high precedence, we would need something like
```css
html > .class > ... > #id{
}
```

Inheriting: takes over property of parent
gets rid of something without overriding it with something specific
```css
.hi{
   color:inherit;
}
```

##Selectors

###Children selector - all `img` children of `figure`, no matter how deeply nested.
```css
figure img{
    border:1px solid red;
}
```

###Direct children selector - only `img` tags nested at the first level of `figure`
```css
figure > img{
    border: 1px solid red;
}
```

###Multiple class selectors

We can usually select by an ID, but in toggling, it can be useful to ensure that the parent has a class set before setting the values on the child `#id`.

```css
.hi.goodbye > #id {
}
```

###Attribute selectors
Is this a good idea? Could use classes (`class = "submit-button"`) to get more specific instead of selecting all such input buttons.

We can combine the above selectors and chaining to select tags with multiple attributes:

```css
input[attribute="value"] {
}
.hi[attribute="value"] {
}
input[disabled] {
}
input[type="submit"][disabled] {
}
```

## Pseudoselectors

Rule: push as much styling as possible into stylesheets. Use jQuery to manage classes, etc.

```css
h1:hover{
}
```

## Example: The Cat Toggle

```html

<div class="cat">
    
    
    <div class="cat-on">
        <strong>Cat is on</strong>
        
    </div>
    
</div>

```

## The Box Model


