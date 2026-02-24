# #70 Remove BOOST_CONSTEXPR on BOOST_FUSION_DEFINE_STRUCT  [Merged]

> Username: daminetreg  
> Created at: 2015-04-21 07:06:41 UTC  
> Updated at: 2015-04-26 20:35:47 UTC  
> Merged at: 2015-04-21 07:22:18 UTC  
> Closed at: 2015-04-21 07:22:19 UTC  
> Url: https://github.com/boostorg/fusion/pull/70  

Remove BOOST_CONSTEXPR on BOOST_FUSION_DEFINE_STRUCT constructors as it however adds the requirement that the user provided types are constant enabled.  
  
Relates to https://svn.boost.org/trac/boost/ticket/11211

---

## Comment 1

> Username: Flast  
> Created_at: 2015-04-21 07:39:23 UTC  
> Url: https://github.com/boostorg/fusion/pull/70#issuecomment-94673042  

Thanks @daminetreg !  
  
I didn't check other variations but is this enough?

---

## Comment 2

> Username: daminetreg  
> Created_at: 2015-04-21 07:44:27 UTC  
> Url: https://github.com/boostorg/fusion/pull/70#issuecomment-94675047  

Compiled for me with the example of the list. Perhaps we have more work somewhere.

---

## Comment 3

> Username: daminetreg  
> Created_at: 2015-04-21 07:44:41 UTC  
> Url: https://github.com/boostorg/fusion/pull/70#issuecomment-94675225  

But I couldn't find it out.

---

## Comment 4

> Username: daminetreg  
> Created_at: 2015-04-21 08:58:14 UTC  
> Url: https://github.com/boostorg/fusion/pull/70#issuecomment-94710685  

@Flast I've let constexpr for empty struct, do you also think it's ok ? On line 290 https://github.com/boostorg/fusion/blob/master/include/boost/fusion/adapted/struct/detail/define_struct.hpp#L290  
  
I think BOOST_FUSION_DEFINE_STRUCT_INLINE could also have problems.

---

## Comment 5

> Username: Flast  
> Created_at: 2015-04-21 15:13:02 UTC  
> Url: https://github.com/boostorg/fusion/pull/70#issuecomment-94834450  

> I've let constexpr for empty struct, do you also think it's ok ?  
  
It seems good.  
  
> I think BOOST_FUSION_DEFINE_STRUCT_INLINE could also have problems.  
  
Sorry, I'm moving now and I can't ensure duration for investigating...

---
