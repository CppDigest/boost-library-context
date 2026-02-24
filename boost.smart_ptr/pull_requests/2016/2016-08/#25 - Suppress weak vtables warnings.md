# #25 Suppress weak vtables warnings [Merged]

> Username: Kojoley  
> Created at: 2016-08-31 16:35:16 UTC  
> Updated at: 2016-09-10 11:04:12 UTC  
> Merged at: 2016-09-10 11:04:11 UTC  
> Closed at: 2016-09-10 11:04:11 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/25  

Fixes following warnings:  
  
```  
In file included from qi/actions2.cpp:20:  
In file included from ../../../boost/spirit/include/qi.hpp:16:  
In file included from ../../../boost/spirit/home/qi.hpp:28:  
In file included from ../../../boost/spirit/home/qi/string.hpp:15:  
In file included from ../../../boost/spirit/home/qi/string/symbols.hpp:30:  
In file included from ../../../boost/shared_ptr.hpp:17:  
In file included from ../../../boost/smart_ptr/shared_ptr.hpp:28:  
In file included from ../../../boost/smart_ptr/detail/shared_count.hpp:29:  
In file included from ../../../boost/smart_ptr/detail/sp_counted_base.hpp:45:  
../../../boost/smart_ptr/detail/sp_counted_base_clang.hpp:61:7: warning: 'sp_counted_base' has no out-of-line virtual method definitions; its vtable will be emitted in every translation unit [-Wweak-vtables]  
class sp_counted_base  
      ^  
In file included from qi/actions2.cpp:20:  
In file included from ../../../boost/spirit/include/qi.hpp:16:  
In file included from ../../../boost/spirit/home/qi.hpp:28:  
In file included from ../../../boost/spirit/home/qi/string.hpp:15:  
In file included from ../../../boost/spirit/home/qi/string/symbols.hpp:30:  
In file included from ../../../boost/shared_ptr.hpp:17:  
In file included from ../../../boost/smart_ptr/shared_ptr.hpp:28:  
In file included from ../../../boost/smart_ptr/detail/shared_count.hpp:28:  
../../../boost/smart_ptr/bad_weak_ptr.hpp:39:7: warning: 'bad_weak_ptr' has no out-of-line virtual method definitions; its vtable will be emitted in every translation unit [-Wweak-vtables]  
class bad_weak_ptr: public std::exception  
      ^  
```

---
