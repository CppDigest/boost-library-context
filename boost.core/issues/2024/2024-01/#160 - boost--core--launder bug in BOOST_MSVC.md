# #160 - boost::core::launder bug in BOOST_MSVC [Closed]

> Username: alandefreitas  
> Created at: 2024-01-04 00:36:18 UTC  
> Updated at: 2024-01-04 03:21:55 UTC  
> Closed at: 2024-01-04 03:21:55 UTC  
> Url: https://github.com/boostorg/core/issues/160  

https://github.com/boostorg/optional/commit/8375df7693340c4182121b9df8cfe8199e65eb33 included a workaround for a problem related to boost::core::launder. The problem was reported at https://github.com/boostorg/optional/issues/122.  
  
The problem happens due to an MSVC bug and it would be best to bypass MSVC directly in Boost.Core than to have this logic replicated in other libraries.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-01-04 01:10:47 UTC  
> Url: https://github.com/boostorg/core/issues/160#issuecomment-1876176368  

To clarify, the problem is an internal compiler error with `toolset=msvc-14.1 cxxstd=17 variant=release address-model=32`, caused by the use of `boost::core::launder` (which calls `std::launder` in this case).

---

## Comment 2

> Username: Lastique  
> Created at: 2024-01-04 01:27:42 UTC  
> Url: https://github.com/boostorg/core/issues/160#issuecomment-1876186506  

Perhaps, it could be emulated with `__asm { nop ptr };` or something?

---

## Comment 3

> Username: pdimov  
> Created at: 2024-01-04 01:28:47 UTC  
> Url: https://github.com/boostorg/core/issues/160#issuecomment-1876187169  

I don't think this is what it does.

---

## Comment 4

> Username: Lastique  
> Created at: 2024-01-04 01:36:28 UTC  
> Url: https://github.com/boostorg/core/issues/160#issuecomment-1876191558  

I think, something like this should have the effect of `std::launder`:  
  
```  
template< typename T >  
T* launder(T* ptr)  
{  
    T* res;  
    __asm  
    {  
        mov eax, ptr  
        mov res, eax  
    };  
    return res;  
}  
```  
  
But that's two instructions. I suggested a `nop` because it may be enough for the compiler to stop assuming anything about `ptr`, and that's just one instruction. Sure, ideally, there should be zero instructions, though.

---

## Comment 5

> Username: pdimov  
> Created at: 2024-01-04 01:43:02 UTC  
> Url: https://github.com/boostorg/core/issues/160#issuecomment-1876195791  

MSVC doesn't perform the optimizations against which `std::launder` guards, so the correct emulation of it today is a no-op.  
  
If it starts doing them some day, and for some reason `std::launder` doesn't work on that hypothetical future version only in 32 bit mode, maybe the above will work.
