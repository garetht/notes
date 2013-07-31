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

Two philosophies about fixing CSS: strip all the styles (reset stylesheet - Eric Meyer), or insist on consistency ([normalized stylesheets](http://necolas.github.io/normalize.css/)).

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

Use as little precedence as necessary - only when there is an exception, or some state is different.

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

###Example: precedence

In order of highest to lowest precedence, for example:

* `.special > #winner.class-1.class-2`
* `.special > #winner`
* `#winner`
* `p.class-1`
* `.class-1`
* `p`

```html
<p class="class-1">ONE</p>
<div class="special">
 <p class="class-1 class-2" id="winner">TWO</p>
</div>
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

Rule: push as much styling as possible into stylesheets. Use jQuery to manage classes, etc. You can use the cursor property to change what the cursor looks like on hover.

The `-webkit-transition` property allows transitions to happen on hovering. Other prefixes are `-moz` and `-msie`.

```css
h1:hover{
 cursor: pointer;
 -webkit-transition: all 2s;
}
```

For links, pick a color that stands out, brighter than what you'd expect.

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

Attempting to `display:none` on a hover probably doesn't work because once the mouse hovers, the element disappears and the mouse no longer hovers on the element.

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

###Block
Will expand to the full width - will not allow anything to be next to it horizontally, even if its size is constrained. If the width is set to 500px a scrollbar will appear when the browser window is smaller than 500px, but we can use the `max-width` property to allow it to be smaller than 500px.

###Float
Can float right or left. Will float to left or right of the parent container. Will take up the space it needs to float left, and everything around it will just wrap around it.

Floats do not take up space with regards to stacking.

When something is made floatable, it will take up the minimum amount of space, and other things wrap around it. But the border ignores the float, and it is possible to clear the floats so that things do not wrap around it: a element with a clear property will not wrap around a float.

```css
clear: both;
clear: left;
clear: right;
```
###The Clearfix

Most useful for having lists of things next to each other.

```css
ul{
 list-style: none;
 padding: 20px;
 }
 
li{
 float: left;
 width: 50px;
 height: 50px;
 background: red;
 border: 5px solid yellow;
}
```

```html
<ul>
 <li></li>
 <li></li>
 <li></li>
 <li></li>
 <li></li>
</ul>
```

It is possible to fix the border by adding a `<li></li>` and clearing it `float: none`.

The trick is pseudocontent, using pseudoselectors.

```css
h1:before{
 content: "++"
}
```
is equivalent to `<h1><span>++</span>Cat Click</h1>`. This gives us a new element on the page that doesn't really exist in the HTML but which we can style.

```css
ul:after{
 content: "";
 clear: both;
 display: block;
}
```
This works in Webkit, but in other browsers you have to declare `display:table`, which also works in Webkit. This is called the clearfix.

It is annoying to have to do this after every float, so we can give the `ul` a class of `group` and put the pseudocontent in `.group:after`.

Use classes to modify state!


###Inline
Follows the normal flow of the text

`<span>` by default is inline (`display:inline`). Cannot specify width in a `<span>`. Span elements take up no space. Can have padding, but this gets messy, so avoid. It may be useful to have inline links with padding, and the spaces between them will just show up.

There are annyoing spaces between inline elements, and we can't exercise much control over them.

Anchor tags are by default inline, but if we want to change their background color we can make them blocks and add padding.

###Inline blocks
A hybrid. Act as a block inside of themselves, allowing us to set padding, width, etc., but have to obey the general rules of being an inline element. Will take up the space, but will push down the next line. Inline stuff around this will be affected by inline blocks.


###CSS Trick: Sidebars
Given two `<div>`s, to make a sidebar with `float:left` and content on the right panel that does not wrap around the sidebar float, use this CSS code:

```css
#content{
 overflow: auto;
}
#sidebar{
 float:left
}
```

It is difficult to believe that this works. It appears that this trick works because setting `overflow:auto` prevents the text from wrapping so that the browser can add scrollbars in a rectangular region if necessary. See the effect created with `overflow:scroll`.

###Button with an image in it

Usually, we might put it in with an `img src`, but we really want to just have a button. It is bad to hide content in images.

```html
<button id="cat-button">CATA</button>
```

Here is a bad first solution:

```css
#cat-button{
 display: inline-block;
 width: 100px;
 height: 50px;
 text-indent: -9999px
 background: url("cats-button.png");
}
```

But this still leaves the text somewhere far away to be discovered in the future. Instead, learn what we used in the box model.

```css
#cat-button{
 display: inline-block;
 width: 100px;
 height: 50px;
 padding: 50px 0 0 0;
 height: 0;
 overflow: hidden;
 background: url("cats-button.png") no-repeat 0 0;
}
```

The padding is added to the top, so it pushes the text down, and changing the height to 0 ensures that it cannot be seen.

```css
#cat-button:hover{
 background: url("cats-button-hover.png");
}
```

But this is not the preferred way. Only when you hover over the button is a request sent to the server to load the image, and so may not appear immediately.

Use PNGs for alpha transparency, and because they are a more modern file format. JPEGs are good for photography.

The solution to this is to make a sprite, which combines two images into one on top of the other. We can then load the sprite in the usual way.

```css
#cat-button{
 display: inline-block;
 width: 100px;
 height: 50px;
 padding: 50px 0 0 0;
 height: 0;
 overflow: hidden;
 background: url("cats-button-sprite.png") no-repeat 0 0;
}
```

And when we want to display the second image, simply move the background upwards to reveal the bottom half of the image.

```css
#cat-button:hover{
 background-position: 0 -50px;
}
```

This gives us very fast loading, and also prevents an extra request to the server. To finish, drag the image into ImageOptim and squeeze all metadata out of it for the best possible loading times.

The sprite technique is not confined to buttons, and it enables us to leave the text on the page, and does not mask it from Google's web crawlers.

You can make anything look like anything else in CSS.

##Positioning

There are four different types of positioning.

###Static Positioning
Static positioning is *the default*.


###Relative Positioning
```css
.relative{
 position: relative;
 top: 30px;
}
```
The element will stay where it is, takes up the space that it normally would (and that is allowed for it), and allows us to use the properties top, left, right, and bottom. Usually, we use only top and left, or right and bottom. Using top/bottom or left/right will probably lead to a conflict.

This pushes the element around on the page - based on its current position - without affecting anything else, and while taking up the space it would under static positioning. The cursor is considered to be hovering over the element in its new position, rather than from the space it occupied under static positioning. This allows the element to move on top of other objects.

We can use webkit-transition with relative positioning to make objects bounce about the page.

```css
.relative{
 position: relative;
 bottom: -40px;
 right: 20px;
 background: blue;
}

.relative:hover{
 button: -40px;
}
```

Note also that while we can change the cursor, we cannot webkit-transition it.

####z-index
Given that we can move objects on top of each other, relative positioning also gives the z-index property, which determines the element's stack order on the page. By default, whatever element comes later in the page is of a higher rank. Relatively positioned objects are always on top of static ones. A z-index of -1 moves the element behind the page.

Z-index looks at elements with z-index and compares them. Things with declared defaults (except -1) always rank above those without declared z-indexes. Those without z-indexes are ranked based on the order in which they appear on the page.

###Absolute Positioning
```css
.absolute{
 background: orange;
 position: absolute;
 width: 30px;
 height: 30px;
}
```
Absolute positioning is the same as relative except it does not take up any space in the page as relatively positioned objects do (their static positions). It is rendered in the spot where it should have been rendered.

It is incorrect to say that absolutely positioned objects are rendered relative to the window. It is absolutely positioned relative to the closest ancestor of its that is not static. If there are no such ancestor, it picks the window.

For example, if we wrapped the absolutely positioned object in a `<div>` that has `position:relative`:
```css
.wrap{
 position: relative;
 margin: 100px;
 background: blue;
 width: 300px;
 height: 300px;
}
```

```html
<div class="wrap">
 <div class="absolute"></div>
</div>
```
the object will be positioned absolutely relative to this element.

If we set an absolute position that is off the page, and set `overflow:hidden`, the object will be moved off the page.

This is very useful, not so much for layouts as for cool stuff and making things move about the page.

###Fixed positioning
Fixed positioning positions something relative to the window and makes it stay there. This lets us make sticky navigation. It does not take up any space on the screen. It does not change when it is nested within another div (cf absolutely positioned stuff).

Use fixed positioning sparingly! If the browser window is small enough the fixed element may end up taking up all of the space.

When we make anything absolute or fixed (or floating), it takes the minimum amount of space necessary.

```css
.fixed{
 position: fixed;
 background: black;
}
```
A navbar cannot declare its own bottom margin because it is taken out of the normal document flow - it does not exist. Set width to 100%, top to 0, and bottom to 0.

###Final notes

Do not use positioning to dictate where each element will be - this will break, unless you try to put a million breakpoints in as well. That is why we use floats.

##Practical Example: Cat Tagger

```html
<figure class="photo is-tagged">
 <div class="tag"></div>
 <img src="cats.jpg" alt="cat">
</figure>
```

Images, by default, are inline block. Take up a block amount of space but come in inline content flow (line height, etc.) Best thing to do is to make them a block - this takes away any extra whitespace on the bottom, because it is no longer inline.

This stretches the width, and sets the height to be in proprtion.
```css
.photo{
 background: green;
 width: 300px;
}

.photo > img{
 display: block;
 height: auto;
 width: 100%;
}
```

We can use `-webkit-transition all 2s linear`, etc. to control the manner in which the transition is made.

### Making a Tag

This code positions a square bordered tag on top of the image.

```css
.tag {
 position: absolute;
 width: 90px;
 height: 90px;
 background: transparent;
 border: 5px solid blue;
}
```

But this does not follow the picture around (it is contained in a static `figure`). To fix this, add `position:relative` to one of its containers:

```css
.photo{
 position: relative
 
}
```

A jQuery-set CSS style will always win over any style set.

We can also make toggles (see some sectios above) to make a toggle that places a tag on the cat when the photo is clicked.



To position the tagging square in the right place, use the `pageX` and `pageY` attributes of the jQuery event. Avoid `clientX` and `clientY`.

```javascript
$(".photo").on("click", function(event){
 ...
 var x = event.pageX
 var y = event.pageY
 ...
})

```

We don't really want to store absolute pixel locations to allow for resizing now or in the future. We want to use percentages, which are relative to the parent container. Do this by subtracting `pageX` and `pageY` by the offset of the image's top left corner from the corner of the browser window.

```javascript
$(".photo").on("click", function(event){
 var photo = $(this);
 photo.addClass("is-tagged");
 
 var x = event.pageX - photo.offset().left;
 var y = event.pageY - photo.offset().top;
 
 var tag = photo.find("tag");
 tag.css({
  "left": x + "px";
  "right": y + "px";
 })
})

```

This code, however, makes the tag box's top left corner appear at whereever the mouse is clicked.

Do not manually subtract the size of the tag box, however, beause this will mess with the display when photos are resized. Instead, do more Javascript calculations. The final code:

```javascript
$(".photo").on("click", function(event){
 var photo = $(this);
 photo.addClass("is-tagged");
 
 var x = parseFloat((event.pageX - photo.offset().left) / photo.width() * 100);
 var y = parseFloat((event.pageY - photo.offset().top) / photo.height() * 100);
 
 var tag = photo.find("tag");
 tag.css({
  "left": x + "%";
  "right": y + "%";
 })
})

```

###Final Notes
Remember to think about the closest non-static parent when using `position:absolute`.

Do not keep searching for jQuery elements. Save a selector as a variable and use the variable.

We can also do more calculations to prevent tag boxes from going over the borders.


##Bubbling

When adding new elements (by appending, for example), a listener that is created before those items are created will not listen to the new events.

The best thing to do, when things are dynamically (or unpredictably) added to the DOM, is to pick something higher up in the hierarchy to bind to, or the most stable element. Instead of binding directly to `.grid > li`, we can bind to `<ul class="grid">` (`.grid`), and add a filter: `$(".grid").on("click", "> li", function(event){})` so that only the proper child elements respond.

The concept of bubbling in Javascript: whatever element you click on will get the click event, and it will travel up to the top of the page to check if there is anyone listening to it. If you `return false` on a certain listener, however, it will stop the propagation. `preventDefault` does not stop propagation.

##Building an Application

Two ways to start: with the database layer, or from the views up. 

Do not make all the models, then all the controllers, and all the views. Try an iterative approach. 

Start with some wireframes to have a guide of what to accomplish early on.
