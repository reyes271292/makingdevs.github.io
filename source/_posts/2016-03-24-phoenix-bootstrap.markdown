---
layout: post
title: "Add complete bootstrap to Phoenix"
date: 2016-03-24 20:08:09 -0600
comments: true
author: Felipe Juárez Murillo
tags: phoenix, elixir, bootstrap
categories:
- elixir
- phoenix
---


It has been a while since I make a post and this is my first post in English so be gentle with me :P

Since a couple of months I been working in a language called [elixir](http://elixir-lang.org/) and with his web framework [phoenix](http://www.phoenixframework.org/) and I have had a lot of fun with these elements. But sometimes I been struggling with configurations that should be easy maybe I don't read that carefully or maybe I'm a knucklehead, but whatever the reasone is, I hope this configuration works for you and give you a little help of how configure your Javascripts third parties for your Phoenix application.

<!-- more -->

When you create a new application with phoenix you will notice (when you start the server) that actually `phoenix` have [bootstrap](http://getbootstrap.com/) but that is not true at all, if you want to add a `dropdown` or something more sophisticated like a `dialog` or a `carousel` you will find that there is no `javascript` and the only thing that you have is the `stylesheet` so in order to add the complete `bootstrap` you need a couple steps before.

Well let's get started with this thing:

In order to manage all the libraries that you need to work with it is recommended to install [bower](http://bower.io/). Actually `phoenix` in his [Static Assets](http://www.phoenixframework.org/docs/static-assets) page encourage you to do it.

So we are going to follow this path and add bootstrap with `bower` but first we are going to create the `bower.json` file for storing our dependencies:

```sh
bower init
```

Then we are going to create a file named `.bowerrc` with this file we are going to tell to `bower` where are going to need to put all the `javascripts` that we need it from now on. In this file we are going to put the next instruction:

```js
{
  "directory": "web/static/vendor"
}
```

Now is the time to install `bootstrap` and for that we need to run the following instruction in your shell:

``` sh
bower install -S bootstrap
```

Now that we have `bootstrap` if you check your `vendor` directory you will see that there is not only `bootstrap` it is also `jquery` (because is a dependency for `bootstrap`) if have not heard of `bower` before I recommend you to look for other proyects it will save you a lot of time and space in your repository.

Well at this moment, if you run your `phoenix.server` you will find a couple of errors, so lets fix that:

1. Let's remove all the `css` that `phoenix` ships with. For this open your `web/static/css/app.css` and remove the content of the file.
1.

