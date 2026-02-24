# #103 Add value member to allocator_is_always_equal [Closed]

> Username: edsavage  
> Created at: 2021-12-01 15:25:37 UTC  
> Updated at: 2021-12-01 22:49:39 UTC  
> Closed at: 2021-12-01 17:10:16 UTC  
> Url: https://github.com/boostorg/core/pull/103  

In order to use `boost::allocator_is_always_equal` with e.g.  
`boost::unordered_set` a `value` member is required to exist in the  
struct, else a compilation error is encountered:  
  
```  
C:\usr\local\include\boost-1_77\boost/unordered/unordered_set.hpp(170,1): error C2039: 'value': is not a member of 'boost::allocator_is_always_equal<Alloc,void>'  
        with  
        [  
            Alloc=std::allocator<std::string>  
        ]  
        BOOST_NOEXCEPT_IF(value_allocator_traits::is_always_equal::value&&  
```  
  
Encountered with boost version 1.77, on OS Windows Server 2016 with  
compiler version MSVC 19.29.30136 for x64

---

## Comment 1

> Username: Lastique  
> Created_at: 2021-12-01 15:32:24 UTC  
> Updated_at: 2021-12-01 15:58:50 UTC  
> Url: https://github.com/boostorg/core/pull/103#issuecomment-983757537  

`value` must have an initializer. And its type should presumably be `bool`, not `type`.

---

## Comment 2

> Username: Lastique  
> Created_at: 2021-12-01 15:33:34 UTC  
> Updated_at: 2021-12-01 15:35:03 UTC  
> Url: https://github.com/boostorg/core/pull/103#issuecomment-983758770  

Also, if you're going to add it, it must be added to all code branches. And while we're at it, we might as well add it to the other traits.

---

## Comment 3

> Username: pdimov  
> Created_at: 2021-12-01 15:35:48 UTC  
> Url: https://github.com/boostorg/core/pull/103#issuecomment-983760790  

The documentation for `allocator_is_always_equal` clearly states that it only has `type`, so the error must be on the unordered side. What is the code that gives the error?

---

## Comment 4

> Username: edsavage  
> Created_at: 2021-12-01 15:36:57 UTC  
> Updated_at: 2021-12-01 15:39:46 UTC  
> Url: https://github.com/boostorg/core/pull/103#issuecomment-983762124  

> The documentation for `allocator_is_always_equal` clearly states that it only has `type`, so the error must be on the unordered side. What is the code that gives the error?  
  
```  
C:\usr\local\include\boost-1_77\boost/unordered/unordered_set.hpp(170,1): error C2039: 'value': is not a member of 'boost::allocator_is_always_equal<Alloc,void>'  
        with  
        [  
            Alloc=std::allocator<std::string>  
        ]  
        BOOST_NOEXCEPT_IF(value_allocator_traits::is_always_equal::value&&  
^  
C:\usr\local\include\boost-1_77\boost/unordered/detail/implementation.hpp(1474): note: see declaration of 'boost::allocator_is_always_equal<Alloc,void>'  
        with  
        [  
            Alloc=std::allocator<std::string>  
        ]  
        typedef boost::allocator_is_always_equal<Alloc> is_always_equal;  
C:\usr\local\include\boost-1_77\boost/unordered/unordered_set.hpp(170,1): error C2039: 'value': is not a member of 'boost::allocator_is_always_equal<Alloc,void>'  
        with  
        [  
            Alloc=std::allocator<std::string>  
        ]  
        BOOST_NOEXCEPT_IF(value_allocator_traits::is_always_equal::value&&  
^  
C:\usr\local\include\boost-1_77\boost/unordered/detail/implementation.hpp(1474): note: see declaration of 'boost::allocator_is_always_equal<Alloc,void>'  
        with  
        [  
            Alloc=std::allocator<std::string>  
        ]  
        typedef boost::allocator_is_always_equal<Alloc> is_always_equal;  
```  
  
so stemming from  
  
```  
#if !defined(BOOST_NO_CXX11_RVALUE_REFERENCES)  
      unordered_set& operator=(unordered_set&& x)  
        BOOST_NOEXCEPT_IF(value_allocator_traits::is_always_equal::value&&  
            boost::is_nothrow_move_assignable<H>::value&&  
              boost::is_nothrow_move_assignable<P>::value)  
      {      
        table_.move_assign(x.table_, boost::unordered::detail::true_type());  
        return *this;  
      }      
#endif  
```  
in `unordered_set.hpp` line 170

---

## Comment 5

> Username: pdimov  
> Created_at: 2021-12-01 15:39:23 UTC  
> Url: https://github.com/boostorg/core/pull/103#issuecomment-983764341  

That's the error message itself but what's the source code that gives it?

---

## Comment 6

> Username: edsavage  
> Created_at: 2021-12-01 15:42:07 UTC  
> Updated_at: 2021-12-01 15:42:36 UTC  
> Url: https://github.com/boostorg/core/pull/103#issuecomment-983767083  

> That's the error message itself but what's the source code that gives it?  
  
Sorry, I added the source code in an edit to the above comment.

---

## Comment 7

> Username: pdimov  
> Created_at: 2021-12-01 15:43:02 UTC  
> Updated_at: 2021-12-01 15:43:23 UTC  
> Url: https://github.com/boostorg/core/pull/103#issuecomment-983768037  

I'm trying  
```  
#include <boost/unordered_set.hpp>  
  
int main()  
{  
    boost::unordered_set<int> s1;  
    boost::unordered_set<int> s2;  
    s2 = std::move( s1 );  
}  
```  
but it seems to work fine.

---

## Comment 8

> Username: pdimov  
> Created_at: 2021-12-01 15:49:37 UTC  
> Url: https://github.com/boostorg/core/pull/103#issuecomment-983774711  

I'm not sure where `boost::allocator_is_always_equal` is even coming from, because as far as I can see Unordered doesn't use it (yet).

---

## Comment 9

> Username: edsavage  
> Created_at: 2021-12-01 15:55:55 UTC  
> Url: https://github.com/boostorg/core/pull/103#issuecomment-983780433  

@pdimov Yes, that is the case at the moment. But in an effort to quiet a deprecation warning related to the use of `std::allocator::is_always_equal` (see https://github.com/boostorg/unordered/pull/52) I've made use of `boost::allocator_is_always_equal`. This was initially suggested on a mailing list a while back - https://groups.google.com/g/boost-developers-archive/c/3MOWuQgEEy4/m/boIwsJsdBAAJ  
 but never followed up.

---

## Comment 10

> Username: pdimov  
> Created_at: 2021-12-01 16:38:07 UTC  
> Url: https://github.com/boostorg/core/pull/103#issuecomment-983820896  

Try replacing  
```  
        typedef BOOST_UNORDERED_DEFAULT_TYPE(std::allocator_traits<Alloc>,  
          is_always_equal,  
          BOOST_UNORDERED_DEFAULT_TYPE(Alloc, is_always_equal,  
            typename boost::is_empty<Alloc>::type)) is_always_equal;  
```  
with  
```  
        typedef typename boost::allocator_is_always_equal<Alloc>::type is_always_equal;  
````  
instead.

---

## Comment 11

> Username: edsavage  
> Created_at: 2021-12-01 16:54:02 UTC  
> Updated_at: 2021-12-01 17:09:05 UTC  
> Url: https://github.com/boostorg/core/pull/103#issuecomment-983834572  

Thanks @pdimov , trying that now..  
  
Update: The suggested change by @pdimov has fixed the compilation error encountered in  https://github.com/boostorg/unordered/pull/52. I'll close this PR and update that one accordingly. Thanks all!

---

## Comment 12

> Username: edsavage  
> Created_at: 2021-12-01 17:10:16 UTC  
> Url: https://github.com/boostorg/core/pull/103#issuecomment-983850316  

Closing as a better solution has been found.

---

## Comment 13

> Username: Lastique  
> Created_at: 2021-12-01 18:37:20 UTC  
> Url: https://github.com/boostorg/core/pull/103#issuecomment-983946368  

Is there a rationale for not having the `value` member? This is unlike other type traits with a value result.

---

## Comment 14

> Username: pdimov  
> Created_at: 2021-12-01 18:42:23 UTC  
> Url: https://github.com/boostorg/core/pull/103#issuecomment-983950444  

This is not a trait with a value result.

---

## Comment 15

> Username: Lastique  
> Created_at: 2021-12-01 19:47:49 UTC  
> Url: https://github.com/boostorg/core/pull/103#issuecomment-983998522  

`allocator_is_always_equal` is a yes or no trait, its `type` member is supposed to have the `value` member. Realistically, it is `true_type` or `false_type` or something that looks like one. It might as well have a value result instead. Same with `allocator_propagate_*` traits.

---

## Comment 16

> Username: pdimov  
> Created_at: 2021-12-01 19:52:26 UTC  
> Url: https://github.com/boostorg/core/pull/103#issuecomment-984001589  

No, `allocator_is_always_equal<A>` is a type transformation trait that returns `A::is_always_equal`, similarly to how `allocator_size_type<A>` is a type transformation trait that returns `A::size_type`.

---

## Comment 17

> Username: Lastique  
> Created_at: 2021-12-01 19:57:39 UTC  
> Url: https://github.com/boostorg/core/pull/103#issuecomment-984005246  

I realize that's how it works now. I'm saying it would make more sense if it was a value trait and gave the result right away rather than require you to use an extra layer of indirection.

---

## Comment 18

> Username: pdimov  
> Created_at: 2021-12-01 20:19:01 UTC  
> Url: https://github.com/boostorg/core/pull/103#issuecomment-984022156  

`allocator_is_always_equal_t<A>` is the value trait that gives the result straight away.

---

## Comment 19

> Username: glenfe  
> Created_at: 2021-12-01 22:49:39 UTC  
> Url: https://github.com/boostorg/core/pull/103#issuecomment-984128921  

The pull request is wrong, because I don't want these types to have a `value`.  They are type traits which give you the equivalent of `allocator_traits<A>::facility` (which give you types - `Alloc::facility` if it exists, or some other type).

---
