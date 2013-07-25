#CSS

##HTML

* Can get away with not using long DOCTYPE tags - a relic from the XML days
  * `<!DOCTYPE html>`
  * `<html class = "webfonts">`

* `<head>` should not show up on the page, but the browser is forgiving
* Not a good idea to put `<script>` tags at the start of the page - JS takes time to load
  * Preferred way: put `<script>` tags at the very end of the page
  * And even then continue placing jQuery in `$(document).ready()`
