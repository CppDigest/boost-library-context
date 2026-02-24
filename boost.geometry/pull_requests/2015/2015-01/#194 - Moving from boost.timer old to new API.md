# #194 Moving from boost.timer old to new API [Closed]

> Username: raffienficiaud  
> Created at: 2015-01-14 17:14:50 UTC  
> Updated at: 2015-01-15 22:37:50 UTC  
> Closed at: 2015-01-14 21:09:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/194  

Please find here the PR for fixing the issues you have in geometry, due to the changes in boost.test.  
http://article.gmane.org/gmane.comp.lib.boost.devel/256800

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-01-14 17:32:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/194#issuecomment-69955575  

Thanks for the PR.  
  
My (our) main objection is that Boost.Test was usable as a header-only library. I assume, and really hope, that it will stay to be usable as a header-only library. I understand that it now depends on Boost.Timer v2 and Boost.Timer v2 is not header-only. But that should be solved somehow.  
  
This PR advances Boost.Timer v1 to Boost.Timer v2 (with the side-effect that it fixes Boost.Test issues) but that is not enough. If we apply this it would require linking with Boost.Timer, which is unwished (and not part of this PR).  
  
Besides that, the robustness tests are NOT unit-tests and (AFAIR) not dependant on Boost.Test. They can for now stay as they are.  
  
I will send a message to the list about my opinion how to solve this.

---

## Comment 2

> Username: raffienficiaud  
> Created_at: 2015-01-14 18:21:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/194#issuecomment-69964198  

I agree with your remarks. I separated the commits on these parts, in case you want to do a selective merge/cherry pick.   
There are several options on boost.test side as you should know. The header only variant is definitely something we want to keep (although I do not know how it works with boost.system).

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2015-01-14 19:29:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/194#issuecomment-69975682  

Good to hear you want to keep it. Will react later.

---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2015-01-14 21:09:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/194#issuecomment-69992376  

I rolled back the changes in boost.test. Should work now. Let me know if you experience any issue with this.

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2015-01-15 22:37:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/194#issuecomment-70176072  

Thanks - I assume you noted on the list that Beman will create a header-only option for Boost.Timer v2. So that will make all your changes OK! One or two weeks.  
https://svn.boost.org/trac/boost/ticket/10935

---
