# #272 - Compilation error using clang [Closed]

> Username: barendgehrels  
> Created at: 2021-02-23 11:49:41 UTC  
> Updated at: 2022-11-25 06:12:38 UTC  
> Closed at: 2022-11-25 06:12:38 UTC  
> Url: https://github.com/boostorg/fiber/issues/272  

Using `clang`, we can't compile Boost.Fiber because the compilation error listed below.  
With `gcc` it's all fine.  
Clang version: `clang version 5.0.2 (tags/RELEASE_502/final)`  
  
As a workaround, or solution, we changed this line:  
https://github.com/boostorg/fiber/blob/develop/include/boost/fiber/context.hpp#L120  
into just `fss_data() noexcept {}`  
and then it compiles and works fine, also with `clang`.  
  
Minimal reproduction scenario:  
`clang -c -std=c++14  -I . -o /tmp/scheduler1.o libs/fiber/src/scheduler.cpp`  
  
Scenario using all the compilation flags used in Boost.Fiber itself, but with `clang` and `c++14`:  
`clang -std=c++14  -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_FIBERS_DYN_LINK=1 -DBOOST_FIBERS_SOURCE -DBOOST_FILESYSTEM_DYN_LINK=1  -I . -c -o /tmp/scheduler3.o libs/fiber/src/scheduler.cpp`  
  
Both scenarios give the same error:  
  
```  
In file included from libs/fiber/src/scheduler.cpp:7:  
In file included from ./boost/fiber/scheduler.hpp:23:  
./boost/fiber/context.hpp:120:9: error: default member initializer for 'vp' needed within definition of enclosing class 'context' outside of member functions  
        fss_data() noexcept = default;  
        ^  
./boost/intrusive/pack_options.hpp:109:6: note: in instantiation of template class 'boost::intrusive::build_number_seq<0, boost::intrusive::index_tuple<0, 1, 2> >' requested here  
   : build_number_seq<Num - 1, index_tuple<Indexes..., sizeof...(Indexes)> >  
     ^  
./boost/intrusive/pack_options.hpp:109:6: note: in instantiation of template class 'boost::intrusive::build_number_seq<1, boost::intrusive::index_tuple<0, 1> >' requested here  
./boost/intrusive/pack_options.hpp:109:6: note: in instantiation of template class 'boost::intrusive::build_number_seq<2, boost::intrusive::index_tuple<0> >' requested here  
./boost/intrusive/pack_options.hpp:189:21: note: in instantiation of template class 'boost::intrusive::build_number_seq<3, boost::intrusive::index_tuple<> >' requested here  
   typedef typename build_number_seq<sizeof...(Types)>::type indexes_t;  
                    ^  
./boost/intrusive/pack_options.hpp:230:21: note: in instantiation of template class 'boost::intrusive::invert_typelist<boost::intrusive::typelist<boost::intrusive::hook_defaults,  
      boost::intrusive::tag<boost::fibers::detail::remote_ready_tag>, boost::intrusive::link_mode<boost::intrusive::link_mode_type::safe_link> > >' requested here  
   typedef typename invert_typelist<typelist_t>::type inverted_typelist;  
                    ^  
./boost/intrusive/slist_hook.hpp:172:21: note: in instantiation of template class 'boost::intrusive::pack_options<boost::intrusive::hook_defaults, boost::intrusive::tag<boost::fibers::detail::remote_ready_tag>,  
      boost::intrusive::link_mode<boost::intrusive::link_mode_type::safe_link> >' requested here  
   typedef typename pack_options  
                    ^  
./boost/intrusive/slist_hook.hpp:210:14: note: in instantiation of template class 'boost::intrusive::make_slist_member_hook<boost::intrusive::tag<boost::fibers::detail::remote_ready_tag>,  
      boost::intrusive::link_mode<boost::intrusive::link_mode_type::safe_link> >' requested here  
   :  public make_slist_member_hook<  
             ^  
./boost/fiber/context.hpp:142:57: note: in instantiation of template class 'boost::intrusive::slist_member_hook<boost::intrusive::tag<boost::fibers::detail::remote_ready_tag>,  
      boost::intrusive::link_mode<boost::intrusive::link_mode_type::safe_link> >' requested here  
    detail::remote_ready_hook                           remote_ready_hook_{};  
                                                        ^  
./boost/fiber/context.hpp:117:49: note: default member initializer declared here  
        void                                *   vp{ nullptr };  
                                                ^  
./boost/fiber/context.hpp:120:9: error: default member initializer for 'cleanup_function' needed within definition of enclosing class 'context' outside of member functions  
        fss_data() noexcept = default;  
        ^  
./boost/fiber/context.hpp:118:49: note: default member initializer declared here  
        detail::fss_cleanup_function::ptr_t     cleanup_function{};  
                                                ^  
2 errors generated.  
  
```

---

## Comment 1

> Username: barendgehrels  
> Created at: 2021-05-12 11:20:12 UTC  
> Url: https://github.com/boostorg/fiber/issues/272#issuecomment-839691422  

Any news?

---

## Comment 2

> Username: mohak6401  
> Created at: 2021-09-24 08:53:18 UTC  
> Url: https://github.com/boostorg/fiber/issues/272#issuecomment-926460995  

not able to understand your problem sir

---

## Comment 3

> Username: bernhardmgruber  
> Created at: 2021-12-09 12:23:22 UTC  
> Url: https://github.com/boostorg/fiber/issues/272#issuecomment-989803748  

Same problem here. Just compile  
```c++  
#include <boost/fiber/fiber.hpp>  
```  
with `clang-5 -std=c++11`. See: https://godbolt.org/z/rY89sMfaK  
  
Error:  
```  
In file included from <source>:1:  
In file included from /opt/compiler-explorer/libs/boost_1_77_0/boost/fiber/fiber.hpp:22:  
/opt/compiler-explorer/libs/boost_1_77_0/boost/fiber/context.hpp:120:9: error: default member initializer for 'vp' needed within definition of enclosing class 'context' outside of member functions  
        fss_data() noexcept = default;  
        ^  
....  
```  
Works with clang-6 and higher.

---

## Comment 4

> Username: muggenhor  
> Created at: 2022-11-24 12:46:39 UTC  
> Updated at: 2022-11-24 12:52:20 UTC  
> Url: https://github.com/boostorg/fiber/issues/272#issuecomment-1326405811  

Using that same godbolt link ^^ I can verify that this is fixed by Boost 1.78.0.  
  
Probably a result of df1196705bc3b6c2d6a6bd21907aba7fe44396b2:  
```diff  
-        fss_data() noexcept = default;  
+        fss_data() = default;  
```  
  
I guess Clang 5 didn't like the `noexcept` in that default constructor definition...

---

## Comment 5

> Username: olk  
> Created at: 2022-11-25 06:12:38 UTC  
> Url: https://github.com/boostorg/fiber/issues/272#issuecomment-1327060907  

fixed
