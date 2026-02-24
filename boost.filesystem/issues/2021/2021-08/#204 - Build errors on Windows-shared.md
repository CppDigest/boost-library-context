# #204 - Build errors on Windows/shared [Closed]

> Username: pdimov  
> Created at: 2021-08-17 11:37:18 UTC  
> Updated at: 2021-08-17 23:30:17 UTC  
> Closed at: 2021-08-17 21:48:32 UTC  
> Url: https://github.com/boostorg/filesystem/issues/204  

```  
compile-c-c++ bin.v2\libs\filesystem\build\msvc-14.2\debug\threading-multi\utf8_codecvt_facet.obj  
utf8_codecvt_facet.cpp  
D:\a\boost_install\boost-root\boost/detail/utf8_codecvt_facet.ipp(33): error C2375: 'boost::filesystem::detail::utf8_codecvt_facet::utf8_codecvt_facet': redefinition; different linkage  
D:\a\boost_install\boost-root\boost/detail/utf8_codecvt_facet.hpp(116): note: see declaration of 'boost::filesystem::detail::utf8_codecvt_facet::utf8_codecvt_facet'  
D:\a\boost_install\boost-root\boost/detail/utf8_codecvt_facet.ipp(39): error C2375: 'boost::filesystem::detail::utf8_codecvt_facet::~utf8_codecvt_facet': redefinition; different linkage  
D:\a\boost_install\boost-root\boost/detail/utf8_codecvt_facet.hpp(117): note: see declaration of 'boost::filesystem::detail::utf8_codecvt_facet::~utf8_codecvt_facet'  
D:\a\boost_install\boost-root\boost/detail/utf8_codecvt_facet.ipp(43): error C2375: 'boost::filesystem::detail::utf8_codecvt_facet::do_in': redefinition; different linkage  
D:\a\boost_install\boost-root\boost/detail/utf8_codecvt_facet.hpp(119): note: see declaration of 'boost::filesystem::detail::utf8_codecvt_facet::do_in'  
D:\a\boost_install\boost-root\boost/detail/utf8_codecvt_facet.ipp(120): error C2375: 'boost::filesystem::detail::utf8_codecvt_facet::do_out': redefinition; different linkage  
D:\a\boost_install\boost-root\boost/detail/utf8_codecvt_facet.hpp(129): note: see declaration of 'boost::filesystem::detail::utf8_codecvt_facet::do_out'  
D:\a\boost_install\boost-root\boost/detail/utf8_codecvt_facet.ipp(182): error C2375: 'boost::filesystem::detail::utf8_codecvt_facet::do_length': redefinition; different linkage  
D:\a\boost_install\boost-root\boost/detail/utf8_codecvt_facet.hpp(181): note: see declaration of 'boost::filesystem::detail::utf8_codecvt_facet::do_length'  
D:\a\boost_install\boost-root\boost/detail/utf8_codecvt_facet.ipp(204): error C2375: 'boost::filesystem::detail::utf8_codecvt_facet::get_octet_count': redefinition; different linkage  
D:\a\boost_install\boost-root\boost/detail/utf8_codecvt_facet.hpp(153): note: see declaration of 'boost::filesystem::detail::utf8_codecvt_facet::get_octet_count'  
D:\a\boost_install\boost-root\boost/detail/utf8_codecvt_facet.ipp(276): error C2375: 'boost::filesystem::detail::utf8_codecvt_facet::get_cont_octet_out_count': redefinition; different linkage  
D:\a\boost_install\boost-root\boost/detail/utf8_codecvt_facet.hpp(157): note: see declaration of 'boost::filesystem::detail::utf8_codecvt_facet::get_cont_octet_out_count'  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2021-08-17 20:44:34 UTC  
> Url: https://github.com/boostorg/filesystem/issues/204#issuecomment-900617361  

CI was fine last I checked, is your setup somehow special?

---

## Comment 2

> Username: pdimov  
> Created at: 2021-08-17 20:56:30 UTC  
> Url: https://github.com/boostorg/filesystem/issues/204#issuecomment-900624447  

No. `b2 --with-filesystem link=shared` fails, presumably because of https://github.com/boostorg/detail/commit/cc329060711fd34e66d66b090662dc8afd0db528.

---

## Comment 3

> Username: Lastique  
> Created at: 2021-08-17 21:49:23 UTC  
> Url: https://github.com/boostorg/filesystem/issues/204#issuecomment-900654265  

Please test if this fixed the problem.

---

## Comment 4

> Username: pdimov  
> Created at: 2021-08-17 23:30:17 UTC  
> Url: https://github.com/boostorg/filesystem/issues/204#issuecomment-900698354  

Seems to work now, thanks.
