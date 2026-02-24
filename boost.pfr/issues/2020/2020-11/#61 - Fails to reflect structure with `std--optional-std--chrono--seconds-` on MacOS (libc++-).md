# #61 - Fails to reflect structure with `std::optional<std::chrono::seconds>` on MacOS (libc++?) [Closed]

> Username: apolukhin  
> Created at: 2020-11-28 17:29:31 UTC  
> Updated at: 2020-12-19 10:48:07 UTC  
> Closed at: 2020-12-19 10:48:06 UTC  
> Url: https://github.com/boostorg/pfr/issues/61  



---

## Comment 1

> Username: apolukhin  
> Created at: 2020-12-16 11:07:48 UTC  
> Url: https://github.com/boostorg/pfr/issues/61#issuecomment-746115458  

The failure is due to the incorrect constraint on a `std::chrono::duration` constructor, see [LWG 3050](https://wg21.link/lwg3050).  
  
libstdc++ applied [a workaround](https://github.com/gcc-mirror/gcc/blob/8833eab4461b4b7050f06a231c3311cc1fa87523/libstdc%2B%2B-v3/include/std/chrono#L507) , libc++ [does not](https://github.com/llvm/llvm-project/blob/407d42002904ce541f732ce4300913ef57cff232/libcxx/include/chrono#L1077)

---

## Comment 2

> Username: apolukhin  
> Created at: 2020-12-19 10:48:06 UTC  
> Url: https://github.com/boostorg/pfr/issues/61#issuecomment-748456686  

Fixed in f28952c
