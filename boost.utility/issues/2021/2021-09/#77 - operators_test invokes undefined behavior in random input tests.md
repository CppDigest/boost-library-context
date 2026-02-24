# #77 - operators_test invokes undefined behavior in random input tests [Open]

> Username: Lastique  
> Created at: 2021-09-16 10:46:33 UTC  
> Updated at: 2021-09-26 09:17:10 UTC  
> Url: https://github.com/boostorg/utility/issues/77  

The [random input tests](https://github.com/boostorg/utility/blob/3f51807f24c9132d673abcaf02f288d9bf592825/test/operators_test.cpp#L619-L641) of operators_test.cpp invokes undefined behavior such as shifting integers for more than the left operand capacity or signed integer overflows in arithmetic tests. These issues are flagged by UBSAN.  
  
Currently, I have disabled these tests under UBSAN as a workaround to keep UBSAN otherwise enabled. However, the tests as they are are invalid. I'm not sure of the purpose of this series of tests and whether the random input is essential in some regard, so I decided to not remove the tests straight away. But the tests need to be corrected.  
  
CC: @d-frey

---

## Comment 1

> Username: d-frey  
> Created at: 2021-09-26 08:06:42 UTC  
> Updated at: 2021-09-26 08:12:06 UTC  
> Url: https://github.com/boostorg/utility/issues/77#issuecomment-927255132  

Those tests are 20 years old. Since they are stable and don't seem to cause problems on any compiler (despite triggering UB), I'd say we keep them. Disabling them for UBSAN seems fine to me, but I don't feel that any more action is needed, especially given that there is no real development for Boost.Operators now that we have the spaceship operator.

---

## Comment 2

> Username: Lastique  
> Created at: 2021-09-26 09:17:10 UTC  
> Url: https://github.com/boostorg/utility/issues/77#issuecomment-927266708  

This doesn't cause problems because we don't run the tests on hardware that e.g. triggers a hardware exception on overflow, and we don't use the results of the operators other than to test they are the same for `Wrapped` and the underlying type (i.e. that garbage equals garbage).  
  
If there isn't a point to use random input, and run the tests a 1000 times to boot, I would rather replace these tests with a single set of tests with predefined data. Or remove the tests completely, if this is covered elsewhere. Testing UB is just wrong, anyway.
