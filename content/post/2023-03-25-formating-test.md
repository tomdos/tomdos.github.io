---
author: "Tomas Dosoudil"
title: "Transfer Jekyll blog to Hugo"
date: "2023-03-25"
description: "Movig blog from Jekyll to Hugo"
tags: [
    "hugo",
    "devise"
]
---

I have recently decided to change my blog from Jekyll to Hugo. Although I don't have 
much content here I always like an idea of beying able to write a post.

This page is using [Devise](https://github.com/austingebauer/devise) theme. All 
configuration went pretty smooth except one thing. All my posts were missing 
headers. After a couple of tries I found out that all posts needs to be in 
a folder called `post` in the `content` folder. 

Images are supposed to be store in the `static` folder where is my 
home image as well `static/me.jpg`.


#### Useful links
Here are couple of usefule links I used during configuration of Hugo.

Syntax highlights:
https://gohugo.io/content-management/syntax-highlighting/

Snippets gallery:
https://xyproto.github.io/splash/docs/all.html


#### My config

This is how the current block is configured in `config.toml` 

```toml
baseURL = "https://tomdos.github.io"
title = "Tomas Dosoudil"
languageCode = "en-us"
theme = "devise"
#relativeURLs = true
enableEmoji = true
enableRobotsTXT = true
copyright = "&copy; Copyright 2023, Tomas Dosoudil"

[markup]
  [markup.highlight]
    style = 'dracula'

# Main menu items
[menu]
  [[menu.main]]
    identifier = "about"
    name = "About"
    title = "About"
    url = "/about/"
    weight = -90
  [[menu.main]]
    identifier = "posts"
    name = "Posts"
    title = "Posts"
    url = "/post/"
    weight = -100

# Configuration Features
[params]
  description = "Tomas Dosoudil's blog"
  header_title = "Tomas Dosoudil"
  header_subtitle = "A software engineer enthusiast"
  home_image = "me.jpg"
  recent_posts = 100  
  mainSections = ["post"]
  [params.style]        
    backgroundColor = "#f8f9fa"
    homeImageBorderColor = "#ffffff"
    fontFamilyBase = "Helvetica Neue"
    fontColor = "#212529"
  [[params.social]]
    fa_icon = "fab fa-github fa-1x" 
    href = "https://github.com/tomdos"
  [[params.social]]
    fa_icon = "fab fa-linkedin-in fa-1x"
    href = "https://www.linkedin.com/in/tom%C3%A1%C5%A1-dosoudil-49453658/"
```

