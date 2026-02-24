# #38 Avoid -Wmaybe-uninitialized warnings in gcc (issue #27). [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2020-03-27 19:03:32 UTC  
> Updated at: 2020-03-28 02:10:51 UTC  
> Merged at: 2020-03-28 01:45:44 UTC  
> Closed at: 2020-03-28 01:45:44 UTC  
> Url: https://github.com/boostorg/function/pull/38  

Hi,  
  
@pdimov I am assuming based on what you wrote in issue #27 that the actual code of Boost function is fine and this is a gcc false positive. So I simply add some pragmas to silence it.  
  
Cheers,  
Romain

---

## Comment 1

> Username: mclow  
> Created_at: 2020-03-27 19:24:46 UTC  
> Url: https://github.com/boostorg/function/pull/38#issuecomment-605254601  

I would appreciate it if you would file a bug against gcc for this bogus warning.

---

## Comment 2

> Username: Romain-Geissler-1A  
> Created_at: 2020-03-27 19:30:20 UTC  
> Updated_at: 2020-03-27 19:30:54 UTC  
> Url: https://github.com/boostorg/function/pull/38#issuecomment-605269471  

Well I can, but please be aware that there are already of lot of gcc bug reports about false positive in -Wmaybe-uninitialized. They usually either get closed as duplicate of existing ones, or left open and never really investigated/fixed. They have so many false positive that they had to introduce "-Wmaybe-uninitialized" out of the existing "-Wuninitialized" because people complained too much about them.  
  
So I can try to submit reduce to a smaller test case and submit a gcc bug, but don't actually expect that it will be fixed any time soon ;)

---

## Comment 3

> Username: mclow  
> Created_at: 2020-03-27 19:44:07 UTC  
> Url: https://github.com/boostorg/function/pull/38#issuecomment-605284355  

If people don't push back on the gcc developers, then it will never get fixed.

---

## Comment 4

> Username: mclow  
> Created_at: 2020-03-27 19:46:29 UTC  
> Url: https://github.com/boostorg/function/pull/38#issuecomment-605285226  

I'm not going to say "don't make this change", but you're replacing 5 lines of code with 15 to silence a (bogus) compiler warning.    
  
A different approach would be to say "`-Wmaybe-uninitialized` gives bogus warnings, please disable it"

---

## Comment 5

> Username: mclow  
> Created_at: 2020-03-27 19:48:09 UTC  
> Url: https://github.com/boostorg/function/pull/38#issuecomment-605285817  

Just to be perfectly clear - I am not advocating the second approach, just pointing out that it is an alternate way of handling this.

---

## Comment 6

> Username: pdimov  
> Created_at: 2020-03-27 20:09:53 UTC  
> Url: https://github.com/boostorg/function/pull/38#issuecomment-605293815  

The warning is correct; we are copying uninitialized data. But it's not an error, and we don't want to pay the price for initializing it just because of the warning.

---

## Comment 7

> Username: pdimov  
> Created_at: 2020-03-27 20:50:58 UTC  
> Url: https://github.com/boostorg/function/pull/38#issuecomment-605309422  

Maybe change the comment from "false positive" to "technically correct, but we don't want to pay the price for initializing just to silence a warning". :-)

---

## Comment 8

> Username: Romain-Geissler-1A  
> Created_at: 2020-03-27 20:51:27 UTC  
> Url: https://github.com/boostorg/function/pull/38#issuecomment-605309597  

@mclow It is definitely not the first time I hear that. To be honest, for some warnings like this one, I even considered it a few times for my own projects. But I never did so far, because while it has a number of false positives, which is definitely very annoying, it also has uncovered a number of bugs in our own code. And some people (among them myself), think that you should be warning free and build with -Werror/-Wall/-Wextra, because more often than not warnings do uncover real problems. Now, I understand not everyone agrees with that, this perfectly fine. Yet, I consider that as much as we can we should try to silence warnings in headers of libraries meant to be used by others, so typically the Boost case, as owner of lib X should refrain from deciding for its users which warning is valid and which is not. I know the question of warnings is a strong debate I have already seen many times on Boost libraries, some consider we should fix/silence them all in Boost code, some consider we should fix compilers instead. This is like the vim vs emacs debate, we will never reach an unanimous consensus.  
  
So here, apparently I misread what @pdimov wrote and this an actual positive that is fine to ignore. In that case fixing the compiler is not an option. So is it ok to silence explicitly the warning via a pragma ?  
  
I am open to suggestions if you want to reduce the amount of boiler plate code. We might consider adding a single "push" at the beginning of the file, and a single "pop" at the end, yet this might hide some other real bugs in the future.

---

## Comment 9

> Username: pdimov  
> Created_at: 2020-03-27 20:53:00 UTC  
> Url: https://github.com/boostorg/function/pull/38#issuecomment-605310162  

> We might consider adding a single "push" at the beginning of the file, and a single "pop" at the end  
  
Absolutely not. -Wmaybe-uninitialized is more often correct than not, and catches real bugs.

---

## Comment 10

> Username: Romain-Geissler-1A  
> Created_at: 2020-03-27 20:59:10 UTC  
> Url: https://github.com/boostorg/function/pull/38#issuecomment-605312418  

I have updated the wording of the comment.

---

## Comment 11

> Username: Kojoley  
> Created_at: 2020-03-28 01:53:37 UTC  
> Url: https://github.com/boostorg/function/pull/38#issuecomment-605377788  

I had reported a few `-Wmaybe-uninitialized` false positives and they are still not fixed. I also learned that copying uninitialized data is UB by the standard, even by implicit copy constructor, and I just do not get it.

---

## Comment 12

> Username: pdimov  
> Created_at: 2020-03-28 02:10:51 UTC  
> Url: https://github.com/boostorg/function/pull/38#issuecomment-605380049  

Not by memcpy though.

---
