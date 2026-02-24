# #27 - std::auto_ptr is deprecated [Closed]

> Username: kivadiu  
> Created at: 2018-07-30 08:34:34 UTC  
> Updated at: 2018-08-22 07:03:47 UTC  
> Closed at: 2018-08-22 07:03:47 UTC  
> Url: https://github.com/boostorg/locale/issues/27  

While building 1.68.0 beta 1 on x86_64 linux with g++ 8.2.0, I get the following warnings and many more of the same king in boost::locale:  
```  
gcc.compile.c++ bin.v2/libs/locale/build/gcc-8.2.0/release/link-static/threading-multi/util/default_locale.o  
  
    "g++-8.2.0"   -I/softs/lin64-gcc-8.2.0/release/iconv/include -I/softs/lin64-gcc-8.2.0/release/gettext/include -I/softs/lin64-gcc-8.2.0/release/bzip2/include -I/softs/lin64-gcc-8.2.0/release/zlib/inc>  
  
In file included from libs/locale/src/util/default_locale.cpp:10:  
./boost/locale/util.hpp:180:28: warning: « template<class> class std::auto_ptr » est obsolète [-Wdeprecated-declarations]  
     BOOST_LOCALE_DECL std::auto_ptr<base_converter> create_utf8_converter();  
                            ^~~~~~~~  
In file included from /softs/gcc-8.2.0/include/c++/8.2.0/bits/locale_conv.h:41,  
                 from /softs/gcc-8.2.0/include/c++/8.2.0/locale:43,  
                 from ./boost/locale/util.hpp:10,  
                 from libs/locale/src/util/default_locale.cpp:10:  
/softs/gcc-8.2.0/include/c++/8.2.0/bits/unique_ptr.h:53:28: note: déclaré ici  
   template<typename> class auto_ptr;  
                            ^~~~~~~~  
In file included from libs/locale/src/util/default_locale.cpp:10:  
./boost/locale/util.hpp:188:28: warning: « template<class> class std::auto_ptr » est obsolète [-Wdeprecated-declarations]  
     BOOST_LOCALE_DECL std::auto_ptr<base_converter> create_simple_converter(std::string const &encoding);  
                            ^~~~~~~~  
In file included from /softs/gcc-8.2.0/include/c++/8.2.0/bits/locale_conv.h:41,  
                 from /softs/gcc-8.2.0/include/c++/8.2.0/locale:43,  
                 from ./boost/locale/util.hpp:10,  
                 from libs/locale/src/util/default_locale.cpp:10:  
/softs/gcc-8.2.0/include/c++/8.2.0/bits/unique_ptr.h:53:28: note: déclaré ici  
   template<typename> class auto_ptr;  
                            ^~~~~~~~  
In file included from libs/locale/src/util/default_locale.cpp:10:  
./boost/locale/util.hpp:203:59: warning: « template<class> class std::auto_ptr » est obsolète [-Wdeprecated-declarations]  
     std::locale create_codecvt(std::locale const &in,std::auto_ptr<base_converter> cvt,character_facet_type type);  
                                                           ^~~~~~~~  
```  
  
std::unique_ptr should be used if possible.

---

## Comment 1

> Username: artyom-beilis  
> Created at: 2018-08-22 07:03:47 UTC  
> Url: https://github.com/boostorg/locale/issues/27#issuecomment-414932853  

Since the code supports C++03 I can't remove `auto_ptr` by default. It is part of API.  
It is removed only when it is not available at language level (C++17)   
  
Since my policy is not to break existing software by changing API it will remain in the code.  
  
For user end code you can always add: `BOOST_LOCALE_HIDE_AUTO_PTR` define to hide the warnings.
