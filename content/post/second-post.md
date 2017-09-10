+++
tags = [
    "go",
    "golang",
    "templates",
    "themes",
    "development",
]
categories = [
    "Sample",
    "Personal Development",
]
image = "/img/contact-bg.jpg" #optional image - "/img/about-bg.jpg" is the default
description = "This is the description"
title = "Gitbhu markdoen"
draft = false
comments = true
date = 2017-09-10
+++

# (GitHub-Flavored) Markdown Editor

Basic useful feature list:

 * Ctrl+S / Cmd+S to save the file
 * Ctrl+Shift+S / Cmd+Shift+S to choose to save as Markdown or HTML
 * Drag and drop a file into here to load it
 * File contents are saved in the URL so you can share files


I'm no good at writing sample / filler text, so go write something yourself.

Look, a list!

 * foo
 * bar
 * baz

And here's some code! :+1:

```javascript
$(function(){
  $('div').html('I am a div.');
});
```

This is [on GitHub](https://github.com/jbt/markdown-editor) so let me know if I've b0rked it somewhere.


Props to Mr. Doob and his [code editor](http://mrdoob.com/projects/code-editor/), from which
the inspiration to this, and some handy implementation hints, came.

### Stuff used to make this:

 * [markdown-it](https://github.com/markdown-it/markdown-it) for Markdown parsing
 * [CodeMirror](http://codemirror.net/) for the awesome syntax-highlighted editor
 * [highlight.js](http://softwaremaniacs.org/soft/highlight/en/) for syntax highlighting in output code blocks
 * [js-deflate](https://github.com/dankogai/js-deflate) for gzipping of data to make it fit in URLs
 
 $ find public -type f -name '*.html' | xargs ls -l
-rw-r--r--  1 quoha  staff  149 Sep 29 22:44 public/index.html
-rw-r--r--  1 quoha  staff  125 Sep 29 22:44 public/post/first/index.html
-rw-r--r--  1 quoha  staff    0 Sep 29 22:44 public/post/index.html
-rw-r--r--  1 quoha  staff  128 Sep 29 22:44 public/post/second/index.html

$ cat public/index.html
<!DOCTYPE html>
<html>
<body>

    <h1><a href="/post/second/">second</a></h1>

    <h1><a href="/post/first/">first</a></h1>

</body>
</html>

$
```

### Create a Post Listing

We have the posts displaying on the home page and on their own page. We also have a file public/post/index.html that is empty. Let's make it show a list of all posts (not just the first ten).

We need to decide which template to update. This will be a listing, so it should be a list template. Let's take a quick look and see which list templates are available.

```
$ find themes/zafta -name list.html | xargs ls -l
-rw-r--r--  1 quoha  staff  0 Sep 29 17:31 themes/zafta/layouts/_default/list.html
```

As with the single post, we have to decide to update _default/list.html or create post/list.html. We still don't have multiple content types, so let's stay consistent and update the default list template.

## Creating Top Level Pages

Let's add an "about" page and display it at the top level (as opposed to a sub-level like we did with posts).

The default in Hugo is to use the directory structure of the content/ directory to guide the location of the generated html in the public/ directory. Let's verify that by creating an "about" page at the top level:

```
$ vi content/about.md
+++
title = "about"
description = "about this site"
date = "2014-09-27"
slug = "about time"
+++

## about us

i'm speechless
:wq
```

Generate the web site and verify the results.

```
$ find public -name '*.html' | xargs ls -l
-rw-rw-r--  1 mdhender  staff   334 Sep 27 15:08 public/about-time/index.html
-rw-rw-r--  1 mdhender  staff   527 Sep 27 15:08 public/index.html
-rw-rw-r--  1 mdhender  staff   358 Sep 27 15:08 public/post/first-post/index.html
-rw-rw-r--  1 mdhender  staff     0 Sep 27 15:08 public/post/index.html
-rw-rw-r--  1 mdhender  staff   342 Sep 27 15:08 public/post/second-post/index.html
```

Notice that the page wasn't created at the top level. It was created in a sub-directory named 'about-time/'. That name came from our slug. Hugo will use the slug to name the generated content. It's a reasonable default, by the way, but we can learn a few things by fighting it for this file.

One other thing. Take a look at the home page.

```
$ cat public/index.html
<!DOCTYPE html>
<html>
<body>
    <h1><a href="http://localhost:1313/post/theme/">creating a new theme</a></h1>
    <h1><a href="http://localhost:1313/about-time/">about</a></h1>
    <h1><a href="http://localhost:1313/post/second-post/">second</a></h1>
    <h1><a href="http://localhost:1313/post/first-post/">first</a></h1>
<script>document.write('<script src="http://'
        + (location.host || 'localhost').split(':')[0]
		+ ':1313/livereload.js?mindelay=10"></'
        + 'script>')</script></body>
</html>
```

Notice that the "about" link is listed with the posts? That's not desirable, so let's change that first.

```
$ vi themes/zafta/layouts/index.html
<!DOCTYPE html>
<html>
<body>
  <h1>posts</h1>
  {{ range first 10 .Data.Pages }}
    {{ if eq .Type "post"}}
      <h2><a href="{{ .Permalink }}">{{ .Title }}</a></h2>
    {{ end }}
  {{ end }}

  <h1>pages</h1>
  {{ range .Data.Pages }}
    {{ if eq .Type "page" }}
      <h2><a href="{{ .Permalink }}">{{ .Title }}</a></h2>
    {{ end }}
  {{ end }}
</body>
</html>
:wq
```

Generate the web site and verify the results. The home page has two sections, posts and pages, and each section has the right set of headings and links in it.

But, that about page still renders to about-time/index.html.

```
$ find public -name '*.html' | xargs ls -l
-rw-rw-r--  1 mdhender  staff    334 Sep 27 15:33 public/about-time/index.html
-rw-rw-r--  1 mdhender  staff    645 Sep 27 15:33 public/index.html
-rw-rw-r--  1 mdhender  staff    358 Sep 27 15:33 public/post/first-post/index.html
-rw-rw-r--  1 mdhender  staff      0 Sep 27 15:33 public/post/index.html
-rw-rw-r--  1 mdhender  staff    342 Sep 27 15:33 public/post/second-post/index.html
```

Knowing that hugo is using the slug to generate the file name, the simplest solution is to change the slug. Let's do it the hard way and change the permalink in the configuration file.

```
$ vi config.toml
[permalinks]
	page = "/:title/"
	about = "/:filename/"
```

Generate the web site and verify that this didn't work. Hugo lets "slug" or "URL" override the permalinks setting in the configuration file. Go ahead and comment out the slug in content/about.md, then generate the web site to get it to be created in the right place.

## Sharing Templates

If you've been following along, you probably noticed that posts have titles in the browser and the home page doesn't. That's because we didn't put the title in the home page's template (layouts/index.html). That's an easy thing to do, but let's look at a different option.

We can put the common bits into a shared template that's stored in the themes/zafta/layouts/partials/ directory.

### Create the Header and Footer Partials

In Hugo, a partial is a sugar-coated template. Normally a template reference has a path specified. Partials are different. Hugo searches for them along a TODO defined search path. This makes it easier for end-users to override the theme's presentation.

```
$ vi themes/zafta/layouts/partials/header.html
<!DOCTYPE html>
<html>
<head>
	<title>{{ .Title }}</title>
</head>
<body>
:wq

$ vi themes/zafta/layouts/partials/footer.html
</body>
</html>
:wq
```

### Update the Home Page Template to Use the Partials

The most noticeable difference between a template call and a partials call is the lack of path:

```
{{ template "theme/partials/header.html" . }}
```
versus
```
{{ partial "header.html" . }}
```
Both pass in the context.

Let's change the home page template to use these new partials.

```
$ vi themes/zafta/layouts/index.html
{{ partial "header.html" . }}

  <h1>posts</h1>
  {{ range first 10 .Data.Pages }}
    {{ if eq .Type "post"}}
      <h2><a href="{{ .Permalink }}">{{ .Title }}</a></h2>
    {{ end }}
  {{ end }}

  <h1>pages</h1>
  {{ range .Data.Pages }}
    {{ if or (eq .Type "page") (eq .Type "about") }}
      <h2><a href="{{ .Permalink }}">{{ .Type }} - {{ .Title }} - {{ .RelPermalink }}</a></h2>
    {{ end }}
  {{ end }}

{{ partial "footer.html" . }}
:wq
```

Generate the web site and verify the results. The title on the home page is now "your title here", which comes from the "title" variable in the config.toml file.

### Update the Default Single Template to Use the Partials

```
$ vi themes/zafta/layouts/_default/single.html
{{ partial "header.html" . }}

  <h1>{{ .Title }}</h1>
  {{ .Content }}

{{ partial "footer.html" . }}
:wq
```

Generate the web site and verify the results. The title on the posts and the about page should both reflect the value in the markdown file.

## Add “Date Published” to Posts

It's common to have posts display the date that they were written or published, so let's add that. The front matter of our posts has a variable named "date." It's usually the date the content was created, but let's pretend that's the value we want to display.

### Add “Date Published” to the Template

We'll start by updating the template used to render the posts. The template code will look like:

```
{{ .Date.Format "Mon, Jan 2, 2006" }}
```

Posts use the default single template, so we'll change that file.

```
$ vi themes/zafta/layouts/_default/single.html
{{ partial "header.html" . }}

  <h1>{{ .Title }}</h1>
  <h2>{{ .Date.Format "Mon, Jan 2, 2006" }}</h2>
  {{ .Content }}

{{ partial "footer.html" . }}
:wq
```

Generate the web site and verify the results. The posts now have the date displayed in them. There's a problem, though. The "about" page also has the date displayed.

As usual, there are a couple of ways to make the date display only on posts. We could do an "if" statement like we did on the home page. Another way would be to create a separate template for posts.

The "if" solution works for sites that have just a couple of content types. It aligns with the principle of "code for today," too.

Let's assume, though, that we've made our site so complex that we feel we have to create a new template type. In Hugo-speak, we're going to create a section template.

Let's restore the default single template before we forget.

```
$ mkdir themes/zafta/layouts/post
$ vi themes/zafta/layouts/_default/single.html
{{ partial "header.html" . }}

  <h1>{{ .Title }}</h1>
  {{ .Content }}

{{ partial "footer.html" . }}
:wq
```

Now we'll update the post's version of the single template. If you remember Hugo's rules, the template engine will use this version over the default.

```
$ vi themes/zafta/layouts/post/single.html
{{ partial "header.html" . }}

  <h1>{{ .Title }}</h1>
  <h2>{{ .Date.Format "Mon, Jan 2, 2006" }}</h2>
  {{ .Content }}

{{ partial "footer.html" . }}
:wq

```

Note that we removed the date logic from the default template and put it in the post template. Generate the web site and verify the results. Posts have dates and the about page doesn't.

### Don't Repeat Yourself

DRY is a good design goal and Hugo does a great job supporting it. Part of the art of a good template is knowing when to add a new template and when to update an existing one. While you're figuring that out, accept that you'll be doing some refactoring. Hugo makes that easy and fast, so it's okay to delay splitting up a template.

