# #123 X3: Add another missing const&. [Merged]

> Username: mlang  
> Created at: 2015-06-18 14:03:24 UTC  
> Updated at: 2015-06-19 17:25:15 UTC  
> Merged at: 2015-06-19 01:06:33 UTC  
> Closed at: 2015-06-19 01:06:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/123  

A tiny change which results in 7kb less binary size and 17.8s to 17.2s compile  
time reduction (GCC).

---

## Comment 1

> Username: vtnerd  
> Created_at: 2015-06-18 17:11:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/123#issuecomment-113224014  

Should X3 change its constructors to the "[pass by value then move idiom](http://stackoverflow.com/questions/21035417/is-the-pass-by-value-and-then-move-construct-a-bad-idiom)"? The operator overloads would have to be updated too. However, this could [increase the size of the binary](https://web.archive.org/web/20140205194657/http://cpp-next.com/archive/2009/08/want-speed-pass-by-value/) in some situations - does anybody know how much of a difference moves could make for expression templates like this? I think there are only a few cases where dynamic memory is held anyway.

---

## Comment 2

> Username: djowel  
> Created_at: 2015-06-18 22:22:03 UTC  
> Updated_at: 2015-06-18 22:24:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/123#issuecomment-113304240  

I made it pass by value initially. It seems, as mlang reported, that doing o increases compile time and binary size. You can check it out by reverting some of mlang's previous commits. Maybe we can try pass value then move, but I am not sure how that would help. Yes, only a few use dynamic memory.

---

## Comment 3

> Username: mlang  
> Created_at: 2015-06-19 17:25:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/123#issuecomment-113581342  

I think the "pass by value and move" idiom is useful when the signature  
has several arguments which demand different ref-ness treatment.  If you  
just pass a single arg, you might as well pass it by const& to avoid copies  
and moves.  As mentioned in this thread, moving a spirit parser is  
basically just a copy anyway,  
since they dont contain anything movable (typically).

---
