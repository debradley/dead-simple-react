# Dead Simple React

* [Project Page](https://debradley.github.io/react-dead-simple/)

## Overview

This project is intended as a learning spike and has two primary purposes:

1. To create a very simple React example (no webpack)
2. To publish the project to Github Pages

## Steps

Because this is a learning project, I'm fully documenting the steps taken to create it.

```sh
# load ssh-agent and github credentials
eval "$(ssh-agent -s)"
ssh-add -K ~/.ssh/github_rsa

# setup repo
mkdir react-dead-simple
cd react-dead-simple
git init
git remote add origin git@github.com:debradley/react-dead-simple.git
hub create
touch README.md
git add -A .
git commit -m "Initial commit"
git push -u origin master

# create index.html

# create styles.css

git add index.html styles.css
git commit -m "Initial commit"

# complete README
git add README.md
git commit -m "First pass"

git push
```

To make the page available on github pages it was necessary to go into the repo settings on the github site and, in the GitHub Pages section, set source to master branch and save.

## Lessons Learned

### Commit Before Push

You must commit at least one file to git before you can do an initial push, otherwise you'll get an ambiguous error: `error: src refspec master does not match any`

### Cloudflare Versus Unpkg

To skip webpack, as I'm doing here, you can't use `react` and `react-dom` off of a CDN like cloudflare, because those versions of the libraries are exported as CommonJS modules, a format browsers can't make use of without help from webpack (or requirejs, browserify, or other tools). Trying to use a bare CommonJS module in a browser will fail with the error: `Uncaught ReferenceError: require is not defined`. Instead, you need to use [unpkg](https://unpkg.com/) to get [UMD](https://github.com/umdjs/umd) versions of the libraries.

### HTML Attributes

[HTML attributes don't need to be quoted unless they contain certain characters](http://w3c.github.io/html/syntax.html#elements-attributes) (spaces, quotes, normal or grave apostrophes, =, <, or >). To minimize file size, it would be best to only use quotes when needed. But for developer quality of life, it's probably easiest to always quote attributes. Use of a minifier in the build process would give the best of both worlds.

### Loading JavaScript

1. Scripts default to `type="text/javascript"` and so type does not need to be specified unless it's something other than plain javascript.

1. If you're developing by loading an HTML file locally in your browser then an inline script can't be externalized to a relative path because you'll hit a cross-origin resource sharing (CORS) limitation. If you want the script to be in a separate file for the convenience of editing it separately, you must use something like `webpack-dev-server` or `parcel` to do development.

1. Scripts should be loaded in `<head>` with `async` or `defer` tags. References:

* [Where should I put \<script\> tags in HTML markup?
](https://stackoverflow.com/a/24070373/5070828)
* [Declaratively loading JavaScript](https://www.fullstackreact.com/articles/Declaratively_loading_JS_libraries/index.html)

Because script tags are loaded synchronously, DOM parsing and construction is blocked until the script is loaded. These scripts are then also executed before the rest of the page has fully loaded.

`defer` ensures that the scripts load in the order they are declared and that they don't run until the page has finished loading. Because [`defer`](https://caniuse.com/#feat=script-defer) and [`async`](https://caniuse.com/#feat=script-async) have similar browser support, using `defer` is probably always the better choice.

### Loading CSS

[Loading your CSS after your JavaScript may provide some performance gain (and won't hurt)](https://stackoverflow.com/a/9272730/5070828).

### Loading Webfonts

Webfonts can loaded on one of two ways, via CSS import:

```css
@import url("https://fonts.googleapis.com/css?family=Nobile:400,600,700");
```

Or via a link in the HTML head:

```html
<link href="https://fonts.googleapis.com/css?family=Nobile:400,500,700" rel=stylesheet>
```

[Using the link is usually the right choice](https://stackoverflow.com/questions/12316501/including-google-web-fonts-link-or-import/12380004#12380004).

TODO: Better understand the differences and when link isn't the right choice.

### Meta Tags

There's no need to include `meta http-equiv="X-UA-Compatible"` except possibly in the case where you're [optimizing for old versions of IE](https://stackoverflow.com/questions/6771258/what-does-meta-http-equiv-x-ua-compatible-content-ie-edge-do).

## TODO

Questions this spike raised that I haven't yet answered:

* Can a git repo's settings be changed to allow GitHub Pages publishing from the command line using `hub` or something else?
* Is there any need to do anything more complicated than using `defer` (as shown in [Declaratively loading JavaScript](https://www.fullstackreact.com/articles/Declaratively_loading_JS_libraries/index.html)?) if you don't need to support older browsers?
* Does using webpack change the answer to the question above?
