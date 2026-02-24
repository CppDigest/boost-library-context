# #85 - is there a list of projects using Boost.Multiprecision? [Closed]

> Username: ofloveandhate  
> Created at: 2018-09-18 00:21:00 UTC  
> Updated at: 2018-09-22 10:45:50 UTC  
> Closed at: 2018-09-19 14:14:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/85  

I have found it nice with other libraries to have some idea of what projects use them.  Is there a list for Boost.Multiprecision?  I looked and failed to find.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-09-19 14:14:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/85#issuecomment-422820338  

No.  
  
I do know that there are plenty of bug-reports quickly arriving if we make a mistake so someone other than yourself must be using it ;)

---

## Comment 2

> Username: NAThompson  
> Created at: 2018-09-19 17:01:57 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/85#issuecomment-422880474  

There is a list of public clones under the insights tab. This shows how many people are doing clones of the head and the referrer header, which gives a partial answer to your question.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2018-09-19 17:37:33 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/85#issuecomment-422893066  

I suspect most of those clones are folks working on the whole boost tree?  Most users probably wouldn't get the library that way.

---

## Comment 4

> Username: NAThompson  
> Created at: 2018-09-22 00:41:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/85#issuecomment-423704000  

I think you are right; I compared the clones for boost.math with boost.multiprecision, and they are almost perfectly correlated.  
  
The referrer header might be more interesting? I note that https://advanpix.com/ is showing up a lot; maybe that is the one project using boost.math and boost.multiprecision?

---

## Comment 5

> Username: jzmaddock  
> Created at: 2018-09-22 08:13:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/85#issuecomment-423726396  

>I note that https://advanpix.com/ is showing up a lot; maybe that is the one project using boost.math and boost.multiprecision?  
  
Not sure about those guys - they have contributed patches to Math, but I think they use us as a benchmark to compare their proprietary code against.

---

## Comment 6

> Username: johnmcfarlane  
> Created at: 2018-09-22 10:45:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/85#issuecomment-423735126  

I do value knowing what else uses a library. It can provide good usage  
examples.  
  
CNL uses Multiprecision for testing interop.  
  
On Sat, Sep 22, 2018, 09:13 jzmaddock <notifications@github.com> wrote:  
  
> I note that https://advanpix.com/ is showing up a lot; maybe that is the  
> one project using boost.math and boost.multiprecision?  
>  
> Not sure about those guys - they have contributed patches to Math, but I  
> think they use us as a benchmark to compare their proprietary code against.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/multiprecision/issues/85#issuecomment-423726396>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AAszN8T8S3aAgaQwH58v2FZhiu1lsmUGks5udfEvgaJpZM4Ws88k>  
> .  
>
