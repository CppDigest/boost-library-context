# #320 ForwardIterator -> ReadableIteratorConcept & ForwardTraversalConcept [Merged]

> Username: wanghan02  
> Created at: 2017-12-07 15:23:10 UTC  
> Updated at: 2017-12-11 09:24:45 UTC  
> Merged at: 2017-12-11 00:02:10 UTC  
> Closed at: 2017-12-11 00:02:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/320  

The concept of `ForwardIterator` is flawed because it mixed 2 sets of concepts (value access and traversal) into 1 package.  
  
http://www.boost.org/doc/libs/1_65_1/libs/iterator/doc/new-iter-concepts.html  
  
It requires `value_type (const)&` as return type when dereference is applied, which is not mandatory in spirit parsing. A return type which is convertible to value_type is good enough. `ReadableIteratorConcept` and `ForwardTraversalConcept` should be what we need for the iterator check.  
  
For example, the iterator of the range returned by `boost::adaptors::transform(std::string, func)` is normally not a `ForwardIterator`. But it fulfills `ReadableIteratorConcept` and `ForwardTraversalConcept` and should be able to be parsed by spirit.  
  
Test cases are added for both qi and x3.   
  
  
There is also a ticket for this but the solution suggested is not correct.   
https://svn.boost.org/trac10/ticket/12817

---

## Comment 1

> Username: wanghan02  
> Created_at: 2017-12-07 15:37:03 UTC  
> Updated_at: 2017-12-07 15:38:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/320#issuecomment-350003665  

@Kojoley pull request #230 was not a correct solution (same as in the ticket). But I cannot close that pull request.

---

## Review 2 [Approved]

> Username: Kojoley  
> Created_at: 2017-12-10 16:50:24 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/spirit/pull/320#pullrequestreview-82355845  

Seems perfectly logical to me.  
@djowel I'm going to merge this in a day or two if you have nothing against.

---

## Comment 3

> Username: djowel  
> Created_at: 2017-12-10 23:28:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/320#issuecomment-350590747  

Hmmm, seems my previous comment did not get through? Or is this a different PR?

---

## Comment 4

> Username: Kojoley  
> Created_at: 2017-12-10 23:30:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/320#issuecomment-350590863  

Who knows.

---

## Comment 5

> Username: djowel  
> Created_at: 2017-12-10 23:33:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/320#issuecomment-350591074  

OK... anyway, what I wrote was: I like the new iterator traits, but it is not really "new". It was written in 2003 and so far, I don't see it assimilated by the standards (C++11 and onwards). Correct me if I am wrong. So, unless there's real activity towards that, this falls into the category of "don't fix it if it ain't broken".

---

## Comment 6

> Username: Kojoley  
> Created_at: 2017-12-10 23:45:11 UTC  
> Url: https://github.com/boostorg/spirit/pull/320#issuecomment-350591862  

The PR is not about simply switching to "new" iterator concepts, but to make spirit less paranoid and allow the things like `boost::transformed_range`. Currently it produces the compilation error because of asserts and will work fine if you remove them.

---

## Comment 7

> Username: djowel  
> Created_at: 2017-12-11 00:01:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/320#issuecomment-350592906  

I see that now. OK, we need this alright. It fixes something that's broken. I'll merge this one.

---

## Comment 8

> Username: djowel  
> Created_at: 2017-12-11 00:03:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/320#issuecomment-350593014  

And thanks @wanghan02 and @Kojoley

---
