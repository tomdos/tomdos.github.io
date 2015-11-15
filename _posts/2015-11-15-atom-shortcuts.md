---
layout: post
title: "Atom shortcuts"
date: 2015-11-15
published: true
tags: Atom
---

I started to use Atom editor last year and it became one of my favorite editor for coding. Unfortunately there are still plenty of things missing and some might have been done more user friendly. One of them is by my opinion the shortcut setting.

You can quite easily change or add new one in `Settings->Keybindings`. Problem is when your new shortcut is in conflict with another. Unfortunately Atom has something like default selectors which are still valid despite your new value. Therefore it is not enough to just add new shortcut but sometimes you have to disable the former one.

The following example shows new shortcut on mac platform. I wanted to use cmd-l for a quick jump to certain line number. My selector is `.platform-darwin`. It says that following settings are valid only on mac. The line bellow contains my new shortcut for this platform `cmd-l` and new keybinding `go-to-line:toggle`. 

The second part is selector `atom-text-editor`. The reason I modified it is because `cmd-l` had been already used. I have to use `unset!` here to disable its functionality.

{% highlight CoffeeScript %}
'.platform-darwin': 
    'cmd-l': 'go-to-line:toggle'
    
'atom-text-editor': 
    'cmd-l': 'unset!'
{% endhighlight %}

**References:**

* [Atom editor](https://atom.io/)
* [Atom keymaps in depth](https://atom.io/docs/latest/behind-atom-keymaps-in-depth)

