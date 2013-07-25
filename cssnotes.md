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
 
The browser calls itself the user agent.

Two philosophies about fixing CSS: strip all the styles (reset stylesheet - Eric Meyer), or insist on consistency (normalized stylesheets).

##Precedence

CSS a giant bag of redefining things.

`!important` tag 

Use as little precedence as necessary - only when there is an eception, or some state is different.

Lowest precedence - declaration of tag
```css
    body {
        background-color: green;
    }
    
    h1 {
        background-color: red;
    }
```
Class Declarations
```css
    .hi{
        color: black;
    }
```
##Selectors



