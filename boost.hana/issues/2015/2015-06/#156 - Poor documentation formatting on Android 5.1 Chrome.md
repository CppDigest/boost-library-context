# #156 - Poor documentation formatting on Android 5.1 Chrome [Closed]

> Username: jbytheway  
> Created at: 2015-06-23 12:16:03 UTC  
> Updated at: 2015-12-07 03:16:57 UTC  
> Closed at: 2015-12-04 23:48:11 UTC  
> Url: https://github.com/boostorg/hana/issues/156  

The reference documentation has some odd properties when viewed on my Nexus 5 (Android 5.1.1 Chrome).  When entering the "Methods" section of any concept's documentation page the font size gets much bigger and the text no longer wraps to the browser witdth.  You can see a similar effect on the emulated Nexus 7 here:  
  
http://mobt.me/BwmI  
  
except there the font is getting smaller rather than larger (which looks less odd).  I presume that the two font sizes are being chosen differently - one by pixel size and one by physical size.  
  
I guess this is probably a Doxygen bug, not hana's fault, but I felt I ought to mention it (perhaps you could pass along the bug report to the Doxygen folks as appropriate).

---

## Comment 1

> Username: ldionne  
> Created at: 2015-12-04 19:02:50 UTC  
> Url: https://github.com/boostorg/hana/issues/156#issuecomment-162053665  

Note to self: the above link is broken; use [this one](http://mobt.me/Hcyo) instead and look for the **Associated functions** section instead of the (now renamed) **Methods** section.  
  
I am tempted to think that this is a Doxygen bug; I'll try to confirm this.

---

## Comment 2

> Username: ldionne  
> Created at: 2015-12-04 20:19:20 UTC  
> Url: https://github.com/boostorg/hana/issues/156#issuecomment-162071453  

It seems to be a mix of Doxygen not creating pages that can be resized properly and MathJax not breaking equations on multiple lines. I think I fixed the second issue locally, but I think I have no control over the first issue. I'll close this, assuming it's not too common for people to browse the documentation on small devices, and otherwise assuming the documentation is still readable (although perhaps a bit clumsy). If the issue is serious enough to be worth spending more time on it, I'll reopen.

---

## Comment 3

> Username: jbytheway  
> Created at: 2015-12-06 02:09:43 UTC  
> Url: https://github.com/boostorg/hana/issues/156#issuecomment-162263852  

If you've gone far enough to verify that it's a Doxygen problem perhaps you could report it upstream?

---

## Comment 4

> Username: ldionne  
> Created at: 2015-12-07 03:16:57 UTC  
> Url: https://github.com/boostorg/hana/issues/156#issuecomment-162398995  

> If you've gone far enough to verify that it's a Doxygen problem perhaps you could report it upstream?  
  
I tried, but I haven't been able to get a minimal non-working example that I could report upstream. I also get different results when looking at the documentation from  
1. my computer with a small window size  
2. an emulator (like the link above)  
3. my iPhone  
  
It just seems like Doxygen is not very good at making the documentation browsable on small devices, but perhaps it's just because of some custom setup in Hana's documentation too.
