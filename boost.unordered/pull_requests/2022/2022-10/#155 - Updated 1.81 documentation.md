# #155 Updated 1.81 documentation [Merged]

> Username: joaquintides  
> Created at: 2022-10-31 18:43:16 UTC  
> Updated at: 2022-11-07 16:11:44 UTC  
> Merged at: 2022-11-07 16:11:15 UTC  
> Closed at: 2022-11-07 16:11:15 UTC  
> Url: https://github.com/boostorg/unordered/pull/155  

Hi,  
  
This is, as far as I can see, a 100% complete update of Boost.Unordered docs for 1.81 including `boost::unordered_flat_set` and `boost::unordered_flat_map`. We've got plenty of time to polish this, so, I encourage you to download, build and thoroughly review --I know it's a drag, but users will thank us. In particular, I recommend that you try to read it from the beginning to the end so as to assess whether the new parts dovetail well with previous material.  
  
Some observations and open questions:  
  
* I've tried to keep the style simple and fairly entry-level, which is how the original documentation felt to me.  
* Both sets of benchmarks (for fca and foa) are now kept in the same repo (https://github.com/joaquintides/boost_unordered_benchmarks) under different branches, and references to this from docs have been updated. Could it be a good idea to transfer this repo to boostorg? Now it's in my personal space.  
* Docs say flat containers require C++11 (we've managed to make it work with rather old C++11 compilers), but we can still raise that bar docwise to C++14.  
* I haven't added for foa an analogous section to that explaning fca structure because I don't think the latter belongs in the docs anyway, given we have more extensive articles we can point to. Your opinion?  
* I've taken the liberty to add Peter and myself to the list of (C) authors at the end.  
  
So, please take some quality time to review this and give fedback. Thank you!

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-10-31 18:51:06 UTC  
> Url: https://github.com/boostorg/unordered/pull/155#issuecomment-1297524719  

An automated preview of the documentation is available at [https://155.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://155.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 2

> Username: pdimov  
> Created_at: 2022-10-31 18:53:25 UTC  
> Url: https://github.com/boostorg/unordered/pull/155#issuecomment-1297527135  

> * Could it be a good idea to transfer this repo to boostorg? Now it's in my personal space.  
  
We can do that. You'll need to transfer the repo to me, and I'll transfer it to boostorg. But it might be better not to, because if it's under boostorg GHA will get throttled.

---

## Comment 3

> Username: pdimov  
> Created_at: 2022-10-31 18:55:07 UTC  
> Url: https://github.com/boostorg/unordered/pull/155#issuecomment-1297528884  

> * I haven't added for foa an analogous section to that explaning fca structure because I don't think the latter belongs in the docs anyway, given we have more extensive articles we can point to. Your opinion?  
  
Unfortunately, I think that the right thing to do here is to have such a section. Boost docs, traditionally, have been kept self-contained so as to enable offline reading from the release tarball, without requiring internet access.

---

## Comment 4

> Username: joaquintides  
> Created_at: 2022-11-01 09:22:28 UTC  
> Url: https://github.com/boostorg/unordered/pull/155#issuecomment-1298255020  

* Transfer of [boost_unordered_benchmarks](https://github.com/joaquintides/boost_unordered_benchmarks) to `boostorg`: GHA runs on Sam's dedicated servers, so I understand we won't have any problem with throttling?  
* Section on foa data structure: OK, I'll write the full-fledged external article (around next week) and then we can paste here an abridged version.  
* Your Slack comment about a typo in `operator==` reference ("behavior is undefined if the two containers don’t have equivalent equality predicates"). Re-reading it, I'm not sure what problem you're referring to. Is it about mentioning that `Key::operator==` must be a refinement of `Pred` as explained [here](https://eel.is/c++draft/unord.req#general-237)?

---

## Comment 5

> Username: pdimov  
> Created_at: 2022-11-01 12:31:09 UTC  
> Url: https://github.com/boostorg/unordered/pull/155#issuecomment-1298441894  

That's a misunderstanding; I meant this part: `Notes:;;`, not the text. Note the semicolons instead of colons.  
  
Re transfer, feel free to transfer the repo to me, and I will transfer it into boostorg.

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2022-11-01 16:36:05 UTC  
> Url: https://github.com/boostorg/unordered/pull/155#issuecomment-1298807117  

An automated preview of the documentation is available at [https://155.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://155.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-11-04 12:26:04 UTC  
> Url: https://github.com/boostorg/unordered/pull/155#issuecomment-1303420097  

An automated preview of the documentation is available at [https://155.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://155.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 8

> Username: joaquintides  
> Created_at: 2022-11-07 16:11:43 UTC  
> Url: https://github.com/boostorg/unordered/pull/155#issuecomment-1305843580  

Merged. Will use the same branch later to add the final missing bit (description of foa data structure).

---
