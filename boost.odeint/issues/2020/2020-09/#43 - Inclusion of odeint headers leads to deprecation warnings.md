# #43 - Inclusion of odeint headers leads to deprecation warnings [Open]

> Username: Bihlerben  
> Created at: 2020-09-08 13:34:18 UTC  
> Updated at: 2020-11-22 07:39:51 UTC  
> Url: https://github.com/boostorg/odeint/issues/43  

When including #include <boost/numeric/odeint.hpp>, I get the following compilation warnings with g++ 10.2/mingw-w64 on Windows:  
  
`In file included from C:\Libraries\boost_1_74_0/boost/config/header_deprecated.hpp:18,  
                 from C:\Libraries\boost_1_74_0/boost/range/result_iterator.hpp:18,  
                 from C:\Libraries\boost_1_74_0/boost/range/metafunctions.hpp:20,  
                 from C:\Libraries\boost_1_74_0/boost/range.hpp:19,  
                 from C:\Libraries\boost_1_74_0/boost/numeric/odeint/util/resize.hpp:22,  
                 from C:\Libraries\boost_1_74_0/boost/numeric/odeint/util/state_wrapper.hpp:26,  
                 from C:\Libraries\boost_1_74_0/boost/numeric/odeint/util/ublas_wrapper.hpp:33,  
                 from C:\Libraries\boost_1_74_0/boost/numeric/odeint.hpp:25,  
                 from ..MyOwnFile.cpp  
C:\Libraries\boost_1_74_0/boost/range/result_iterator.hpp:20:1: note: '#pragma message: This header is deprecated. Use <boost/range/iterator.hpp> instead.'  
   20 | BOOST_HEADER_DEPRECATED("<boost/range/iterator.hpp>")  
      | ^~~~~~~~~~~~~~~~~~~~~~~  
C:\Libraries\boost_1_74_0/boost/range/const_reverse_iterator.hpp:20:1: note: '#pragma message: This header is deprecated. Use <boost/range/reverse_iterator.hpp> instead.'  
   20 | BOOST_HEADER_DEPRECATED("<boost/range/reverse_iterator.hpp>")  
      | ^~~~~~~~~~~~~~~~~~~~~~~  
C:\Libraries\boost_1_74_0/boost/range/reverse_result_iterator.hpp:20:1: note: '#pragma message: This header is deprecated. Use <boost/range/reverse_iterator.hpp> instead.'  
   20 | BOOST_HEADER_DEPRECATED("<boost/range/reverse_iterator.hpp>")  
      | ^~~~~~~~~~~~~~~~~~~~~~~`

---

## Comment 1

> Username: tueda  
> Created at: 2020-11-22 07:39:51 UTC  
> Url: https://github.com/boostorg/odeint/issues/43#issuecomment-731710882  

This is a problem of Boost.Range and will be fixed in Boost 1.75, https://github.com/boostorg/range/issues/106.
