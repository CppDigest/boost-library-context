# #17 - any_collection fails to compile when boost::any is included [Closed]

> Username: schaumb  
> Created at: 2020-06-09 12:36:47 UTC  
> Updated at: 2020-06-09 18:24:37 UTC  
> Closed at: 2020-06-09 18:24:37 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/17  

Minimal code repro  
```  
#include <boost/any.hpp>  
#include <boost/poly_collection/any_collection.hpp>  
boost::poly_collection::any_collection<boost::type_erasure::destructible<>> collection;  
```  
When I want to compile these lines, the compiler says:  
```  
In file included from /opt/wandbox/boost-1.73.0/gcc-9.3.0/include/boost/poly_collection/any_collection.hpp:17,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-9.3.0/include/boost/poly_collection/detail/any_model.hpp: In static member function 'static boost::poly_collection::detail::any_model<Concept>::value_type boost::poly_collection::detail::any_model<Concept>::make_value_type(boost::type_erasure::any<Concept2, Tag2>&)':  
/opt/wandbox/boost-1.73.0/gcc-9.3.0/include/boost/poly_collection/detail/any_model.hpp:187:20: error: reference to 'any' is ambiguous  
  187 |     using ref_type=any<Concept2,T>;  
      |                    ^~~  
In file included from prog.cc:1:  
/opt/wandbox/boost-1.73.0/gcc-9.3.0/include/boost/any.hpp:35:11: note: candidates are: 'class boost::any'  
   35 |     class any  
      |           ^~~  
In file included from /opt/wandbox/boost-1.73.0/gcc-9.3.0/include/boost/type_erasure/any.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-9.3.0/include/boost/type_erasure/any_cast.hpp:24,  
                 from /opt/wandbox/boost-1.73.0/gcc-9.3.0/include/boost/poly_collection/detail/any_iterator.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-9.3.0/include/boost/poly_collection/detail/any_model.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-9.3.0/include/boost/poly_collection/any_collection.hpp:17,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-9.3.0/include/boost/type_erasure/detail/access.hpp:33:7: note:                 'template<class Concept, class T> class boost::type_erasure::any'  
   33 | class any;  
      |       ^~~  
```  
  
The current version is the same as 1.73, the problem is [here](https://github.com/boostorg/poly_collection/blob/develop/include/boost/poly_collection/detail/any_model.hpp#L187).   
  
The `type_erasure::` prefix solves this issue, but who knows what else can cause.

---

## Comment 1

> Username: joaquintides  
> Created at: 2020-06-09 17:58:12 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/17#issuecomment-641477984  

Hi Bela, thanks for reporting. Could you check if c00e7bcf9deece68e5c69a0ba31fb77145b775bb fixes the problem?

---

## Comment 2

> Username: schaumb  
> Created at: 2020-06-09 18:16:30 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/17#issuecomment-641487134  

This commit fixes the problem. Thanks.  
  
What about the comment above the changed code?   
```  
    /* I don't pretend to understand what's going on here, see  
     * https://lists.boost.org/boost-users/2017/05/87556.php  
     */  
```  
This is relevant/needed?

---

## Comment 3

> Username: joaquintides  
> Created at: 2020-06-09 18:17:53 UTC  
> Updated at: 2020-06-09 18:19:04 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/17#issuecomment-641487820  

This is a note to myself in case I need to recall what the lines below do. Nothing to do with the ambiguity problem.

---

## Comment 4

> Username: schaumb  
> Created at: 2020-06-09 18:23:59 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/17#issuecomment-641491292  

I think this ticket can be closed now :)  
Thanks for the information!
