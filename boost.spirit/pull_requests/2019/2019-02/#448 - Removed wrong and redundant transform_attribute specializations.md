# #448 Removed wrong and redundant transform_attribute specializations [Closed]

> Username: Kojoley  
> Created at: 2019-02-02 20:05:27 UTC  
> Updated at: 2019-03-28 20:27:11 UTC  
> Closed at: 2019-02-03 02:27:19 UTC  
> Url: https://github.com/boostorg/spirit/pull/448  

The wrong specializations were allowing to assign a parse to a rule with  
incompatible attribute types.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2019-02-02 20:14:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/448#issuecomment-459995834  

Hm, I caught a bug in tests because rules started to synthesize attributes, I should not remove these specializations and will revert that part, but it is a nice finding.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2019-02-02 20:47:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/448#issuecomment-459997877  

Omg, they were synthesizing, but in `make_attribute` and then `transform_attribute` just did not let it go into parser. So... we need to fix this bug first.

---

## Comment 3

> Username: Xeverous  
> Created_at: 2019-02-02 21:38:58 UTC  
> Updated_at: 2019-02-02 22:39:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/448#issuecomment-460001318  

There definitely is some issue with make/transform attribute traits. A lot of my changes seem to break some tests. I'm not sure what these traits should do exactly by default.  
  
I also got an error in the test file you have changed, was wondering whether `char const*` as an attribute to `int_ >> ',' >> int_` makes any sense.

---

## Comment 4

> Username: Kojoley  
> Created_at: 2019-02-03 02:27:19 UTC  
> Url: https://github.com/boostorg/spirit/pull/448#issuecomment-460017197  

> I also got an error in the test file you have changed, was wondering whether `char const*` as an attribute to `int_ >> ',' >> int_` makes any sense.  
  
That's because you have repaired broken attribute synthesization in rule. I think it just should be removed (see #449).  
  
Closing this PR as for now.

---
