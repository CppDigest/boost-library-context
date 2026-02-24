# #117 error: reference to 'ws' is ambiguous [Merged]

> Username: fiesh  
> Created at: 2015-11-23 16:23:15 UTC  
> Updated at: 2015-11-23 21:36:16 UTC  
> Merged at: 2015-11-23 21:36:16 UTC  
> Closed at: 2015-11-23 21:36:16 UTC  
> Url: https://github.com/boostorg/fusion/pull/117  

Upon including a file that declares a namespace `::ws`, `GCC` would no longer compile due to the error given above.  This little fix solves the issue.

---

## Comment 1

> Username: djowel  
> Created_at: 2015-11-23 20:48:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/117#issuecomment-159059898  

is that the exact error message? "Do not use "using namespace std;" what ambiguities with ws do you mean?

---

## Comment 2

> Username: fiesh  
> Created_at: 2015-11-23 21:32:56 UTC  
> Url: https://github.com/boostorg/fusion/pull/117#issuecomment-159072359  

Take the following file `x.cpp`:  
  
```  
#include <iostream>  
namespace ws {}  
#include <boost/spirit/include/karma.hpp>  
```  
  
Then `gcc` 4.9.3 gives:  
  
```  
In file included from /usr/include/boost/fusion/sequence/io/detail/out.hpp:13:0,  
                 from /usr/include/boost/fusion/sequence/io/out.hpp:13,  
                 from /usr/include/boost/fusion/include/out.hpp:10,  
                 from /usr/include/boost/spirit/home/karma/nonterminal/debug_handler.hpp:20,  
                 from /usr/include/boost/spirit/home/karma/nonterminal.hpp:15,  
                 from /usr/include/boost/spirit/home/karma.hpp:17,  
                 from /usr/include/boost/spirit/include/karma.hpp:16,  
                 from x.cpp:5:  
/usr/include/boost/fusion/sequence/io/detail/manip.hpp: In member function 'void boost::fusion::detail::string_ios_manip<Tag, Stream>::read(const char*) const':  
/usr/include/boost/fusion/sequence/io/detail/manip.hpp:117:17: error: reference to 'ws' is ambiguous  
                 ws(stream);  
                 ^  
x.cpp:3:14: note: candidates are: namespace ws { }  
 namespace ws {}  
              ^  
In file included from /usr/lib/gcc/x86_64-pc-linux-gnu/4.9.3/include/g++-v4/istream:882:0,  
                 from /usr/lib/gcc/x86_64-pc-linux-gnu/4.9.3/include/g++-v4/iostream:40,  
                 from x.cpp:1:  
/usr/lib/gcc/x86_64-pc-linux-gnu/4.9.3/include/g++-v4/bits/istream.tcc:1016:5: note:                 template<class _CharT, class _Traits> std::basic_istream<_CharT, _Traits>& std::ws(std::basic_istream<_CharT, _Traits>&)  
     ws(basic_istream<_CharT, _Traits>& __in)  
     ^  
```  
  
and `clang` 3.5.0 gives  
  
```  
In file included from x.cpp:5:  
In file included from /usr/include/boost/spirit/include/karma.hpp:16:  
In file included from /usr/include/boost/spirit/home/karma.hpp:17:  
In file included from /usr/include/boost/spirit/home/karma/nonterminal.hpp:15:  
In file included from /usr/include/boost/spirit/home/karma/nonterminal/debug_handler.hpp:20:  
In file included from /usr/include/boost/fusion/include/out.hpp:10:  
In file included from /usr/include/boost/fusion/sequence/io/out.hpp:13:  
In file included from /usr/include/boost/fusion/sequence/io/detail/out.hpp:13:  
/usr/include/boost/fusion/sequence/io/detail/manip.hpp:117:17: error: reference to 'ws' is ambiguous  
                ws(stream);  
                ^  
x.cpp:3:11: note: candidate found by name lookup is 'ws'  
namespace ws {}  
          ^  
/usr/lib/gcc/x86_64-pc-linux-gnu/4.9.3/include/g++-v4/bits/istream.tcc:1016:5: note: candidate found by name lookup is 'std::ws'  
    ws(basic_istream<_CharT, _Traits>& __in)  
    ^  
1 error generated.  
```

---
