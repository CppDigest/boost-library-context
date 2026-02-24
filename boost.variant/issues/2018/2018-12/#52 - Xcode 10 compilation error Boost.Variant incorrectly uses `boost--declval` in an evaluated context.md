# #52 - Xcode 10 compilation error Boost.Variant incorrectly uses `boost::declval` in an evaluated context. [Closed]

> Username: rogerjarrett  
> Created at: 2018-12-04 18:11:35 UTC  
> Updated at: 2019-01-05 10:51:34 UTC  
> Closed at: 2019-01-05 10:51:34 UTC  
> Url: https://github.com/boostorg/variant/issues/52  

Xcode 10.1 and Xcode 10 (clang++)  fails to compile boost variant code in unnamed namespace   
Boost version 1.65  
  
Compilation error:  
boost/include/boost/type_traits/declval.hpp:76:44: error:   
      function 'boost::declval<(anonymous namespace)::Pod &>' is used but not defined in this translation  
      unit, and cannot be defined in any other translation unit because its type does not have linkage  
    typename add_rvalue_reference<T>::type declval() BOOST_NOEXCEPT; // as unevaluated operand  
  
boost/variant/detail/apply_visitor_unary.hpp:112:32: note:   
      used here  
        return vis()( boost::declval< value_t& >() );  
  
Attached 3x gzipped files   
test.cpp  
compile.sh  
compile.log  
  
To reproduce compile test.cpp with Xcode 10 or Xcode 10.1   
  
[compile.log.gz](https://github.com/boostorg/variant/files/2645469/compile.log.gz)  
[compile.sh.gz](https://github.com/boostorg/variant/files/2645470/compile.sh.gz)  
[test.cpp.gz](https://github.com/boostorg/variant/files/2645471/test.cpp.gz)  
  
  
  
  
Also reported as https://www.mail-archive.com/llvm-bugs@lists.llvm.org/msg20101.html   
File builds successfully with Xcode 9.4.1 and before   
  
Filed defect with Apple https://bugreport.apple.com/web/?problemID=46061262; Apple  responded as follows:  
  
The problem is that Boost.Variant incorrectly uses `boost::declval` in an evaluated context. The compiler correctly diagnoses the invalid usage. Basically, boost::declval is a never-defined function template. It is instantiated in a TU with a type that has internal linkage (i.e. defined in an anonymous namespace), and so the compiler can tell no definition of boost::declval can ever exist.

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-12-08 08:39:59 UTC  
> Url: https://github.com/boostorg/variant/issues/52#issuecomment-445442892  

This will be fixed by merging the https://github.com/boostorg/variant/pull/51 . The fix will hopefully land in Boost 1.70

---

## Comment 2

> Username: apolukhin  
> Created at: 2019-01-05 10:51:34 UTC  
> Url: https://github.com/boostorg/variant/issues/52#issuecomment-451645449  

#51 is merged now, closing this one as a dup.
