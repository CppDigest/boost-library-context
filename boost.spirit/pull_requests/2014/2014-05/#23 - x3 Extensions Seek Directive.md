# #23 x3 Extensions Seek Directive [Merged]

> Username: vtnerd  
> Created at: 2014-05-03 04:04:26 UTC  
> Updated at: 2014-07-20 22:08:09 UTC  
> Merged at: 2014-05-03 22:27:18 UTC  
> Closed at: 2014-05-03 22:27:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/23  

This is a port of the v2 seek directive to x3. The directive is in an extensions subdirectory that will have a duplicate layout to the parent directory.

---

## Comment 1

> Username: djowel  
> Created_at: 2014-05-03 06:00:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/23#issuecomment-42097341  

do we need separate directories? would it be too cluttered if we just place them all under x3/extensions?

---

## Comment 2

> Username: vtnerd  
> Created_at: 2014-05-03 15:17:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/23#issuecomment-42107475  

There aren't many features in the v2 repository, so the subdirectory structure is likely overkill. I pushed changes for a flattened directory. I think it could be re-organized later if it became too cluttered - x3 hasn't been released yet anyway.

---

## Comment 3

> Username: jamboree  
> Created_at: 2014-05-04 04:05:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/23#discussion_r12259590  

There's no test.hpp in the directory, maybe  
`#include "../test.hpp"`

---

## Comment 4

> Username: vtnerd  
> Created_at: 2014-05-04 17:21:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/23#discussion_r12262755  

The Jamfile is including test/x3/ when building, so the compiler picks up test.hpp from that directory.  
  
``` bash  
../../../../b2 --ignore-site-config spirit_v3/qi/x3_extensions  
Performing configuration checks  
  
    - symlinks supported       : yes (cached)  
...patience...  
...patience...  
...found 3891 targets...  
...updating 4 targets...  
gcc.compile.c++ ../../../../bin.v2/libs/spirit/test/x3/x3_seek-p3.test/gcc-4.7.3/debug/extensions/seek.o  
gcc.link ../../../../bin.v2/libs/spirit/test/x3/x3_seek-p3.test/gcc-4.7.3/debug/x3_seek-p3  
testing.capture-output ../../../../bin.v2/libs/spirit/test/x3/x3_seek-p3.test/gcc-4.7.3/debug/x3_seek-p3.run  
**passed** ../../../../bin.v2/libs/spirit/test/x3/x3_seek-p3.test/gcc-4.7.3/debug/x3_seek-p3.test  
...updated 4 targets...  
  
```  
  
The name for the test-suite might need some tweaking though.

---

## Comment 5

> Username: jamboree  
> Created_at: 2014-05-05 03:33:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/23#discussion_r12266666  

It seems accidental to me, if that's intentional, we should have `<test.hpp>` instead of `"test.hpp"`.

---

## Comment 6

> Username: vtnerd  
> Created_at: 2014-05-06 12:23:07 UTC  
> Url: https://github.com/boostorg/spirit/pull/23#discussion_r12321274  

It wasn't accidental, but it may have been incorrect. Is it standard practice in boost or spirit to use `<test.hpp>` in this situation? This doesn't seem like a library header to me, but it is referencing a file like it is a library (assumes the top-level directory is included by compiler). I guess I will make another pull request for this, and see what happens.

---

## Comment 7

> Username: K-ballo  
> Created_at: 2014-05-08 01:30:25 UTC  
> Url: https://github.com/boostorg/spirit/pull/23#discussion_r12412380  

This looks convoluted. Why isn't this a traditional `for`, with the `return false` happening outside of it?

---

## Comment 8

> Username: vtnerd  
> Created_at: 2014-05-08 01:47:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/23#discussion_r12412731  

I thought the same thing when I first ported this. If the loop conditional checks for `current != last`, then an expression like `x3::seek[x3::eoi]` will fail with an empty string input. I don't know when that case (`x3::seek[x3::eoi]`) would be useful, it seems kind of odd to use `x3::seek` like that. I figured it was best to keep existing behavior since it did seem correct in that scenario. Tongari wrote the original version, so maybe he knows of a use case.

---

## Comment 9

> Username: jamboree  
> Created_at: 2014-05-08 01:49:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/23#discussion_r12412781  

Because you can't use `current != last` as the condition, some parser may success on `current == last` (i.e. `eoi`).

---

## Comment 10

> Username: jamboree  
> Created_at: 2014-05-08 01:59:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/23#discussion_r12412914  

ok, some use case:  
  
```  
comment = "//" >> seek[eol | eoi]  
```

---

## Comment 11

> Username: K-ballo  
> Created_at: 2014-05-08 02:02:06 UTC  
> Url: https://github.com/boostorg/spirit/pull/23#discussion_r12412959  

Makes sense. This guarantees an `"intentionally complex"` comment with the previous explanation, or to be refactored into a regular loop with an additional call to `subject.parse` after it (possibly with a comment as well).

---

## Comment 12

> Username: vtnerd  
> Created_at: 2014-05-08 12:15:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/23#discussion_r12425355  

@jamboree I didn't think about it very much; thats an extremely obvious use case in hindsight.  
@K-ballo I'm thinking of going with the post loop subject check. It will require copy-and-pasting a few lines, but I think it will be easier to read. The loop termination will at least be more standard, and less concerning to anyone looking at the code.

---

## Comment 13

> Username: jamboree  
> Created_at: 2014-05-08 13:12:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/23#discussion_r12427259  

how about this?  
  
```  
Iterator it(first);  
for ( ; !subject.parse(it, last, context, attr); ++it)  
{  
    if (it == last)  
        return false;  
}  
first = it;  
return true;  
```

---

## Comment 14

> Username: K-ballo  
> Created_at: 2014-05-08 13:29:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/23#discussion_r12428094  

@vtnerd I too think the easier to read version is the one with a post-loop check. With that, one can immediately know at a glance that if you get to the check then the input has been consumed already (`current == last`). I would still add a comment explaining why is it needed, like `// some parser may success on current == last (i.e. eoi).`

---
