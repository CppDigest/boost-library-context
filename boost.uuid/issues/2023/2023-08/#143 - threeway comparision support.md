# #143 - threeway comparision support [Closed]

> Username: timblechmann  
> Created at: 2023-08-02 02:09:17 UTC  
> Updated at: 2024-04-24 00:22:53 UTC  
> Closed at: 2024-04-24 00:22:53 UTC  
> Url: https://github.com/boostorg/uuid/issues/143  

it would be great if `operator <=>` would be supported

---

## Comment 1

> Username: pdimov  
> Created at: 2024-04-23 14:55:23 UTC  
> Url: https://github.com/boostorg/uuid/issues/143#issuecomment-2072577827  

@Lastique As far as I can see, the implementation of `operator<=>` for SSE2 would be line by line identical to `operator<`, with the final line replaced with  
```  
    return cmp <=> rcmp;  
```  
but I'm leaving the actual change to you. (I suppose you'd want to refactor the common part out.)  
  
I haven't yet decided to definitively drop msvc-12.0 so let's not remove the unaligned load workarounds yet (although the `< 1600` part can certainly be dropped.)

---

## Comment 2

> Username: Lastique  
> Created at: 2024-04-23 15:17:28 UTC  
> Url: https://github.com/boostorg/uuid/issues/143#issuecomment-2072663821  

@pdimov Did you intentionally disable SSE2 implementation [here](https://github.com/boostorg/uuid/blob/1c6e52721ff8ae8705ce4a422de67211532c62f7/include/boost/uuid/uuid.hpp#L222-L225), when `uint128_t` is available? The change was made in https://github.com/boostorg/uuid/commit/b3e3a59a9869eb70fb089dbb88b4e29fe7c91c5e.  
  
I would prefer SSE2 to be used over `uint128_t`, unless the latter is actually faster.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-04-23 15:43:39 UTC  
> Url: https://github.com/boostorg/uuid/issues/143#issuecomment-2072767508  

It was intentional, yes. I think we should err on the side of trusting the compilers to produce whatever codegen they believe is optimal for `__uint128_t`, as this would automatically take advantage of improvements they implement, and better reflect the target instruction set.  
  
I've looked at the generated code, and everything seems OK when using `__uint128_t`; even `operator<` looks to my naked eye not that bad compared to your SSE2 implementation.  
  
I think that the benefit we gain from just delegating the job of producing optimal codegen to the compilers and not worrying about it anymore justifies leaving small gains on the table (provided they exist at all - I haven't really measured.)  
  
Some CE links:  
  
Clang `operator==`: https://godbolt.org/z/7cP98x53q (https://godbolt.org/z/dWMe4MWqh with `-march=native`)  
GCC `operator==`: https://godbolt.org/z/rY5GvhvcK (https://godbolt.org/z/r33PEd5ja with `-march=native`)  
  
Clang `operator<`: https://godbolt.org/z/435c6dzvb (https://godbolt.org/z/Pf455dxKq with `-march=native`)  
GCC `operator<`: https://godbolt.org/z/ncrcbYshT (https://godbolt.org/z/3P1n4hcWr with `-march=native`)  
  
We still use the hand optimized SSE2 on MSVC and under 32 bit, which is as it should be.

---

## Comment 4

> Username: Lastique  
> Created at: 2024-04-23 15:57:55 UTC  
> Updated at: 2024-04-23 15:59:12 UTC  
> Url: https://github.com/boostorg/uuid/issues/143#issuecomment-2072810785  

Here are links for gcc 10.2 (the version used in Debian 11, which is one of the target platforms I'm interested in):  
  
`operator==`: https://godbolt.org/z/bfTvncdo9  
`operator<`: https://godbolt.org/z/ojqGTvx3e  
  
And here is gcc 12.2 (Debian 12, another target system of my interest):  
  
`operator==`: https://godbolt.org/z/PMzPr14nr  
`operator<`: https://godbolt.org/z/rMK1h57oa  
  
(Spoiler: 10.2 and 12.2 are pretty much the same.)  
  
I think, I would still prefer SSE2 in these cases, although I haven't benchmarked yet. I definitely don't like the doubled number of loads.
