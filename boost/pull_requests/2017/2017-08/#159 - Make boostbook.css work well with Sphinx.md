# #159 Make boostbook.css work well with Sphinx [Merged]

> Username: twesterhout  
> Created at: 2017-08-04 20:13:50 UTC  
> Updated at: 2017-09-08 19:11:00 UTC  
> Merged at: 2017-09-08 19:09:24 UTC  
> Closed at: 2017-09-08 19:09:24 UTC  
> Url: https://github.com/boostorg/boost/pull/159  

* `body`'s `font-size` wasn't set. It should though to avoid inconsistencies in font sizes. Fixed.  
* [Sphinx](http://www.sphinx-doc.org/en/stable/index.html) uses [Pygments](http://pygments.org/) to highlight the text. Added support for it. The change shouldn't break any existing code.

---

## Comment 1

> Username: danieljames  
> Created_at: 2017-08-04 22:39:35 UTC  
> Url: https://github.com/boostorg/boost/pull/159#issuecomment-320368243  

I'm not opposed to this, but if I was using sphinx, I'd just want a separate css file. I'm sure there are existing ones that would work better, and you can add one to the super project if you want.  
  
Btw. is this for a library that has been accepted? I'll need to make sure that the documentation works with the website.

---

## Comment 2

> Username: twesterhout  
> Created_at: 2017-08-04 23:27:47 UTC  
> Updated_at: 2017-08-04 23:28:22 UTC  
> Url: https://github.com/boostorg/boost/pull/159#issuecomment-320373957  

No, it's for a new library ([StaticViews](https://github.com/BoostGSoC17/static-views)). You can have a look at the docs [here](https://boostgsoc17.github.io/static-views/doc/_build/html/reference.html) (it's a work in progress, but should be enough to get a feeling on the looks). I've used this [boost sphinx theme](https://github.com/pfultz2/sphinx-boost) created by Paul Fultz to make the page look Boosty.  
  
About creating a separate file -- yes, seemed like a good idea to me too, at first. But then, if I want to use the same fonts and colors etc., I'll have to copy paste parts of `boostbook.css`. A bit of a maintenance nightmare. And Sphinx is a nice tool, so I can imagine that other people might want to use it too.

---

## Comment 3

> Username: danieljames  
> Created_at: 2017-08-07 18:26:58 UTC  
> Url: https://github.com/boostorg/boost/pull/159#issuecomment-320742641  

OK, I've been trying this out, and there are some issues with setting the font size, as the stylesheet is a bit of a mess of different font sizing methods. For example, the table of contents uses `font-size: 80%`, so this makes it a bit too small. I realise this is the fault of the existing stylesheet, but it's hard to update now, there are multiple stylesheets built on top of this one. Looking at your documentation, I think something like `div.document, div.footer { font-size: 10pt; }` would work instead.  
  
The syntax highlighting css looks good.

---

## Comment 4

> Username: twesterhout  
> Created_at: 2017-08-08 13:01:38 UTC  
> Url: https://github.com/boostorg/boost/pull/159#issuecomment-320948929  

You're absolutely right. Setting `font-size` is by no means required to get my case to work. I still think it's a bug though. Like you say, some parts of the stylesheet use relative font sizes. The problem is relative to *what*? And, I believe, some fonts are relative to the default font in the browser. So now, if I have my default font set to, say, `30pt`, some parts will be huge while the rest of the text will still have font size 10 (or 9 or whatever). Here's an example of default size `= 30` in play:  
https://www.dropbox.com/s/vws7teil1acsc8d/screenshot_2017-08-08%2014%3A46%3A01.png?dl=0  
I'm a complete newby in web design, but I believe that setting the font-size of, for example, `body` sets a starting point. Other font sizes are then calculated relative to it and not to some default font size which users may set to obscure values. So I think it wise to either hardcode such a starting point or never ever use absolute font sizes and only rely on relative ones.

---

## Comment 5

> Username: danieljames  
> Created_at: 2017-08-08 13:20:30 UTC  
> Url: https://github.com/boostorg/boost/pull/159#issuecomment-320953726  

I understand all that, and agree that the font sizing should have been better handled, but I don't want to make such a change to a well established and used style sheet (it's not just this stylesheet, there's a lot of other stuff built on top of it). The approach I'd take would be to write a new stylesheet and let people use that if they wish, perhaps also along with some better markup from boostbook's xsl to go along with it.  
  
If I was going to set a font size on the body, I'd mimic the default web browser size, which I think is 16px, so that the documentation would remain more or less consistent, but I didn't think that does what you want.

---

## Comment 6

> Username: danieljames  
> Created_at: 2017-09-08 19:11:00 UTC  
> Url: https://github.com/boostorg/boost/pull/159#issuecomment-328189761  

I'm merging this, but in https://github.com/boostorg/boost/commit/a2e1dc44f5ecb3c08d0bc9e530dc526ba7e44e59 I changed the text sizing as described. Let me know if it doesn't work.

---
