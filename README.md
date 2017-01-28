# start.iopen #

start.iopen is an open-source clone of [start.io](http://start.io), a startpage
for your favorite links.

It's designed to be **open-source** and therefore **self-hostable**, be so simply written in **vanilla JS** that it should run in
all modern browsers, and -most importantly- be essentially self-contained in
**one HTML file** and your sites in **one JSON file**. With some concessions,
it's also designed to work **locally** and to load even **despite internet failure**.


## Editing your sites ##

Your sites are stored in a JSON file with the following format:

```
{
  "Group 1": {
    "Site A": "http://sitea.com",
    "Site B": "http://siteb.com",
  },
  "Group 2": {
    "Site Manfred": "http://planmanfred.com"
  }
}
```

That simple.

## Preferences ##

Preferences are stored in localStorage with the prefix 'startiopen_' (e.g. 'startiopen_jsonPath').

  - jsonPath: the path to the JSON file
  - theme: the name of the theme to use (`themes` variable contains a list of those available)
  - newWindow: whether or not to make links open in a new window;
  - forceLayout: force the layout to be either "desktop", "mobile", or "auto"
  - autoFetchDays: how often to auto-fetch the sites; set to 0 to fetch on every load
  - jsonIsh: jsonPath really points to a JavaScript file that sets window.jsonData to the data
  - auth: HTTP Basic Auth string (base64 encoded) to use when making the AJAX requests

These can be set via the interface in-browser or by URL parameters, like so:

  ```index.html?theme=Precinct&jsonIsh=1&jsonPath=http%3A%2F%2Fmysite.com%2Ffavs.json&newWindow=false&forceLayout=mobile&autoFetchDays=3&auth=YXNkZjqe6WU%3D```

Remember to URL encode values as needed, mostly jsonPath and auth.


## Themes & "One File" ##

As stated, start.iopen is self-contained in one file. But there's also external
skins that are *entirely optional*. A theme is just two files inside the
'themes' directory:

  1. $THEME_NAME.css
  2. $THEME_NAME.templ

start.iopen uses [John Resig's Micro-Templating](http://ejohn.org/blog/javascript-micro-templating/)
for templating so it's fast and simple.


## Ideal usage ##

The setup to start.iopen involves just hosting your JSON file somewhere. That's
it.

From there you can either host index.html (+themes if desired), use the [Github pages](http://notbryant.github.io/start.iopen) hosted version, or store them
locally on your machine (which admittedly has some complications, noted below).


## Things lacking from start.io ##

start.iopen does not have feature parity with start.io nor does it intend to.

  - Colors: currently this is omitted to make the JSON more concise but may
    happen in the future.
  - Stats: things like "lastClicked/lastUpdated"; but really the goal is to
    create a quick, simple starting page, not an avenue to datamine onesself.
  - Updates: checking if a site has "updated" is better reserved to feed readers
    and utilities that can handle sites that require authentication (e.g. gmail).
  - Site Management: no more fancy GUI, "Trash", etc; a JSON file should be enough
  - Bookmarklet: no "quick adds" to the JSON file
  - Antiquated iPhone interface: just......yeah
  - no JS: start.iopen requires Javascript



********************************************************************************

# Advanced Usage #

## Local usage ##

Using start.iopen locally is indeed possible, but with one major hickup:
browsers are strict when it comes to CORS (Cross Origin Resource Sharing).
Even Ajax GET requests are cross-origin when being made from 'file://'.

When your JSON file is also local, the amount of hassle this provides depends on
what browser you use. For example, Firefox works flawlessly if index.html and
the JSON file are in the same directory, Chrome does not. But all browsers have
a problem if index.html is local and JSON is hosted.

For local themes, the only real solution is to paste the contents of the
theme(s) you want into `index.html` inside a `<script>` tag (like Default and Mobile do) as well as the css insode a `style`.

```
<script type="text/html" id="startiopen_templ_{{THEME_NAME}}">
  ... file contents ...
</script>
<style id="startiopen_resetStyle" disabled>
  ... reset.css if you need it ...
</style>
<style id="startiopen_style_{{THEME_NAME}}" disabled>
  ... css contents ...
</style>
```

The JSON file has two different solutions. If it's hosted online and you are
able, you can set the CORS headers and make sure the OPTIONS HTTP method is
supported on your web host. If you can't or don't want to, you can use the
jsonIsh preference (which is basically JSONP that sets 'window.jsonData').


### Writing your own theme ###

You can easily write your own theme. You can write an HTML template using your
JSON data as the variable `groups`, then just write whatever CSS you want to
style it. You can include any valid HTML inside your template, including
Javascript, so you can do things like `document.title = 'mah title';`.

Here are a few tips about writing your own theme:

  - Assign the id of the div with the 'group' class to the group name; this will
    allow you to view just that group and even link to just it.
    (```<div class="group" id="<%=groupName%>">```)
  - Avoid using hardcoded ids. Mostly just so it won't interfere with the above.
  - Make sure your CSS stay contained inside `<main>`, otherwise things like
    the menu and preferences will look weird.


And lastly, if you write a theme that you like, feel free to submit it to the
[Github repo](http://github.com/notbryant/start.iopen) or create a
[Gist](https://gist.github.com/) for it or just email me (address is in the
commit log).
