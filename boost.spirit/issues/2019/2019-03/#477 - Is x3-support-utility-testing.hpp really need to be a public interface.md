# #477 - Is x3/support/utility/testing.hpp really need to be a public interface? [Closed]

> Username: Kojoley  
> Created at: 2019-03-01 20:01:20 UTC  
> Updated at: 2019-04-20 12:51:29 UTC  
> Closed at: 2019-04-20 12:51:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/477  

It was added in a3d667002f125aae1482bb2a631475f716d723cb and still only used by a single example. It also includes Boost.Filesystem and Boost.Regex libraries what is not fine as any inclusion makes BCP and Boostdep think the whole library depends on them (there are few other questionable places where this happens).  
  
A bit off-topic, but it also seems to have bugs (see #476) and I do not want to cover it with tests.

---

## Comment 1

> Username: djowel  
> Created at: 2019-03-01 22:55:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/477#issuecomment-468839463  

It does not need to be public.

---

## Comment 2

> Username: Kojoley  
> Created at: 2019-03-01 22:56:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/477#issuecomment-468839730  

Then what was the purpose of a3d667002f125aae1482bb2a631475f716d723cb commit?

---

## Comment 3

> Username: djowel  
> Created at: 2019-03-01 22:58:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/477#issuecomment-468840250  

I think a3d6670 commit is wrong, in retrospect. You are correct that it unnecessarily includes dependencies that we do not want.

---

## Comment 4

> Username: Kojoley  
> Created at: 2019-03-01 23:06:11 UTC  
> Url: https://github.com/boostorg/spirit/issues/477#issuecomment-468841925  

Ok. I will move it back after 1.70 released as technically it is a breaking change and Boost is currently closed for breaking changes.  
  
Please, look at #476, the rexpr example runs fine with it but I may miss something.
