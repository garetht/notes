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

Two philosophies about fixing CSS: strip all the styles (reset stylesheet - Eric Meyer), or insist on consistency (normalized stylesheets).

##CSS3

The core of CSS is CSS 2.1. CSS3 has drop shadows and rounded corners, etc. There is a [CSS2.1 property table](http://www.w3.org/TR/CSS21/propidx.html).

`min-width` and `max-width` limit the width of content - relevant to responsive design.
```css
width: 100%;
max-width: 500px;
```

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

## Example: The Lamp Toggle

###HTML
HTML for the lamp: there are two states, lamp on and lamp off. By default, the lamp is off. We use the class `is-on` to declare state.

```html
<div class="lamp is-on">
    <div class="lamp-on">
        <strong>Lamp is on</strong><br>
        <button>Turn lamp off</button>
    </div>
    <div class="lamp-off">
        <strong>Lamp is off</strong>
        <button>Turn lamp on</button>
    </div>
</div>
```

###CSS 
We use state in a binary way.

Generally, using visibility to hide elements is a bad idea, because the hidden elements still take up space on the page. Use the `display` property instead. 
* `display:none` will completely ignore the element. 
* `<div>`s by default are block elements - the inverse of `display:none` is `display:block`

```css
.lamp-on{
    display: none;
}
.lamp-off{
    display: block;
}
.is-on > .lamp-on{
    display: block;
}
.is-on > .lamp-off{
    display: none;
}
```

Using `is-on` and `is-off` instead of just one toggle can create messy states. Both `is-on` and `is-off` might accidentally be toggled at the same time, for example.

And we can refactor our code to reduce repetition:

```css
.lamp-on, .is-on > .lamp-off{
    display: none;
}
.lamp-off, .is-on > .lamp-on{
    display: block;
}
```

Attempting to `display:none` on a hover probably doesn't work because once the mouse hovers, the element disappears and the mouse no longer hvers on the element.

### Javascript
`event` has plenty of interesting stuff in it - what the target was, the position, timestamp, whether the control key was pressed, etc.

```javascript
$(document).ready(function(){
    $(".lamp button").on("click", function(event){
        event.preventDefault();
```
At this point there are two jQuery objects (`.lamp` and `button`), and jQuery automatically binds to the one that was clicked on.

Here is one way to change the state:
```javascript
        $(".lamp").toggleClass("is-on");
    });
});
```

But this is sloppy. There is the possbility that we may be swapping the wrong way because of user input. The button changes the state regardless of what the state actually is, and may not correspond to what the button says it does. Another way to change the state:
```javascript
        var lamp = $(".lamp");
        var state = $(this).closest("div").attr("class");
        
        if(state == "lamp-on"){
            lamp.removeClass("is-on");
        }
        else{
            lamp.addClass("is-on");
        }
    });
});
```

And a third method, using the optional Boolean argument of `toggleClass()`:
```javascript
        var lamp = $(".lamp")
        var buttonClicked = $(this).closest("div").attr("class");
        
        lamp.toggleClass("is-on", buttonClicked == "lamp-off")
    });
});
```

It is probably not a good idea, either, to bind a `click` event to an input submit button. Instead, we capture the submission of the form, which will also work when the form is submitted with a keypress.

```javascript
$('form').on("submit", function(event){
});
```

A [more complete listing of DOM events](http://en.wikipedia.org/wiki/DOM_events).

## The Box Model

Only applies to `display: block`.

We care about
* width/height
* border
* padding
* margin

If we do not declare a width and height for a `block` element, it will expand to the full width of the page by default. This changes when you resize the browser.

If we do not give it a `height`, it will take the minimum amount of height necessary. It is not that common to give things a height.

Do not use percentages with pixels (different units!)

Percentages: percent width of the parent container.

Fixed footers: make all parents 100%, then position an element at the bottom and then move it up a bit - possible, but messy.

###Padding
Padding is outside of the width, but the background color of the element will apply to it.

###Border
Like the padding, it is another level on the outside. 

Events are triggered from the border inwards, not at the margin.

###Margins
Given a margin of 50 pixels, no element can come within 50 pixels of it.

Margin collapse: two elements with margins are allowed to approach each other to the maximum distance of the two margins, rather than the sum of the two.

Negative margins:

What different numbers of arguments for margin specify:
```css
margin: top right bottom left
margin: top/bottom right/left
margin: 50px
margin-left: 50px
```

`auto` margins: if you want to center a `div` on a page, given a certain width, `margin auto` will make the left and right margins take up equal space such that the `div` is centered in its container. It will not alter the vertical space.
```css
margin: 50px auto;
```



##Other Display Types
