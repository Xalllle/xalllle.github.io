+++
title = "Docs"
+++

# Configuration

d3c3nt has a number of custom variables you can set in the `[extra]`
section of your `config.toml` file to personalize the theme for your
site.

## Navbar

To customize the navbar, add a `d3_nav` property. The variable should be
an array of objects, which each have a `name` and `path` value. `name`
will be used as the link's text, and `path` will be used as the `href`
value.

### Example
```toml
d3_nav = [
  { name = "HOME", path = "/" },
  { name = "POSTS", path = "/posts/" }
]
```
### Output
```html
<nav>
  <a href="/">HOME</a>
  <a href="/posts/">POSTS</a>
</nav>
```

## Favicon

To customize the favicon, set the `d3_icon` property. The variable
should be a string path to an image in the static directory.

### Example
```toml
d3_icon = "/icon.png"
```
### Output
```html
<link rel="icon" href="/icon.png">
```

