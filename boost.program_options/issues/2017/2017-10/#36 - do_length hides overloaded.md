# #36 - do_length hides overloaded [Open]

> Username: jeking3  
> Created at: 2017-10-25 23:24:52 UTC  
> Updated at: 2017-10-25 23:24:52 UTC  
> Url: https://github.com/boostorg/program_options/issues/36  

Got this in a build today (develop):  
  
https://api.travis-ci.org/jobs/292877168/log.txt?deansi=true  
  
```  
In file included from libs/program_options/src/convert.cpp:19:  
In file included from ./boost/program_options/detail/utf8_codecvt_facet.hpp:19:  
./boost/detail/utf8_codecvt_facet.hpp:181:17: warning: 'boost::program_options::detail::utf8_codecvt_facet::do_length' hides overloaded virtual function [-Woverloaded-virtual]  
    virtual int do_length(  
                ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/7.2.0/../../../../include/c++/7.2.0/bits/codecvt.h:453:11: note: hidden overloaded virtual function 'std::codecvt<wchar_t, char, __mbstate_t>::do_length' declared here: type mismatch at 1st parameter ('std::codecvt<wchar_t, char, __mbstate_t>::state_type &' (aka '__mbstate_t &') vs 'const std::mbstate_t &' (aka 'const __mbstate_t &'))  
      int do_length(state_type&, const extern_type* __from,  
          ^  
```
