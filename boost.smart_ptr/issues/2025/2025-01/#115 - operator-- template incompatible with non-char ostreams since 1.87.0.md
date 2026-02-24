# #115 - operator<< template incompatible with non-char ostreams since 1.87.0 [Closed]

> Username: xen0n  
> Created at: 2025-01-13 10:45:56 UTC  
> Updated at: 2025-01-13 15:56:39 UTC  
> Closed at: 2025-01-13 15:56:19 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/115  

This code (simplified from Lucene++) fails to compile with Boost 1.87.0 but is otherwise fine with older versions:  
  
```cpp  
#include <iosfwd>  
#include <sstream>  
#include <boost/shared_ptr.hpp>  
  
typedef std::basic_ostringstream< wchar_t, std::char_traits<wchar_t> > StringStream;  
  
class X {};  
typedef boost::shared_ptr<X> T;  
  
void foo(StringStream &ss, T &x) {  
        ss << x;  
}  
```  
  
I confirmed with GCC 14 and Clang 19.  
  
While I'm not very familiar with Boost's codebase, it seems commit d2c24075850aa8c6939b0cc690edeb741e733f1a is responsible, that removed the template for `std::basic_ostream` but retaining one for `std::ostream` which is only for `char` ostreams.  
  
According to the original `#ifdef`'s it looks like an oversight, as the remaining codepath seems intended for `BOOST_NO_TEMPLATED_IOSTREAMS` or GCC < 3; but I only know so much about Boost internals and would like the maintainers to take a look.  
  
Related downstream issue:  
  
* https://github.com/luceneplusplus/LucenePlusPlus/issues/208  
* https://bugs.gentoo.org/947796

---

## Comment 1

> Username: pdimov  
> Created at: 2025-01-13 13:01:41 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/115#issuecomment-2587046509  

That's definitely an oversight. Thank you for spotting and reporting it.

---

## Comment 2

> Username: pdimov  
> Created at: 2025-01-13 15:56:37 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/115#issuecomment-2587510532  

Should be fixed by https://github.com/boostorg/smart_ptr/commit/e7433ba54596da97cb7859455cd37ca140305a9c.
