# #296 Fix clang-cl regression introduced by #282 [Merged]

> Username: pdimov  
> Created at: 2023-08-28 17:19:02 UTC  
> Updated at: 2023-08-29 22:49:19 UTC  
> Merged at: 2023-08-29 22:49:18 UTC  
> Closed at: 2023-08-29 22:49:18 UTC  
> Url: https://github.com/boostorg/serialization/pull/296  

The sources conditioned on wstreambuf existence were mistakenly added to WSOURCES, which resulted in them being present in both libraries. This causes link errors with clang-cl.  
  
This pull request removes the inadvertent addition, fixing the link errors.  
  
Note that currently the clang-cl CI job still fails, but for reasons unrelated to this change and outside of our control (https://github.com/actions/runner-images/issues/8125.) It's supposed to be fixed by the GHA team this week when they deploy the fixed windows-2022 image.

---

## Comment 1

> Username: Flamefire  
> Created_at: 2023-08-29 07:06:51 UTC  
> Url: https://github.com/boostorg/serialization/pull/296#issuecomment-1696887065  

FTR (previously mentioned on Slack): I don't think this is a good (final) solution. The 2 files in question (xml_oarchive.cpp & utf8_codecvt_facet.cpp) are (as far as I can tell) not actually dependent on `std::wstream` but `boost/archive/detail/utf8_codecvt_facet.hpp` errors out when it is not available and `xml_oarchive.cpp` includes it transitively:  
```  
In file included from ./boost/archive/iterators/mb_from_wchar.hpp:31,  
                 from ./boost/archive/impl/xml_oarchive_impl.ipp:28,  
                 from libs/serialization/src/xml_oarchive.cpp:23:  
./boost/archive/detail/utf8_codecvt_facet.hpp:13:2: error: #error "wide char i/o not supported on this platform"  
```  
  
The `mb_from_wchar` dependency is (only) required to load/save `std::wstring`/`wchar_t`-arrays.  
  
Summary: Is it really worth excluding a whole class because a single method requires something? And: Is that requirement even correct (it looks like `std::wstring` is used, but `std::wstream` is required)?  
  
And going further: As `std::wstream` is C++03 is it still worth having conditionals on that?     
However that also means that this fix is sufficient as the 2 files will in practice now (almost) always be included in the build  
  
@robertramey

---
