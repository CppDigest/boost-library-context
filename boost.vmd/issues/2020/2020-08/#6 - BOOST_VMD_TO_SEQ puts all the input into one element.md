# #6 - BOOST_VMD_TO_SEQ puts all the input into one element [Closed]

> Username: hirrolot  
> Created at: 2020-08-11 23:03:04 UTC  
> Updated at: 2020-08-14 21:15:24 UTC  
> Closed at: 2020-08-12 00:02:07 UTC  
> Url: https://github.com/boostorg/vmd/issues/6  

This code:  
  
```c  
BOOST_VMD_TO_SEQ(tree 59(56, BOOST_VMD_TYPE_SEQ)(128)(fire)(clown)(  
    47, (BOOST_VMD_TYPE_TUPLE, BOOST_PP_NIL)))  
```  
  
Produces  
  
```  
(  
tree 59(56, BOOST_VMD_TYPE_SEQ)(128)(fire)(clown)( 47, (BOOST_VMD_TYPE_TUPLE, BOOST_PP_NIL))  
)  
```  
  
But I expect the following output:  
  
```  
(tree)  
(59)  
( (56, BOOST_VMD_TYPE_SEQ) )  
( (128)(fire)(clown) )  
( ( 47, (BOOST_VMD_TYPE_TUPLE, BOOST_PP_NIL)) )  
```  
  
Do I misunderstand something or it's a bug?  
  
Boost/VMD has been downloaded from the latest `master`.

---

## Comment 1

> Username: eldiener  
> Created at: 2020-08-11 23:56:32 UTC  
> Url: https://github.com/boostorg/vmd/issues/6#issuecomment-672373125  

You need to register the identifiers:  
  
```  
#include <boost/vmd/to_seq.hpp>  
  
#define BOOST_VMD_REGISTER_tree (tree)  
#define BOOST_VMD_REGISTER_fire (fire)  
#define BOOST_VMD_REGISTER_clown (clown)  
#define BOOST_VMD_REGISTER_BOOST_PP_NIL (BOOST_PP_NIL)  
  
BOOST_VMD_TO_SEQ(tree 59(56, BOOST_VMD_TYPE_SEQ)(128)(fire)(clown)(  
    47, (BOOST_VMD_TYPE_TUPLE, BOOST_PP_NIL)))  
  
```  
  
should produce the output you expect. The type identifiers are always automatically registered for you. Maybe the VMD library should automatically register/detect BOOST_PP_NIL for the user, since it is used by the Boost PP library, but I had not considered that before.

---

## Comment 2

> Username: hirrolot  
> Created at: 2020-08-12 00:02:07 UTC  
> Url: https://github.com/boostorg/vmd/issues/6#issuecomment-672375458  

Got it.  
  
> Maybe the VMD library should automatically register/detect BOOST_PP_NIL for the user, since it is used by the Boost PP library, but I had not considered that before.  
  
Yeah, I think it would be a bit more convenient.

---

## Comment 3

> Username: eldiener  
> Created at: 2020-08-14 21:15:24 UTC  
> Url: https://github.com/boostorg/vmd/issues/6#issuecomment-674275303  

Actually BOOST_PP_NIL is registered/predetected as an identifier whenever you use any generic VMD macro and whenever you use the specific BOOST_VMD_IS_LIST macro. So in my example above the line:  
  
`#define BOOST_VMD_REGISTER_BOOST_PP_NIL (BOOST_PP_NIL)`  
  
is unnecessary. I should however document this.
