# #93 - Redundant condition [Closed]

> Username: PHackett  
> Created at: 2023-02-28 12:37:32 UTC  
> Updated at: 2025-01-02 12:27:47 UTC  
> Closed at: 2025-01-02 12:27:47 UTC  
> Url: https://github.com/boostorg/format/issues/93  

https://github.com/boostorg/format/blob/78ef371d2d90462671b90c3af407fae07820b193/include/boost/format/alt_sstream_impl.hpp#L127  
  
Line 127 is -   
    else if(way == ::std::ios_base::beg)  
  
It is followed on line 129 -  
    else if(way != ::std::ios_base::beg)  
  
Which, of course is always true given the previous test. This "impossible-redundant-condition" was spotted by a source analyser I used.

---

## Comment 1

> Username: jeking3  
> Created at: 2024-05-15 17:17:35 UTC  
> Url: https://github.com/boostorg/format/issues/93#issuecomment-2113065090  

I think the proper solution here is likely the following on lines 129, 130:  
  
```  
                else if(way != ::std::ios_base::cur || (which & ::std::ios_base::in) )  
                    // (altering in&out is only supported if way is beg or end, not cur)  
```  
  
Similar to the logic on lines 110, 111:  
```  
                else if(way != ::std::ios_base::cur || (which & ::std::ios_base::out) )  
                    // (altering in&out is only supported if way is beg or end, not cur)  
```

---

## Comment 2

> Username: jeking3  
> Created at: 2024-06-04 21:34:07 UTC  
> Url: https://github.com/boostorg/format/issues/93#issuecomment-2148452990  

@PHackett any thoughts on this approach?

---

## Comment 3

> Username: PHackett  
> Created at: 2024-06-05 07:45:08 UTC  
> Url: https://github.com/boostorg/format/issues/93#issuecomment-2149108493  

This was spotted by a static code analyser in an old version of boost - it was fixed in a later version.  
I suggest that you look at newer versions of the library to see what fix was applied.

---

## Comment 4

> Username: jeking3  
> Created at: 2025-01-02 11:49:28 UTC  
> Url: https://github.com/boostorg/format/issues/93#issuecomment-2567656229  

It actually wasn't fixed, but what I suggested was too complicated.  Thanks.
