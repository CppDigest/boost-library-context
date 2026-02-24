# #37 - Provide a way to extract an arbitrary subsequence of a sequence [Closed]

> Username: ldionne  
> Created at: 2015-04-08 21:52:26 UTC  
> Updated at: 2015-04-12 17:27:52 UTC  
> Closed at: 2015-04-12 16:56:29 UTC  
> Url: https://github.com/boostorg/hana/issues/37  

Given a sequence `[x1, ..., xn]`, we need a way to extract a subsequence of the elements at indices `[i1, ..., ik]`.

---

## Comment 1

> Username: MarisaKirisame  
> Created at: 2015-04-12 17:25:06 UTC  
> Url: https://github.com/boostorg/hana/issues/37#issuecomment-92091150  

Nice :+1:

---

## Comment 2

> Username: ldionne  
> Created at: 2015-04-12 17:27:52 UTC  
> Url: https://github.com/boostorg/hana/issues/37#issuecomment-92092187  

Actually, I'm thinking that this exact operation could be used as an efficient basis for many other operations like `drop`, `take` and so on. That would require the sequences to be finite and to know their length at compile-time, but this might be a reasonable assumption to make throughout the library anyway. I'll research this further when I have more time.
