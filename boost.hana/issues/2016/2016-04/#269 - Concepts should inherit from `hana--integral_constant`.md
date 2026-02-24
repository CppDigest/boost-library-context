# #269 - Concepts should inherit from `hana::integral_constant` [Closed]

> Username: ldionne  
> Created at: 2016-04-20 01:24:31 UTC  
> Updated at: 2016-11-18 02:44:57 UTC  
> Closed at: 2016-11-18 02:44:57 UTC  
> Url: https://github.com/boostorg/hana/issues/269  

Right now, concepts only provide a nested `::value` static member, which is not idiomatic. Instead, they should inherit from `hana::integral_constant`, but that might raise some circular dependencies issues.  
  
Another take on this is that on the contrary, we don't want the concepts to inherit from an integral constant class, since we'll eventually move to C++17 concepts, and the migration will be easier if we only provide a `::value` member.

---

## Comment 1

> Username: ricejasonf  
> Created at: 2016-04-20 15:23:47 UTC  
> Url: https://github.com/boostorg/hana/issues/269#issuecomment-212476069  

I recently did something like `using type = hana::bool_c<value>;` in a concept so I could use it in `hana::trait<Concept>`.

---

## Comment 2

> Username: ldionne  
> Created at: 2016-04-20 15:36:00 UTC  
> Url: https://github.com/boostorg/hana/issues/269#issuecomment-212480737  

Ok, I'll implement this. But not in 1.0, because I'm freezing master until Boost 1.61 is out.

---

## Comment 3

> Username: ldionne  
> Created at: 2016-04-20 15:36:15 UTC  
> Url: https://github.com/boostorg/hana/issues/269#issuecomment-212480825  

I really want to avoid doing something stupid that I can't undo easily for that version.

---

## Comment 4

> Username: pfultz2  
> Created at: 2016-04-21 18:38:59 UTC  
> Url: https://github.com/boostorg/hana/issues/269#issuecomment-213057973  

Having the concepts be integral constants would help them play nicer with the rest of hana, if the user would want to, say for example, use them in a predicate to filter.
