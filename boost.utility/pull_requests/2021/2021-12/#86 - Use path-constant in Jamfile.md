# #86 Use path-constant in Jamfile [Merged]

> Username: alandefreitas  
> Created at: 2021-12-08 20:46:05 UTC  
> Updated at: 2021-12-09 00:19:58 UTC  
> Merged at: 2021-12-08 20:57:04 UTC  
> Closed at: 2021-12-08 20:57:04 UTC  
> Url: https://github.com/boostorg/utility/pull/86  

The previous jamfile was counting on the git layout instead of the boost release layout. We can replicate the error with `b2 libs/utility/doc` from `<boost root>`, which still tries to generate the doc in `<boost root>/libs/utility/doc/html` and `<boost root>/bin.v2/libs/utility/doc/html` still keeps temporary files, but it fails to find the links.  
  
According to Peter, we should use path-constant instead of hardcoding relative paths in the jamfile. The reason is relative paths are often but not always relative to the Jamfile location. This PR uses [BOOST_ROOT](https://github.com/boostorg/boost/blob/bd78d4d549b43fda7c49a8536f2ec73b34e9f811/Jamroot#L146) from Jamroot. `b2 libs/utility`/`b2 libs/utility/doc` work from `<boost root>` now (locally).  
  
What's funny is `b2 libs/core/doc` works with both layouts and boost.utility uses almost the same commands script.

---

## Comment 1

> Username: Lastique  
> Created_at: 2021-12-08 20:48:29 UTC  
> Url: https://github.com/boostorg/utility/pull/86#issuecomment-989183504  

Don't do unnecessary `glob`.

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2021-12-08 20:51:23 UTC  
> Url: https://github.com/boostorg/utility/pull/86#issuecomment-989185395  

An automated preview of the documentation is available at [https://86.utility.prtest.cppalliance.org/libs/utility/index.html](https://86.utility.prtest.cppalliance.org/libs/utility/index.html)

---

## Comment 3

> Username: alandefreitas  
> Created_at: 2021-12-08 20:55:05 UTC  
> Url: https://github.com/boostorg/utility/pull/86#issuecomment-989187679  

Oops... I was trying to ensure it looked exactly [like boost.core](https://github.com/boostorg/core/blob/6da7958281fcdbd8fa768e37686c62984c8af673/doc/Jamfile.v2#L13) and left that behind. The new version also worked locally.

---

## Comment 4

> Username: Lastique  
> Created_at: 2021-12-08 20:58:44 UTC  
> Url: https://github.com/boostorg/utility/pull/86#issuecomment-989190127  

This broke styling and links to images in my local build.

---

## Comment 5

> Username: alandefreitas  
> Created_at: 2021-12-08 21:00:20 UTC  
> Url: https://github.com/boostorg/utility/pull/86#issuecomment-989191191  

https://www.boost.org/doc/libs/develop/libs/utility/doc/html/index.html  
  
🤞

---

## Comment 6

> Username: Lastique  
> Created_at: 2021-12-08 21:00:38 UTC  
> Url: https://github.com/boostorg/utility/pull/86#issuecomment-989191389  

The problem is it generates links such as `<link rel="stylesheet" href="..//home/lastique/src/boost/doc/src/boostbook.css" type="text/css">` in html files. Sorry, I'm reverting this.

---

## Comment 7

> Username: alandefreitas  
> Created_at: 2021-12-08 21:02:25 UTC  
> Url: https://github.com/boostorg/utility/pull/86#issuecomment-989192601  

I'll look for a solution that works directly on the docker images then.

---

## Comment 8

> Username: Lastique  
> Created_at: 2021-12-08 21:05:33 UTC  
> Url: https://github.com/boostorg/utility/pull/86#issuecomment-989194978  

Other libraries seem to work fine with relative paths, so it seems they should work for Boost.Utility as well. At least, it isn't clear what's the difference between the system where the official docs are built and our local systems.

---

## Comment 9

> Username: alandefreitas  
> Created_at: 2021-12-08 21:12:56 UTC  
> Updated_at: 2021-12-08 21:14:16 UTC  
> Url: https://github.com/boostorg/utility/pull/86#issuecomment-989200102  

> Other libraries seem to work fine with relative paths, so it seems they should work for Boost.Utility as well.   
  
Exactly.   
  
Apart from the `glob`, there's almost no difference between boost.utility and [boost.core](https://github.com/alandefreitas/core/blob/develop/doc/Jamfile.v2).  
  
According to Peter, we should never use relative paths in a Jamfile. This implies that boost.core was just lucky. But it's not the only library where we see relative paths.  
  
And we already replicated the error by running it from the root path, so I don't think the docker image will help much, even though I'll keep trying.

---

## Comment 10

> Username: pdimov  
> Created_at: 2021-12-08 21:34:40 UTC  
> Url: https://github.com/boostorg/utility/pull/86#issuecomment-989214663  

I have no idea what's going on here; the PR says "use path-constant" but doesn't use `path-constant` but `BOOST_ROOT`.

---

## Comment 11

> Username: alandefreitas  
> Created_at: 2021-12-08 21:57:39 UTC  
> Url: https://github.com/boostorg/utility/pull/86#issuecomment-989256011  

> I have no idea what's going on here; the PR says "use path-constant" but doesn't use path-constant but BOOST_ROOT.  
 Delete branch  
  
I might have completely misunderstood what you said then. I'm so sorry. Isn't BOOST_ROOT a path-constant? I imagined if we *defined* the path-constant in our own Jamfile instead of just *using* a path-constant defined somewhere else, then the new solution would be as hard-coded as before.  
  
I probably misunderstood you then. It didn't work anyway.

---

## Comment 12

> Username: pdimov  
> Created_at: 2021-12-08 22:07:45 UTC  
> Url: https://github.com/boostorg/utility/pull/86#issuecomment-989266122  

Use the `path-constant` b2 rule to define a variable for the path you want, like it's done for example in this Jamfile: https://github.com/boostorg/boostdep/blob/037705e21b297b28c77663d7cc1cd28cb7a240c5/test/Jamfile#L8-L9  
  
Since your Jamfile is in `libs/utility/doc`, you can for instance use `path-constant INCLUDE : ../include ;` and then use `$(INCLUDE)/boost/utility/value_init.hpp` to reference `value_init.hpp`. Or you can use `path-constant INCLUDE : ../include/boost/utility ;` and then `$(INCLUDE)/value_init.hpp`. And so on.  
  
`path-constant` takes a path relative to the current Jamfile, converts it into absolute path, and sets the variable to that.

---

## Comment 13

> Username: Lastique  
> Created_at: 2021-12-08 22:32:41 UTC  
> Url: https://github.com/boostorg/utility/pull/86#issuecomment-989281264  

I've pushed 9d1284ef6d8f51ac15b6f9de44cadc11a38bdbf4 that works for me locally. I think, this is what Peter meant.  
  
The important difference from this PR is that `boost.root` parameter needs to stay relative to the Jamfile.

---

## Comment 14

> Username: alandefreitas  
> Created_at: 2021-12-08 22:56:50 UTC  
> Url: https://github.com/boostorg/utility/pull/86#issuecomment-989294010  

So...  
  
```  
path-constant VALUE : ../../.. ;  
$(VALUE)/boost/utility/base_from_member.hpp  
```  
  
is different from   
  
```  
../../../boost/utility/base_from_member.hpp  
```  
  
? 😓

---

## Comment 15

> Username: pdimov  
> Created_at: 2021-12-08 23:03:43 UTC  
> Url: https://github.com/boostorg/utility/pull/86#issuecomment-989297662  

Sometimes, yes. Most parts of b2 (all of the built-in rules, for instance) interpret a relative path as relative to the current Jamfile. However, other parts of it interpret it relative to the current directory. (E.g. the path.glob rule.)  
  
In addition, if you pass the relative path as-is to an external program, the external program will again interpret it against the current directory.  
  
It's safest to use `path-constant` variables because these are unambiguously interpreted to be relative to the current Jamfile.  
  
It is what it is.

---

## Comment 16

> Username: Lastique  
> Created_at: 2021-12-09 00:07:31 UTC  
> Url: https://github.com/boostorg/utility/pull/86#issuecomment-989328345  

Looks like https://github.com/boostorg/utility/commit/9d1284ef6d8f51ac15b6f9de44cadc11a38bdbf4 did the trick. Can we update the website?

---

## Comment 17

> Username: glenfe  
> Created_at: 2021-12-09 00:08:45 UTC  
> Url: https://github.com/boostorg/utility/pull/86#issuecomment-989328928  

Yes, @alandefreitas please create pull request to https://github.com/boostorg/website/tree/master/doc/libs   for 1_78_0/libs/utility with the correct generated content

---

## Comment 18

> Username: alandefreitas  
> Created_at: 2021-12-09 00:10:11 UTC  
> Url: https://github.com/boostorg/utility/pull/86#issuecomment-989329564  

Oh... `website/doc/libs/` is meant to override content? I always wondered why there were only a few libraries in there. 😆

---

## Comment 19

> Username: alandefreitas  
> Created_at: 2021-12-09 00:12:50 UTC  
> Url: https://github.com/boostorg/utility/pull/86#issuecomment-989330965  

It should have the same layout as `https://www.boost.org/doc/libs/1_78_0/libs/utility/doc/html/`, right?  
  
That is, `https://www.boost.org/doc/libs/1_78_0/libs/utility/doc/html/index.html` should go into `website/doc/libs/1_78_0/libs/utility/doc/html/index.html`, right?

---

## Comment 20

> Username: glenfe  
> Created_at: 2021-12-09 00:19:58 UTC  
> Url: https://github.com/boostorg/utility/pull/86#issuecomment-989334147  

Yes, see the other directories for examples

---
