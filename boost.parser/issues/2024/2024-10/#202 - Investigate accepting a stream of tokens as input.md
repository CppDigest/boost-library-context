# #202 - Investigate accepting a stream of tokens as input [Open]

> Username: tzlaine  
> Created at: 2024-10-24 22:57:02 UTC  
> Updated at: 2024-12-10 02:23:14 UTC  
> Url: https://github.com/boostorg/parser/issues/202  

Not sure exactly how this should work -- should it take some abstract notion of a token, a lexrtl token, something I create, a CTRE-style thing, or what?  
  
But it definitely deserves at least some investigation.  Some parsers are very hard to write (or even ambiguous) without lexing first.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-11-25 23:50:01 UTC  
> Updated at: 2024-11-25 23:50:41 UTC  
> Url: https://github.com/boostorg/parser/issues/202#issuecomment-2499266080  

@jefftrull Thought you might want to track this one.  I decided to investigate what it would take to add, and it was really fun, so I just kept going.

---

## Comment 2

> Username: tzlaine  
> Created at: 2024-11-25 23:50:59 UTC  
> Url: https://github.com/boostorg/parser/issues/202#issuecomment-2499267131  

Won't make this release, btw.

---

## Comment 3

> Username: jefftrull  
> Created at: 2024-11-26 00:30:30 UTC  
> Url: https://github.com/boostorg/parser/issues/202#issuecomment-2499310372  

I love to see it thanks Zach!

---

## Comment 4

> Username: tzlaine  
> Created at: 2024-12-08 23:26:27 UTC  
> Url: https://github.com/boostorg/parser/issues/202#issuecomment-2526459174  

Ok, this stuff is pretty much ready to go.  I say "pretty much" only because I don't want to merge it quite yet, since I don't really have a big enough use case for it to really try it out.  
  
@jefftrull, that's where you come in. :)  Please give it a try and tell me what's under-documented, hard to use, etc.  You can find all the code on the ctre branch, and the pre-built updated docs can be found on the ctre_doc branch (just point your browser at `parser/doc/html/index.html`).

---

## Comment 5

> Username: jefftrull  
> Created at: 2024-12-09 05:29:19 UTC  
> Url: https://github.com/boostorg/parser/issues/202#issuecomment-2526968123  

Things are a little crazy right now but I'm very excited to try this. Thanks for taking on this big feature!

---

## Comment 6

> Username: tzlaine  
> Created at: 2024-12-10 02:23:13 UTC  
> Url: https://github.com/boostorg/parser/issues/202#issuecomment-2530067664  

No worries!  The branch isn't going anywhere.
