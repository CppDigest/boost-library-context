# #14 - do_length is not correctly overridden? [Open]

> Username: jeking3  
> Created at: 2017-10-26 22:07:41 UTC  
> Updated at: 2017-12-23 00:38:45 UTC  
> Url: https://github.com/boostorg/detail/issues/14  

I have two independent builds reporting this error in Boost.Uuid, which includes this header from Boost.Serialization, which includes a header from Boost.Detail:  
```  
clang-linux.compile.c++.without-pth bin.v2/libs/serialization/build/clang-gnu-linux-3.9.0/debug/cxxstd-03/threadapi-pthread/utf8_codecvt_facet.o  
In file included from libs/serialization/src/utf8_codecvt_facet.cpp:19:  
In file included from ./boost/detail/utf8_codecvt_facet.ipp:13:  
./boost/detail/utf8_codecvt_facet.hpp:181:17: warning: 'boost::archive::detail::utf8_codecvt_facet::do_length' hides overloaded virtual function [-Woverloaded-virtual]  
    virtual int do_length(  
                ^  
/usr/lib/gcc/x86_64-linux-gnu/7.2.0/../../../../include/c++/7.2.0/bits/codecvt.h:453:11: note: hidden overloaded virtual function 'std::codecvt<wchar_t, char, __mbstate_t>::do_length' declared here: type mismatch at 1st parameter ('state_type &' (aka '__mbstate_t &') vs 'const std::mbstate_t &' (aka 'const __mbstate_t &'))  
      int do_length(state_type&, const extern_type* __from,  
          ^  
1 warning generated.  
```  
  
This same issue was identified by Coverity Scan as well.  
The build job that identified it: https://travis-ci.org/jeking3/uuid/jobs/293338558  
  
Thanks.  
  
@robertramey it looks like you made the original module - can you take a quick look?  In Coverity Scan the failure was reported against libstdc++-4.8 and here it is version 7.2.

---

## Comment 1

> Username: pdimov  
> Created at: 2017-12-23 00:38:45 UTC  
> Url: https://github.com/boostorg/detail/issues/14#issuecomment-353695849  

The signature of `do_length` in the standard changed between C++98 and C++03 as a result of a defect report (`const mbstate_t&` was changed to `mbstate&`). `utf8_codecvt_facet` overrides both.  
  
(At the time we got burned by not getting the signature correct a few times as it varied between standard libraries, and since when you get it wrong, the code still compiles, the safe choice was to override both.)
