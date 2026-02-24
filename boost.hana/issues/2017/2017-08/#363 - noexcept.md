# #363 - noexcept? [Closed]

> Username: bebuch  
> Created at: 2017-08-15 08:48:28 UTC  
> Updated at: 2017-08-22 01:34:02 UTC  
> Closed at: 2017-08-22 01:33:56 UTC  
> Url: https://github.com/boostorg/hana/issues/363  

Hi Louis, why don't you use `noexcept` in most of your code? It's not important, I'm just interested.

---

## Comment 1

> Username: ldionne  
> Created at: 2017-08-22 01:33:56 UTC  
> Url: https://github.com/boostorg/hana/issues/363#issuecomment-323894655  

Sorry for the slow reply, I was on vacation. The reason for not using `noexcept` is just simplicity, a bit like the standard library, which only talks about `noexcept` when it makes an obvious difference. For Hana I simply did not do it. Also, given the metaprogramming nature of Hana, I think there are _fewer_ places where it would matter, but there are certainly places where it would. It's just a lot of work and seemed like the return on investment would be minimal. If there are places where you think `noexcept` would be a worthwhile addition, feel free to open a PR.
