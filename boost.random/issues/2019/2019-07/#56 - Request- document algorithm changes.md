# #56 - Request: document algorithm changes [Closed]

> Username: dhardy  
> Created at: 2019-07-03 09:48:03 UTC  
> Updated at: 2019-07-03 10:16:10 UTC  
> Closed at: 2019-07-03 10:16:09 UTC  
> Url: https://github.com/boostorg/random/issues/56  

Could you please ensure any changes affecting reproducibility of results of generator and distribution algorithms are included in the release notes?  
  
See [this question](https://stackoverflow.com/questions/56384275/known-differences-in-boost-random-output-from-1-58-to-1-67) which notes possible changes in 1.62 and 1.64, yet I find no mentions of this in the [release notes](https://www.boost.org/users/history/).  
  
Also, may I assume that all (deterministic) algorithms are designed to be portable (no machine-dependent results)?

---

## Comment 1

> Username: swatanabe  
> Created at: 2019-07-03 10:06:14 UTC  
> Url: https://github.com/boostorg/random/issues/56#issuecomment-508030239  

AMDG  
  
On 7/3/19 3:48 AM, Diggory Hardy wrote:  
> Could you please ensure any changes affecting reproducibility of results of generator and distribution algorithms are included in the release notes?  
>  
  
Generators do not change, as the exact algorithm is  
part of the public API (okay, some of them changed once,  
when I normalized them for C++11).  Distributions, on  
the other hand, may vary between machines, releases, and  
implementations (of <random>), and you should never rely  
on the exact sequence.  
  
> See [this question](https://stackoverflow.com/questions/56384275/known-differences-in-boost-random-output-from-1-58-to-1-67) which notes possible changes in 1.62 and 1.64, yet I find no mentions of this in the [release notes](https://www.boost.org/users/history/).  
>   
> Also, may I assume that all (deterministic) algorithms are designed to be portable (no machine-dependent results)?  
>   
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: dhardy  
> Created at: 2019-07-03 10:16:09 UTC  
> Url: https://github.com/boostorg/random/issues/56#issuecomment-508033451  

Thanks for the clarification. In that case I think I'll have to use GSL for my distribution implementations.  
  
By the way, we have a [different policy](https://rust-random.github.io/book/portability.html) for the Rust Rand lib which may be of interest.  
  
(The irony: I maintain a random number lib for Rust, but have difficulty finding what I need for a C++ project.)
