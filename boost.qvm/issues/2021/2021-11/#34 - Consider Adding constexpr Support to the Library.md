# #34 - Consider Adding constexpr Support to the Library? [Closed]

> Username: stellarpower  
> Created at: 2021-11-22 18:49:11 UTC  
> Updated at: 2021-12-14 23:19:51 UTC  
> Closed at: 2021-12-13 23:15:15 UTC  
> Url: https://github.com/boostorg/qvm/issues/34  

Hi,  
  
Looks to me like a good amount of QVM would have support for evaluating as constant expressions, and indeed operations like element access require template parameters and so far are all working for me in constexpr functions. I may of course be overlooking a massive implementation detail, but as QVM targets C++03, there would be no impetus to make the library constexpr in general, so I think that it would be possible to provide limited support for a subset of operations to be constexpr where this is mathematically possible, and this would be a nice feature to have for those of us compiling against a more modern standard. I am using QVM combined with strong types and unit wrapping, converting between different co-ordinate systems. Certainly would be nice to show how everything just evaporates away once it's compiled and is no slower than using floats!  
  
### Proposal:  
- Raise the question whether it would be suitable for some operations to be declared as constexpr  
- Raise the question whether it would actually be possible for some operations to be declared constexpr   
  * And assess rough library coverage of the operations that can be reduced to compile-time expressions  
- Look into implications of having constexpr support for some operations but not others, when combining transformations in typical sample usage of the library.  
  * Would it be beneficial to have constexpr support for even just basic operations, so that this option is available for user programmers?  
  * Or would it be unhelpful if provided whilst only a limited range of typical scenarios would benefit?  
- Detect standard version using a macro, naturally reverting to normal/inline function templates when constexpr support is missing.  
  
Thanks.

---

## Comment 1

> Username: zajo  
> Created at: 2021-11-22 19:14:29 UTC  
> Url: https://github.com/boostorg/qvm/issues/34#issuecomment-975839305  

Good points. Basically everything that can be constexpr should be constexpr, so it's just a matter of using the correct Boost macros to avoid breaking old compilers.

---

## Comment 2

> Username: stellarpower  
> Created at: 2021-11-22 20:46:31 UTC  
> Updated at: 2021-11-22 20:48:09 UTC  
> Url: https://github.com/boostorg/qvm/issues/34#issuecomment-975904540  

Zero-overhead, I think that was my thinking, hopefully it shouldn't require changing anything except enabling via a macro where possible.  As you say, the standard library has taken the same principle - if it can reduce to a constant expression then it ought to. Makes basic getters and setters cheap in the worst case and free in the best. If QVM can support this for some operations then it's a nice bonus if targetting a recent standard.  
  
Separate thread, but I wonder if there would be a way to do the same for concepts and constraints, falling back to enable_if if not possible. For more readable errors. That's a much larger modification however.

---

## Comment 3

> Username: zajo  
> Created at: 2021-11-22 22:17:58 UTC  
> Url: https://github.com/boostorg/qvm/issues/34#issuecomment-975966887  

Yeah maybe it's possible to come up with some macro trickery to support both. Probably not very many people would care for old compilers, but it can be really painful so I don't want to break it.

---

## Comment 4

> Username: zajo  
> Created at: 2021-11-26 00:05:58 UTC  
> Url: https://github.com/boostorg/qvm/issues/34#issuecomment-979524655  

See https://github.com/boostorg/qvm/commit/54aa1f8d93a11ac2210d23a59c4b862ef30efd98.

---

## Comment 5

> Username: stellarpower  
> Created at: 2021-12-14 16:47:19 UTC  
> Url: https://github.com/boostorg/qvm/issues/34#issuecomment-993752891  

I'll try to pull this and give a test, when I can find the time. Thakns!

---

## Comment 6

> Username: zajo  
> Created at: 2021-12-14 18:23:52 UTC  
> Url: https://github.com/boostorg/qvm/issues/34#issuecomment-993858010  

It's now in the `master` branch, though it didn't make it in the current Boost release.

---

## Comment 7

> Username: stellarpower  
> Created at: 2021-12-14 20:42:43 UTC  
> Url: https://github.com/boostorg/qvm/issues/34#issuecomment-993971112  

I'm in mamba actually, so it may be easy enough to clone the env and pull the new headers. I've commented out all my previous constexpr specifiers, so it should be easy enough to grep through with those, rebuild, and see if it all works as expected. Just really busy right now. Thanks for the work!   
  
14 Dec 2021 18:24:03 Emil Dotchevski ***@***.***>:  
  
> It's now in the *master* branch, though it didn't make it in the current Boost release.  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub[https://github.com/boostorg/qvm/issues/34#issuecomment-993858010], or unsubscribe[https://github.com/notifications/unsubscribe-auth/ABGF2ANMPJXEAXBPNGOEGH3UQ6DUFANCNFSM5IRWXN2Q].  
>  [data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAAAAXNSR0IArs4c6QAAAARzQklUCAgICHwIZIgAAAArSURBVHic7cEBDQAAAMKg909tDjegAAAAAAAAAAAAAAAAAAAAAAAAAAA+DFFIAAEctgHwAAAAAElFTkSuQmCC###24x24:true###][Tracking image][https://github.com/notifications/beacon/ABGF2APBL3Q5JPCLFAHIIALUQ6DUFA5CNFSM5IRWXN22YY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGOHM6RDWQ.gif]  
>

---

## Comment 8

> Username: zajo  
> Created at: 2021-12-14 23:19:50 UTC  
> Url: https://github.com/boostorg/qvm/issues/34#issuecomment-994131136  

The work is not complete, I've basically added `constexpr` where it's easy to add.
