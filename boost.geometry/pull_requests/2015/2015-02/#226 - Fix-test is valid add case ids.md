# #226 Fix/test is valid add case ids [Merged]

> Username: mkaravel  
> Created at: 2015-02-17 06:33:42 UTC  
> Updated at: 2015-02-19 08:46:46 UTC  
> Merged at: 2015-02-18 13:37:09 UTC  
> Closed at: 2015-02-18 13:37:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/226  

In this PR:  
- String-based IDs are added to the test cases (for easier identification).  
- Additional test cases (coming from buffer computations) have been added.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-02-17 13:38:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/226#issuecomment-74668429  

I'm of course ok with the change. However AFAIK in most cases the ID is the first parameter of test functions in Geometry. Then the geometries, then expected results, and additional parameters. Though I don't know if this order is required or if there is some guideline. Therefore I'll wait for Barend's comments before merging.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-02-17 14:06:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/226#issuecomment-74672043  

I have not checked this, but I take your word for it. I usually do it the other way around in my unit tests: I indicate the case id as the last parameter passed to the tester (see for example set operations).  
  
I had not realized we have a rule about that. Let's indeed wait for Barend's feedback on this.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2015-02-17 20:48:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/226#issuecomment-74750013  

Yes, I normally pass the case id first. It's not in the guidelines, it's more a habit. The order is as Adam describes.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2015-02-17 20:50:24 UTC  
> Updated_at: 2015-02-17 20:50:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/226#issuecomment-74750416  

But there are more differences: the statements  
  
```  
 test::apply(from_wkt<G>("MULTILINESTRING()"), true);  
```  
  
Look highly repetitive.  
I usually call  
  
```  
 test<G>("mycase", "MULTILINESTRING()", true);  
```  
  
instead

---

## Comment 5

> Username: mkaravel  
> Created_at: 2015-02-18 09:20:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/226#issuecomment-74834454  

Adopted both suggestions (see commit 4ab7a4f).  
The tester now takes plain WKTs as arguments, and the case ID is the first argument.  
  
I have kept the `apply()` static method, as my tester is a class rather than a function. I could fake a function like syntax by overloading the constructor, but I do not think this is necessary or that important.

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2015-02-18 10:58:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/226#issuecomment-74846410  

Thanks for adapting. It looks good.  
No, please don't create a constructor, that is indeed not the intention.  
  
I have functions, not classes. But no need to change this - if you need a class, fine

---

## Comment 7

> Username: mkaravel  
> Created_at: 2015-02-18 11:53:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/226#issuecomment-74852518  

Okay, let's merge this PR then.

---
