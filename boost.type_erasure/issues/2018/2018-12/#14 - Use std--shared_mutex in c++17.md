# #14 - Use std::shared_mutex in c++17 [Closed]

> Username: MikeGitb  
> Created at: 2018-12-21 15:12:10 UTC  
> Updated at: 2019-06-06 18:44:28 UTC  
> Closed at: 2019-03-09 22:47:07 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/14  

Currently, type_erasure has a private dependency on boost thread because it needs a shared_mutex in dynamic_binding.cpp. Would it be feasible to use std::shared_mutex when available to break that dependency?   
  
I generally dislike conditional compilation, but in this case it would have a pretty strong impact on the number of dependencies (Downto 25 from 66 according to boostdep) and a very limited scope (no transitive propagation).

---

## Comment 1

> Username: MikeGitb  
> Created at: 2019-03-09 22:47:07 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/14#issuecomment-471229201  

I guess that is a no.

---

## Comment 2

> Username: sdebionne  
> Created at: 2019-06-06 10:01:00 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/14#issuecomment-499431653  

@MikeGitb I am not a maintainer of Boost.TypeErasure but this looks like a valid request to me. Could you reopen it? Maybe a PR would have more feedback?  
  
This actually raises the broader question of how should Boost libraries be modernized when new c++ standard are available. For example, in Boost.TypeErasure, an MPL Sequence is used in the `any` interface, while we have variadic templates in C++11.

---

## Comment 3

> Username: MikeGitb  
> Created at: 2019-06-06 18:44:28 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/14#issuecomment-499618727  

Well, as there was no reaction after 3 months, I'm sceptical if reopening the issue alone will help.  
Feel free to open a PR or an issue of your own but I'm no longer using Boost.TypeErasure and hence didn't want to track this further from my side.  
  
Some boost libraries do use c++11 or later features conditionally, if available.  That may be a valid approach here, but personally I'm not a fan of changing the API depending on the standard.  
  
I think it would be better if  boost would stop maintaining backwards compatibility with c++03 completely and would fully embrace c++11 (or even better 14) , but apparently there isn't enough consensus in the community.
