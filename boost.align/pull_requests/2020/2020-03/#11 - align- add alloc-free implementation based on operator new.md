# #11 align: add alloc/free implementation based on operator new [Closed]

> Username: timblechmann  
> Created at: 2020-03-16 12:11:14 UTC  
> Updated at: 2020-04-30 12:40:51 UTC  
> Closed at: 2020-03-16 12:38:48 UTC  
> Url: https://github.com/boostorg/align/pull/11  

default and fallback implementation map `aligned_alloc` and  
`aligned_free` to malloc/free and the platform equivalents.  
  
however there are certain advantages when using the global  
operator `new`/`delete`, e.g. for applications that provide  
custom implementations of these operators. we therefore add a new  
implementation based on the c++ operators. when c++17 is available  
we directly map to alignment-aware operators, otherwise we use the  
trick of re-aligning un-aligned memory

---

## Comment 1

> Username: glenfe  
> Created_at: 2020-03-16 12:38:48 UTC  
> Url: https://github.com/boostorg/align/pull/11#issuecomment-599513014  

@timblechmann, Thanks Tim. I've implemented this in a different way: See 9af5171f7fe286a606f0a7d99964b69185dbe9e9.  
  
* Instead of just detecting C++17, which might be partial support, we detect whether compilers specifically support P0035R4 via the feature macro `__cpp_aligned_new`.  
* When we detect C++17 alignment aware dynamic allocation support we just defer to std::aligned_alloc which exists on all supported implementations.

---

## Comment 2

> Username: timblechmann  
> Created_at: 2020-03-16 12:48:44 UTC  
> Url: https://github.com/boostorg/align/pull/11#issuecomment-599517829  

@glenfe thanks for quickly looking into. however this doesn't help me with my current use case where i'm trying to wrap code that's using `alignment::aligned_alloc` and `alignment::aligned_allocator` to use my custom global `operator new`, `operator delete` (which in turn are based on mimalloc).  
  
the `new`/`delete` operator give me a hook into the allocator, for c-style memory allocators this isn't possible (i cannot use `LD_PRELOAD` hooks, since that would affect multiple DSOs).

---

## Comment 3

> Username: glenfe  
> Created_at: 2020-03-16 12:51:24 UTC  
> Url: https://github.com/boostorg/align/pull/11#issuecomment-599519046  

@timblechmann, Ah, I see. Maybe a general solution is to allow you/users to customize `aligned_allocator`? i.e. Control what allocation functions it uses.

---

## Comment 4

> Username: glenfe  
> Created_at: 2020-03-16 13:10:02 UTC  
> Url: https://github.com/boostorg/align/pull/11#issuecomment-599527062  

@timblechmann, would a solution where users can define `BOOST_ALIGN_ALLOCATE_USER` and then have to provide definitions of `boost::alignment::aligned_alloc` and `boost::alignment::aligned_free` work for you?  
  
If not, then instead of using `std::aligned_alloc` if that implementation deferred to this specific `operator new` overload, would that work for you:  
  
```  
void* operator new(std::size_t size, std::align_val_t alignment, const std::nothrow_t&);  
```   
  
i.e. The no-throw, non-array, alignment-aware allocation version.

---

## Comment 5

> Username: timblechmann  
> Created_at: 2020-03-16 13:23:21 UTC  
> Url: https://github.com/boostorg/align/pull/11#issuecomment-599533601  

i'd prefer to `operator new` (no-throw looks more reasonable), mainly because it composes. requiring the user to provide `boost::alignment::aligned_alloc` would also work, but complicates the downstream code a little: if my downstream code defines `BOOST_ALIGN_ALLOCATE_USER`, one *has* to define the functions (undefined symbols if not). if i define `BOOST_ALIGN_USE_NEW`, downstream code *can* override global `new`, but no matter if it's done or not, the code still work fine

---

## Comment 6

> Username: timblechmann  
> Created_at: 2020-04-30 07:27:25 UTC  
> Url: https://github.com/boostorg/align/pull/11#issuecomment-621662903  

@glenfe ping? i would still prefer to have something along the lines of my original PR

---

## Comment 7

> Username: glenfe  
> Created_at: 2020-04-30 12:17:17 UTC  
> Url: https://github.com/boostorg/align/pull/11#issuecomment-621796628  

@timblechmann affirmative - I haven't forgotten about it (was just waiting until the Boost release work was done before I start thinking about it again).  I'm still trying to think of a good customization point that suits your needs. I'll post something in the coming week.

---

## Comment 8

> Username: timblechmann  
> Created_at: 2020-04-30 12:40:51 UTC  
> Url: https://github.com/boostorg/align/pull/11#issuecomment-621808541  

:) thanks a lot!

---
