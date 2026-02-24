# #152 Fix conflict with std::fill_n and boost::range::fill_n (Trac 7189) [Merged]

> Username: mloskot  
> Created at: 2018-10-12 15:59:52 UTC  
> Updated at: 2018-10-30 17:19:34 UTC  
> Merged at: 2018-10-13 19:37:42 UTC  
> Closed at: 2018-10-13 19:37:42 UTC  
> Url: https://github.com/boostorg/gil/pull/152  

Add minimal test for the `std::fill` and `boost::array` or `std::array` as pixel type.  
  
### References  
  
* https://svn.boost.org/trac10/ticket/7189  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed  
  
  
-----  
  
https://svn.boost.org/trac10/ticket/7189 description:  
  
> The following does not compile because it cannot be resolved whether a call  
> to `boost::range::fill_n` or `std::fill_n` is correct  
>   
> ```  
> #include <boost/array.hpp>  
> #include <boost/gil/image_view.hpp>  
> #include <boost/gil/image.hpp>  
> #include <boost/gil/typedefs.hpp>  
> #include <boost/range/algorithm/fill_n.hpp>  
> namespace gil = boost::gil;  
> int main( int argc, char** argv)  
> {  
>     gil::image<boost::array<float,2> > img;  
>     std::fill( gil::view(img).begin(), gil::view(img).end(), boost::array<float,2>() );  
> }  
> ```  
>   
> Error:  
>   
> ```  
> [100%] Building CXX object CMakeFiles/test_misc.dir/test_misc.cpp.o  
> In file included from /usr/include/boost/gil/image.hpp:29:0,  
>                  from /media/data2TB/data_win/Code/weavepattern/ActiveGrid/tests/test_misc.cpp:3:  
> /usr/include/boost/gil/algorithm.hpp: In function ‘void std::fill(boost::gil::iterator_from_2d<IL>, boost::gil::iterator_from_2d<IL>, const V&) [with IL = boost::gil::memory_based_2d_locator<boost::gil::memory_based_step_iterator<boost::array<float, 2ul>*> >, V = boost::array<float, 2ul>]’:  
> /media/data2TB/data_win/Code/weavepattern/ActiveGrid/tests/test_misc.cpp:14:86:   instantiated from here  
> /usr/include/boost/gil/algorithm.hpp:382:13: error: call of overloaded ‘fill_n(boost::array<float, 2ul>*&, std::ptrdiff_t&, const boost::array<float, 2ul>&)’ is ambiguous  
> /usr/include/boost/gil/algorithm.hpp:382:13: note: candidates are:  
> /usr/include/c++/4.6/bits/stl_algobase.h:775:5: note: _OI std::fill_n(_OI, _Size, const _Tp&) [with _OI = boost::array<float, 2ul>*, _Size = long int, _Tp = boost::array<float, 2ul>]  
> /usr/include/boost/range/algorithm/fill_n.hpp:31:22: note: ForwardRange& boost::range::fill_n(ForwardRange&, Size, const Value&) [with ForwardRange = boost::array<float, 2ul>*, Size = long int, Value = boost::array<float, 2ul>]  
> /usr/include/boost/range/algorithm/fill_n.hpp:41:28: note: const ForwardRange& boost::range::fill_n(const ForwardRange&, Size, const Value&) [with ForwardRange = boost::array<float, 2ul>*, Size = long int, Value = boost::array<float, 2ul>]  
> make[3]: *** [CMakeFiles/test_misc.dir/test_misc.cpp.o] Error 1  
> ```  
  
-----  
  
I also confirmed the error myself using GCC 5.5 and 8.2:  
  
```  
$ g++ -std=c++11 -I//mnt/d/boost.wsl gil-trac-ticket-7189.cpp  
In file included from //mnt/d/boost.wsl/boost/gil/image.hpp:11:0,  
                 from gil-trac-ticket-7189.cpp:3:  
//mnt/d/boost.wsl/boost/gil/algorithm.hpp: In instantiation of ‘void std::fill(boost::gil::iterator_from_2d<Loc>, boost::gil::iterator_from_2d<Loc>, const V&) [with IL = boost::gil::memory_based_2d_locator<boost::gil::memory_based_step_iterator<boost::array<float, 2ul>*> >; V = boost::array<float, 2ul>]’:  
gil-trac-ticket-7189.cpp:14:86:   required from here  
//mnt/d/boost.wsl/boost/gil/algorithm.hpp:358:19: error: call of overloaded ‘fill_n(boost::array<float, 2ul>*&, std::ptrdiff_t&, const boost::array<float, 2ul>&)’ is ambiguous  
             fill_n(first.x(), numToDo, val);  
                   ^  
In file included from /usr/include/c++/5/bits/char_traits.h:39:0,  
                 from /usr/include/c++/5/string:40,  
                 from /usr/include/c++/5/stdexcept:39,  
                 from //mnt/d/boost.wsl/boost/array.hpp:44,  
                 from gil-trac-ticket-7189.cpp:1:  
/usr/include/c++/5/bits/stl_algobase.h:800:5: note: candidate: _OI std::fill_n(_OI, _Size, const _Tp&) [with _OI = boost::array<float, 2ul>*; _Size = long int; _Tp = boost::array<float, 2ul>]  
     fill_n(_OI __first, _Size __n, const _Tp& __value)  
     ^  
In file included from gil-trac-ticket-7189.cpp:6:0:  
//mnt/d/boost.wsl/boost/range/algorithm/fill_n.hpp:31:22: note: candidate: ForwardRange& boost::range::fill_n(ForwardRange&, Size, const Value&) [with ForwardRange = boost::array<float, 2ul>*; Size = long int; Value = boost::array<float, 2ul>]  
 inline ForwardRange& fill_n(ForwardRange& rng, Size n, const Value& val)  
                      ^  
//mnt/d/boost.wsl/boost/range/algorithm/fill_n.hpp:41:28: note: candidate: const ForwardRange& boost::range::fill_n(const ForwardRange&, Size, const Value&) [with ForwardRange = boost::array<float, 2ul>*; Size = long int; Value = boost::array<float, 2ul>]  
 inline const ForwardRange& fill_n(const ForwardRange& rng, Size n, const Value& val)  
                            ^  
```

---

## Review 1 [Approved]

> Username: stefanseefeld  
> Created_at: 2018-10-12 16:02:55 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/152#pullrequestreview-164308610  

Good catch ! And thanks for being so thorough as to adding a new test !

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-10-12 16:36:40 UTC  
> Updated_at: 2018-10-12 16:36:57 UTC  
> Url: https://github.com/boostorg/gil/pull/152#issuecomment-429386100  

@stefanseefeld Thanks. Yeah, to me, tests are quite critical. I personally do not like lazy PRs with fixes but without tests :)  
  
BTW, I've just updated this PR adding ` test/algorithm`  to `test/Jamfile` so the subdirectory is actually built.

---
