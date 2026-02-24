# #43 Missing includes [Closed]

> Username: alekseyt  
> Created at: 2019-09-10 09:53:20 UTC  
> Updated at: 2022-05-30 08:24:06 UTC  
> Closed at: 2022-05-30 08:24:06 UTC  
> Url: https://github.com/boostorg/locale/pull/43  

`#include <algorithm>` for `std::find()` (line 521 without patch, 522 with patch).  
  
I'm guessing it was included in `locale/boundary/index.hpp` which i don't include during build, then included by `locale/boundary.hpp` and then included by something else. Or maybe it was included in `boost/function.hpp`.  
  
When compiled without `locale/boundary/index.hpp`, `boost/function.hpp`, `boost/shared_ptr.hpp` and couple other headers, it shows the following error (GCC 8):  
  
```gcc  
boost-locale/src/shared/message.cpp: In instantiation of ‘const char_type* boost::locale::gnu_gettext::mo_message<CharType>::get(int, const char_type*, const char_type*, int) const [with CharType = wchar_t; boost::locale::gnu_gettext::mo_message<CharType>::char_type = wchar_t]’:  
boost-locale/src/shared/message.cpp:508:42:   required from here  
boost-locale/src/shared/message.cpp:521:36: error: no matching function for call to ‘find(const wchar_t*&, const wchar_t*&, int)’  
                         p=std::find(p,ptr.second,0);  
```

---

## Comment 1

> Username: alekseyt  
> Created_at: 2019-10-23 14:21:59 UTC  
> Updated_at: 2019-10-23 14:35:56 UTC  
> Url: https://github.com/boostorg/locale/pull/43#issuecomment-545468095  

Another commit (8485f6d) adds missing `#include "conv.hpp"`. It's required for `boost::locale::conv::impl::convert_between()` to pick up `converter_between`.  
  
I'm not exactly sure what's going on here, i suspect that it won't build without precompiled headers, although i don't build it with default build tools, i'm using my own CMake scripts, so i can't tell for sure. This is what i was able to find regarding missing includes in my build.

---

## Comment 2

> Username: alekseyt  
> Created_at: 2019-10-23 14:31:57 UTC  
> Updated_at: 2019-10-23 14:38:03 UTC  
> Url: https://github.com/boostorg/locale/pull/43#issuecomment-545472796  

And another commit (81a9f7f) replaces forward declaration of `boost::shared_ptr` with `#include <boost/shared_ptr.hpp>`.  
  
This PR might look strange, but i'm actually trying to build a version of Boost.Locale without other parts of Boost. So i replaced `<boost/shared_ptr.hpp>` (and some other files) with a shim like this:  
  
```c++  
#ifndef BOOST_SHARED_PTR_SHIM  
#define BOOST_SHARED_PTR_SHIM  
  
#include <memory>  
  
namespace boost {  
  
template <typename T>  
using shared_ptr = std::shared_ptr<T>;  
  
}  
  
#endif // BOOST_SHARED_PTR_SHIM  
```  
  
Such forward declaration doesn't work with shim and i think correct way to use `boost::shared_ptr` would be to include it anyways.  
  
I'm trying to build a very minimally viable version of  Boost.Locale with UTF-8 only implementation of gettext and trying to reduce the difference between my copy and upstream. It would be really nice if not merge this PR, then to resolve those issues in some other way.

---

## Comment 3

> Username: alekseyt  
> Created_at: 2019-10-23 14:34:35 UTC  
> Updated_at: 2019-10-23 14:34:55 UTC  
> Url: https://github.com/boostorg/locale/pull/43#issuecomment-545474057  

On the side note, i'm having hard time figuring out includes order in source code, so i just placed new includes where i felt it is most appropriate in regards to how includes are ordered in source code in general. If something is in the wrong place - i would appreciate if someone would link a proper doc.

---
