# #523 - Some headers don't compile independently [Closed]

> Username: tonyelewis  
> Created at: 2018-10-27 20:30:57 UTC  
> Updated at: 2023-07-25 19:56:13 UTC  
> Closed at: 2023-07-25 19:56:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/523  

Following on from [Trac#12393](https://svn.boost.org/trac10/ticket/12393)&hellip; some Geometry headers don't compile independently (under Clang) in violation of the [Boost header policy](https://www.boost.org/development/header.html):  
  
> Make sure that a translation unit consisting of just the contents of the header file will compile successfully.  
  
One example is `extensions/algebra/algorithms/reverse.hpp`:  
  
~~~no-highlight  
boost/geometry/extensions/algebra/algorithms/reverse.hpp:33:24: error: unknown type name 'vector_tag'; did you mean 'mpl::aux::vector_tag'?  
struct reverse<Vector, vector_tag>  
                       ^~~~~~~~~~  
                       mpl::aux::vector_tag  
/opt/include/boost/mpl/vector/aux_/tag.hpp:25:8: note: 'mpl::aux::vector_tag' declared here  
struct vector_tag;  
       ^  
boost/geometry/extensions/algebra/algorithms/reverse.hpp:42:19: error: use of undeclared identifier 'rotation_quaternion_tag'  
struct reverse<R, rotation_quaternion_tag>  
                  ^  
boost/geometry/extensions/algebra/algorithms/reverse.hpp:51:19: error: use of undeclared identifier 'rotation_matrix_tag'  
struct reverse<R, rotation_matrix_tag>  
                  ^  
3 errors generated.  
~~~  
  
Thanks.

---

## Comment 1

> Username: mloskot  
> Created at: 2018-10-28 19:57:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/523#issuecomment-433736212  

@tonyelewis Thanks for the report. Apparently, more headers need attention than just this one, see #525

---

## Comment 2

> Username: tonyelewis  
> Created at: 2018-10-29 11:43:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/523#issuecomment-433880328  

NP. Thanks for looking into this so quickly.
