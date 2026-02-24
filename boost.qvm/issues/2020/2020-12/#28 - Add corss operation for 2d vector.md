# #28 - Add corss operation for 2d vector [Closed]

> Username: eryar  
> Created at: 2020-12-14 14:51:56 UTC  
> Updated at: 2020-12-19 08:23:21 UTC  
> Closed at: 2020-12-19 07:21:51 UTC  
> Url: https://github.com/boostorg/qvm/issues/28  

Hello,  
I find that the cross operation only for 3d vector:  
```c++  
namespace boost { namespace qvm {  
  
    //Only enabled if:  
    //  is_vec<A>::value && is_vec<B>::value &&  
    //  vec_traits<A>::dim==3 && vec_traits<B>::dim==3  
    template <class A,class B>  
    typename deduce_vec2<A,B,3>::type  
    cross( A const & a, B const & b );  
  
} }  
```  
cross for 2d vector is also useful for check parallel.  
  
Best Regards,

---

## Comment 1

> Username: zajo  
> Created at: 2020-12-14 18:18:28 UTC  
> Url: https://github.com/boostorg/qvm/issues/28#issuecomment-744620701  

On Mon, Dec 14, 2020 at 6:52 AM Shing Liu <notifications@github.com> wrote:  
>  
> Hello,  
> I find that the cross operation only for 3d vector:  
>  
> namespace boost { namespace qvm {  
>  
>     //Only enabled if:  
>     //  is_vec<A>::value && is_vec<B>::value &&  
>     //  vec_traits<A>::dim==3 && vec_traits<B>::dim==3  
>     template <class A,class B>  
>     typename deduce_vec2<A,B,3>::type  
>     cross( A const & a, B const & b );  
>  
> } }  
>  
> cross for 2d vector is also useful for check parallel.  
  
I thought that in math cross product is only defined for 3D vectors, but  
today I learned (  
https://www.gamedev.net/forums/topic/289972-cross-product-of-2d-vectors/2828289/)  
that there are "analogs" for other dimensions, e.g. for 2D we could  
use (U.x*V.y-U.y*V.x). Is this what you had in mind?

---

## Comment 2

> Username: eryar  
> Created at: 2020-12-15 01:14:51 UTC  
> Url: https://github.com/boostorg/qvm/issues/28#issuecomment-744946528  

Yes, I mean the "analogs" for 2d vector, use (U.x*V.y-U.y*V.x).  
The cross product result of two 3d vector is a vector,   
but the "analogs" for 2d vector is a scalar type.

---

## Comment 3

> Username: zajo  
> Created at: 2020-12-19 07:21:51 UTC  
> Url: https://github.com/boostorg/qvm/issues/28#issuecomment-748433698  

See https://github.com/boostorg/qvm/blob/master/include/boost/qvm/vec_operations.hpp#L147.

---

## Comment 4

> Username: eryar  
> Created at: 2020-12-19 08:23:20 UTC  
> Url: https://github.com/boostorg/qvm/issues/28#issuecomment-748440777  

Great! Thank you.
