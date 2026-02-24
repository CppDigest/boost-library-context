# #66 Add a new value_init test, change implementation to not depend on TypeTraits [Merged]

> Username: pdimov  
> Created at: 2020-05-25 02:12:33 UTC  
> Updated at: 2020-05-27 09:51:52 UTC  
> Merged at: 2020-05-27 09:51:46 UTC  
> Closed at: 2020-05-27 09:51:46 UTC  
> Url: https://github.com/boostorg/utility/pull/66  

As suggested in https://github.com/boostorg/core/issues/76.

---

## Comment 1

> Username: Lastique  
> Created_at: 2020-05-25 18:54:59 UTC  
> Url: https://github.com/boostorg/utility/pull/66#issuecomment-633684543  

Though this will likely work, isn't using `memset` on a half-constructed non-POD object UB?

---

## Comment 2

> Username: pdimov  
> Created_at: 2020-05-25 19:03:15 UTC  
> Url: https://github.com/boostorg/utility/pull/66#issuecomment-633686586  

Interesting question. I'm not sure what would make it UB.

---

## Comment 3

> Username: Lastique  
> Created_at: 2020-05-25 19:56:34 UTC  
> Url: https://github.com/boostorg/utility/pull/66#issuecomment-633699854  

Basically, object representation of non-trivial and non-standard-layout types is implementation-defined, so modifying it with `memset` and friends is UB. [intro.object]/8 doesn't even guarantee that such objects are contiguous.  
  
In practice, though, `zero_init` and `initialized` will likely have the natural contiguous layout with no hidden members like vtable pointer etc.

---

## Comment 4

> Username: Lastique  
> Created_at: 2020-05-25 20:00:32 UTC  
> Updated_at: 2020-05-25 20:01:39 UTC  
> Url: https://github.com/boostorg/utility/pull/66#issuecomment-633700849  

You could probably `memset` `data_` in `zero_init` constructor as that would be zeroing the storage of that specific member before it is constructed. That would not affect any other possible representation bits of `zero_init` or `initialized`.

---

## Comment 5

> Username: Kojoley  
> Created_at: 2020-05-25 20:05:51 UTC  
> Url: https://github.com/boostorg/utility/pull/66#issuecomment-633702147  

Writing anything to anything is not UB, reading after it may. One may be concerned about virtual classes, but the standard is worded in a way that the virtual pointer is actually written after user constructor is called. UBSAN (of both Clang and GCC) is also quite about the trick.

---

## Comment 6

> Username: Kojoley  
> Created_at: 2020-05-25 20:15:12 UTC  
> Url: https://github.com/boostorg/utility/pull/66#issuecomment-633704335  

@zygoloid could you take a look and judge whether the code contains UB, please?

---

## Comment 7

> Username: Lastique  
> Created_at: 2020-05-25 20:15:56 UTC  
> Updated_at: 2020-05-25 20:20:45 UTC  
> Url: https://github.com/boostorg/utility/pull/66#issuecomment-633704506  

> the standard is worded in a way that the virtual pointer is actually written after user constructor is called  
  
Given that the virtual function is called in the class that is being constructed/destructed, I'd say the vtable pointer must be written before that construction/destruction begins. [class.cdtor]/4

---

## Comment 8

> Username: Kojoley  
> Created_at: 2020-05-25 20:36:19 UTC  
> Url: https://github.com/boostorg/utility/pull/66#issuecomment-633709410  

Anyway it should happen only after `zero_init` constructor is done. Again, UBSAN is fine with:  
```cpp  
#include <cstring>  
  
struct foo  
{  
    foo() : i(get() + 42) { }  
    virtual ~foo() {}  
    virtual int get() const { return 42; }  
    int i;  
};  
  
struct bar : virtual foo  
{  
    bar() {}  
    int get() const override { return i; }  
};  
  
struct zero_init  
{  
    zero_init( void * p, std::size_t n )  
    {  
        std::memset( p, 0, n );  
    }  
};  
  
template<class T> class initialized: private zero_init  
{  
private:  
  
    T t_;  
  
public:  
  
    initialized(): zero_init( this, sizeof(*this) ), t_()  
    {  
    }  
  
    T const& get() const noexcept  
    {  
        return t_;  
    }  
};  
  
  
int main()  
{  
    initialized<bar> i;  
    return i.get().get();  
}  
```

---

## Comment 9

> Username: pdimov  
> Created_at: 2020-05-25 20:39:36 UTC  
> Url: https://github.com/boostorg/utility/pull/66#issuecomment-633710251  

> Given that the virtual function is called in the class that is being constructed/destructed  
  
I don't think they are.

---

## Comment 10

> Username: pdimov  
> Created_at: 2020-05-25 20:40:20 UTC  
> Url: https://github.com/boostorg/utility/pull/66#issuecomment-633710431  

> You could probably `memset` `data_` in `zero_init` constructor as that would be zeroing the storage of that specific member before it is constructed.  
  
This is a good idea, thanks.

---

## Comment 11

> Username: Lastique  
> Created_at: 2020-05-25 20:42:12 UTC  
> Url: https://github.com/boostorg/utility/pull/66#issuecomment-633710862  

> I don't think they are.  
  
They are, see [[class.cdtor]/4](http://eel.is/c++draft/class.cdtor#4).

---

## Comment 12

> Username: Kojoley  
> Created_at: 2020-05-25 20:42:23 UTC  
> Url: https://github.com/boostorg/utility/pull/66#issuecomment-633710905  

> > You could probably memset data_ in zero_init constructor as that would be zeroing the storage of that specific member before it is constructed.  
>  
> This is a good idea, thanks.  
  
It gives no additional safety but requires `addressof`

---

## Comment 13

> Username: Lastique  
> Created_at: 2020-05-25 20:46:36 UTC  
> Url: https://github.com/boostorg/utility/pull/66#issuecomment-633711866  

@Kojoley I think you may have misunderstood my point. The problem is not the representation of `bar` in your example. It's that of `initialized` and `zero_init`. The standard doesn't guarantee anything about them and in particular allows the implementation to add hidden members in them or do any kind of funny stuff.

---

## Comment 14

> Username: pdimov  
> Created_at: 2020-05-25 20:55:51 UTC  
> Url: https://github.com/boostorg/utility/pull/66#issuecomment-633713881  

> They are, see [[class.cdtor]/4](http://eel.is/c++draft/class.cdtor#4).  
  
In the constructor of the derived class, virtual calls resolve to that derived class. In the constructor of the base class (`zero_init` in our case), virtual calls resolve to that base class. https://godbolt.org/z/a21ZTQ  
  
It follows that the vptr is initialized to point at the derived class vtable after the base class constructor completes, i.e. after the memset.  
  
Anyway, I've updated the PR to memset `data_` instead of `*this`, which is more correct in either case.

---

## Comment 15

> Username: Kojoley  
> Created_at: 2020-05-25 20:58:09 UTC  
> Url: https://github.com/boostorg/utility/pull/66#issuecomment-633714380  

> The standard doesn't guarantee anything about them and in particular allows the implementation to add hidden members in them or do any kind of funny stuff.  
  
I do not understand why does it matter, invoking constructor on uninitialized memory would had the same problem in that case.

---

## Comment 16

> Username: Lastique  
> Created_at: 2020-05-25 21:02:53 UTC  
> Updated_at: 2020-05-25 21:08:26 UTC  
> Url: https://github.com/boostorg/utility/pull/66#issuecomment-633715502  

> In the constructor of the derived class, virtual calls resolve to that derived class. In the constructor of the base class (`zero_init` in our case), virtual calls resolve to that base class.  
  
Yes, exactly.  
  
The `memset` overwrites `zero_init` representation as well as `initialized`, which means if the implementation had some hidden member in `zero_init` it would have been corrupted. Whether that would be a problem or not would depend on how that member would have been used by the implementation. For example, it could have used it before calling `initialized` constructor.  
  
> Anyway, I've updated the PR to memset `data_` instead of `*this`, which is more correct in either case.  
  
Great, thanks!

---

## Comment 17

> Username: pdimov  
> Created_at: 2020-05-25 21:16:21 UTC  
> Url: https://github.com/boostorg/utility/pull/66#issuecomment-633718549  

There's still one potentially problematic case,  
```  
struct X  
{  
    int a;  
    char b;  
};  
  
struct Y: value_initialized<X>  
{  
    char c = 42;  
};  
```  
where the `memset` may overwrite `c` which could have been laid out in the padding of `X` and initialized before calling the constructor of `zero_init`. I'm not sure if that's a problem in practice though.

---

## Comment 18

> Username: Lastique  
> Created_at: 2020-05-25 21:45:58 UTC  
> Url: https://github.com/boostorg/utility/pull/66#issuecomment-633724624  

I think this should not happen if `X` is stored as a member in `value_initialized` (which it is). `c` can only be stored in the tail padding if `X` is potentially overlapping (i.e. a base class or marked with `[[no_unique_address]]`).

---

## Review 19 [Commented]

> Username: Lastique  
> Created_at: 2020-05-25 22:23:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/utility/pull/66#pullrequestreview-417890085  

📁 test/value_init_test3.cpp

```diff
   8 |+ #include <boost/config/pragma_message.hpp>
   9 |+ 
  10 |+ #if __cplusplus >= 201103L || ( defined(BOOST_MSVC) && BOOST_MSVC >= 1900 )
```

> Username: Lastique  
> Created_at: 2020-05-25 22:23:16 UTC  
> Url: https://github.com/boostorg/utility/pull/66#discussion_r430087385  

Doesn't `BOOST_NO_CXX14_AGGREGATE_NSDMI` cover this?

> Username: pdimov  
> Created_at: 2020-05-26 00:40:02 UTC  
> Updated_at: 2020-05-26 00:40:03 UTC  
> Url: https://github.com/boostorg/utility/pull/66#discussion_r430106341  

No.

> Username: pdimov  
> Created_at: 2020-05-26 21:34:18 UTC  
> Url: https://github.com/boostorg/utility/pull/66#discussion_r430720768  

BOOST_NO_CXX14_AGGREGATE_NSDMI is about the C++14 feature, the equivalent of __cpp_aggregate_nsdmi. What I need here is the nonexistent macro about the C++11 feature, the equivalent of __cpp_nsdmi. No idea why we have the former in Config, but not the latter.

> Username: Lastique  
> Created_at: 2020-05-27 09:49:43 UTC  
> Updated_at: 2020-05-27 09:49:44 UTC  
> Url: https://github.com/boostorg/utility/pull/66#discussion_r430995113  

Ok, thanks. Maybe we should add one.


---
