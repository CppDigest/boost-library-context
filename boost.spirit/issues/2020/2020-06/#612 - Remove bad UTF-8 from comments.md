# #612 - Remove bad UTF-8 from comments [Open]

> Username: jefftrull  
> Created at: 2020-06-10 22:36:21 UTC  
> Updated at: 2025-05-10 00:39:42 UTC  
> Url: https://github.com/boostorg/spirit/issues/612  

The LLVM tool `clangd`, a language server based on Clang, was triggering assertions on some of the comments inside `iso8859_1.hpp`, specifically starting at [this line](https://github.com/boostorg/spirit/blob/0abca50754c266a4191a1cade4477481e42fee19/include/boost/spirit/home/support/char_encoding/iso8859_1.hpp#L206). Evidently some parts are not valid UTF-8, and whether this can be handled by a compiler is implementation-dependent.  
  
A workaround has gone into `clangd`, but since the value is unprintable anyway (and to avoid future compatibility issues) maybe it would be worth scrubbing the comments.  
  
A more detailed description of the issue is [on the LLVM Phabricator](https://reviews.llvm.org/D81530#2085725).

---

## Comment 1

> Username: Kojoley  
> Created at: 2020-06-10 23:27:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/612#issuecomment-642318562  

> Evidently some parts are not valid UTF-8  
  
It is been the second time within a month or so when someone assumes UTF-8 as the encoding of source files. While it is understandable, it is not correct.  
  
> whether this can be handled by a compiler is implementation-dependent.  
  
Not really. The mapping is implementation-defined, the handling must be the same as `\u` of that character in a string literal: http://www.eel.is/c++draft/lex.phases#1.1  
  
I have an idea that we need to leave this as-is since it makes tools better at handling encoding issues.

---

## Comment 2

> Username: jefftrull  
> Created at: 2020-06-10 23:35:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/612#issuecomment-642320671  

The LLVM people understand this more than I do :) I'll let them reply, if they want.

---

## Comment 3

> Username: sam-mccall  
> Created at: 2020-06-11 00:20:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/612#issuecomment-642333176  

Hi, clang/clangd/llvm person here. Agreed that tools guessing about encodings are liable to get them wrong, and calling this "bad UTF-8" may be putting the blame at the wrong level.  
  
However, this is still a source file that most compilers and other tools (even the [github code viewer](https://github.com/boostorg/spirit/blob/0abca50754c266a4191a1cade4477481e42fee19/include/boost/spirit/home/support/char_encoding/iso8859_1.hpp#L206) and [your own website](https://www.boost.org/doc/libs/1_73_0/boost/spirit/home/support/char_encoding/iso8859_1.hpp)!) don't parse correctly, and it's not because they're all dumb and need to get better at handling encoding issues - it's because there's no defined way for your header to signal that it's using ISO-8859-1. So there's technically nothing invalid about this header (since the standard doesn't speak to that), but it doesn't/can't work with real tools and there are principled ways to do so, why not fix it?  
  
FWIW, clang only supports UTF-8 input, and gcc supports some options that don't work well here:  
- the `-finput-encoding` flag. Well, this affects the whole TU, and you're providing a header, so this only works if you want to constrain all downstream projects to using ISO-8859-1 as well  
- the current locale if it implies an encoding. Again, no way to restrict this to the header, do you package different versions of this header for different locales?  
- by default, parse as utf-8  
What do you think compilers/tools should be doing instead?  
  
If you care about making this work, the reasonably-portable options I'm aware of:  
 - use ASCII only - obviously this will work everywhere  
 - use UTF-8 with BOM - this is unambiguous, clang and gcc will parse as UTF-8 regardless of locale. BOMs are ugly though and simpler tools might choke.  
 - use UTF-8 without BOM: I guess GCC will get columns wrong in some locales. Clang and clang-based tools will be fine. Anything else written in the last 15 years by someone who knew what they were doing will be fine.  
  
FWIW our workaround is to log something like "iso8859_1.hpp has an unknown encoding" and letting find-refs etc jump to the wrong place. I care quite a lot about this stuff (I'm rambling about encoding late at night for some reason) and don't think this is something we can reasonably try to solve on our side. "makes tools better at handling encoding issues" seems pretty implausible, but best of luck to you either way.

---

## Comment 4

> Username: djowel  
> Created at: 2020-06-11 07:24:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/612#issuecomment-642464226  

In light of this, I think it makes sense to just use UTF-8. It seems there are no tools capable in handling encoding.

---

## Comment 5

> Username: KubaO  
> Created at: 2020-12-29 07:04:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/612#issuecomment-751973354  

>  It seems there are no tools capable in handling encoding.  
  
Even if the tools *were* capable, how would you expect to pass that knowledge to them? That one file has a non-standard encoding? It'd be extra work for everyone, and for what reason?

---

## Comment 6

> Username: saki7  
> Created at: 2025-05-10 00:39:41 UTC  
> Url: https://github.com/boostorg/spirit/issues/612#issuecomment-2868152401  

I'm not sure if we still need to fix this in 2025.
