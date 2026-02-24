# #14 Add support for Boost Serialization of dynamic_bitset<> [Merged]

> Username: sehe  
> Created at: 2015-06-24 00:21:01 UTC  
> Updated at: 2017-01-09 04:35:27 UTC  
> Merged at: 2017-01-09 04:35:27 UTC  
> Closed at: 2017-01-09 04:35:27 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/14  

Serialization using the public interface isn't optimal as `to_block_range()`/`from_block_range()` require copying of `m_bits` (and subsequent `resize()`).  
  
I added a generic implementation to Boost Dynamic Bitset. The changes merge cleanly against develop or master (1_58_0).  
## Changes  
  
Implementation added with   
- Minimal intrusiveness, only a nested friend (`class serialization_impl;`) has been forward declared to "key-hole" the required friend access through  
- This class, as well as the actual ADL hook for Boost Serialization are implemented in a separate header (`dynamic_bitset/serialization.hpp`, similar to other boost libraries with serialization support).  
- This means that zero dependencies on Boost Serialization stuff exists unless `boost/dynamic_bitset/serialization.hpp` is actually included  
- Zero copy is achieved (leveraging `std::vector<Block>`'s builtin support in Boost Serialization)  
- Supports archives that use name-value pairs (like xml_oarchive)  
## Tests  
  
The second commit in the pull request adds tests for this feature. I'm not sure how to add the `dyn_bitset_unit_tests5.cpp` to the Jamfile. I suppose something else must be done to ensure linking to Boost System and Boost Serialization. I have run the tests myself using a simple wrapper:  
  
<!-- language: c++ -->  
  
```  
#include <modular-boost/libs/dynamic_bitset/dyn_bitset_unit_tests5.cpp>  
  
int main() {  
    test_main(0, nullptr);  
}  
```  
  
Which can then be compiled and run with e.g.  
  
```  
g++ main.cpp -lboost_system -lboost_serialization && ./a.out  
```  
  
No output means no errors.

---

## Comment 1

> Username: sehe  
> Created_at: 2015-06-24 00:35:49 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/14#issuecomment-114682897  

I just realized we might want to `bs.m_check_invariants();` after deserialization for the icing on the cake. In this case, I'd split the load/save operations (so we don't call `m_check_invariants()` on save).  
  
Let me know if you want this.

---

## Comment 2

> Username: mclow  
> Created_at: 2015-06-24 01:05:04 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/14#issuecomment-114687235  

Looks like adding the tests should be as simple as adding the line `[ run dyn_bitset_unit_tests5.cpp ]` to the file Jamfile.v2

---

## Comment 3

> Username: mclow  
> Created_at: 2015-06-24 01:05:53 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/14#issuecomment-114687573  

Might be as simple as...

---

## Comment 4

> Username: sehe  
> Created_at: 2015-06-24 01:07:05 UTC  
> Updated_at: 2016-07-21 22:36:36 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/14#issuecomment-114688048  

Like i said i don't think that brings in the libraries. Except of course on MSVC with auto linking  
  
##   
  
Seth

---

## Comment 5

> Username: mclow  
> Created_at: 2015-06-24 01:13:18 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/14#issuecomment-114690086  

In libs/property_tree/test/Jamfile.v2, I see:  
     : [ run test_property_tree.cpp /boost/serialization//boost_serialization  
          : : : <define>_SCL_SECURE_NO_WARNINGS=1 ]  
  
which looks like the syntax you want.  
We definitely want your new test to be run automatically.

---

## Comment 6

> Username: sehe  
> Created_at: 2015-06-24 09:06:13 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/14#issuecomment-114794794  

Thanks. That worked. (Well, looking at the actual Jamfile because of very dodgy markdown).  
  
Unit test is now registered and passes (at least on my test systems)

---

## Comment 7

> Username: rhalbersma  
> Created_at: 2016-12-29 16:06:34 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/14#issuecomment-269651702  

Is there any chance of this being integrated any time soon?

---

## Comment 8

> Username: eldiener  
> Created_at: 2016-12-29 18:01:14 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/14#issuecomment-269668305  

Currently there are conflicts so unless those conflicts are resolved this PR cannot be merged.

---

## Comment 9

> Username: sehe  
> Created_at: 2017-01-09 02:35:27 UTC  
> Updated_at: 2017-01-09 02:38:26 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/14#issuecomment-271201708  

The whole "conflict" is caused by changes on your end.  
  
Apparently all that happened is moving tests into their subfolder. Test still passes.  
  
Updated it, but I hope I don't have to revisit this again because of sheer bitrot.  
  
/cc @rhalbersma fyi

---
