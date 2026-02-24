# #113 Avoid -Wdangling-pointer warning with gcc 13 and -O2 [Closed]

> Username: Romain-Geissler-1A  
> Created at: 2023-03-14 20:46:04 UTC  
> Updated at: 2023-08-15 22:59:00 UTC  
> Closed at: 2023-08-15 22:58:59 UTC  
> Url: https://github.com/boostorg/algorithm/pull/113  

Apply the simple workaround described https://gcc.gnu.org/bugzilla/show_bug.cgi?id=106238#c6 which prevents the warning from being raised while keeping the exact same semantic.  
  
```  
In member function 'std::_Rb_tree<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::_Identity<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, std::allocator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > >::swap(std::_Rb_tree<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::_Identity<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, std::allocator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > >&)',  
    inlined from 'std::set<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, std::allocator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > >::swap(std::set<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, std::allocator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > >&)' at /remote/tools/Linux/2.6/1A/toolchain/x86_64-v23.0.7/include/c++/13.0.1/bits/stl_set.h:443:18,  
    inlined from 'boost::algorithm::iter_split<std::set<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, std::allocator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, boost::algorithm::detail::token_finderF<boost::algorithm::detail::is_any_ofF<char> > >(std::set<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, std::allocator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > >&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, boost::algorithm::detail::token_finderF<boost::algorithm::detail::is_any_ofF<char> >)std::set<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, std::allocator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > >& [clone .isra.0]' at /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/algorithm/string/iter_find.hpp:188:24:  
/remote/tools/Linux/2.6/1A/toolchain/x86_64-v23.0.7/include/c++/13.0.1/bits/stl_tree.h:2092:36: error: storing the address of local variable 'Tmp' in '_239->_M_parent' [-Werror=dangling-pointer=]  
 2092 |           __t._M_root()->_M_parent = __t._M_end();  
      |           ~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~  
```

---

## Comment 1

> Username: mclow  
> Created_at: 2023-03-14 21:21:58 UTC  
> Updated_at: 2023-03-14 21:22:15 UTC  
> Url: https://github.com/boostorg/algorithm/pull/113#issuecomment-1468862422  

is this only for gcc 13? (not yet released)

---

## Comment 2

> Username: Romain-Geissler-1A  
> Created_at: 2023-03-14 21:23:53 UTC  
> Url: https://github.com/boostorg/algorithm/pull/113#issuecomment-1468864357  

Yes, I compiled gcc from the master branch. I also compile the same code base with gcc 11 (compiled from the release branch) and I didn't hit this issue. Note that using gcc 13 from early february, I didn't have this issue either.

---

## Comment 3

> Username: Romain-Geissler-1A  
> Created_at: 2023-08-15 22:58:59 UTC  
> Url: https://github.com/boostorg/algorithm/pull/113#issuecomment-1679735298  

Closing this pull request as it seems gcc 13 doesn't have this bug anymore.

---
