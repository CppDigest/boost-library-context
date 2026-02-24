# #204 - Unordered doesn't respect explicit converting Allocator constructors [Closed]

> Username: cmazakas  
> Created at: 2023-08-10 21:30:52 UTC  
> Updated at: 2024-02-18 10:58:34 UTC  
> Closed at: 2024-02-17 17:58:49 UTC  
> Url: https://github.com/boostorg/unordered/issues/204  

Relevant Compiler Explorer link: https://godbolt.org/z/s5PfM6x9h

---

## Comment 1

> Username: ro4i7  
> Created at: 2023-08-13 15:17:56 UTC  
> Updated at: 2023-08-13 15:18:18 UTC  
> Url: https://github.com/boostorg/unordered/issues/204#issuecomment-1676389945  

@cmazakas I think manually fixing the constructor call by explicitly creating the allocator and then passing it to the 'boost::unordered_map' constructor. I think it will work by this way, can I work on this?

---

## Comment 2

> Username: ephraimfeldblum  
> Created at: 2024-02-17 22:12:48 UTC  
> Url: https://github.com/boostorg/unordered/issues/204#issuecomment-1950430483  

Thanks, @joaquintides for the fix.  
  
The conversation we had on discord, and the godbolt link above refer specifically to `unordered_map`. However, there was also a regression introduced later in 1.84. In 1.83, `unordered_node_map` with explicit converting allocators compiled. In 1.84 they do not. I understand that that is simply a duplicate of this issue, and that #234 fixes it as well?

---

## Comment 3

> Username: joaquintides  
> Created at: 2024-02-18 10:58:34 UTC  
> Url: https://github.com/boostorg/unordered/issues/204#issuecomment-1951148552  

Hi @ephraimfeldblum, #234 fixed problems with all containers, including `unordered_node_map`. Maybe you could check out the `develop` branch of Boost.Unordered and verify that everything's solved on your side?  
  
Best,
