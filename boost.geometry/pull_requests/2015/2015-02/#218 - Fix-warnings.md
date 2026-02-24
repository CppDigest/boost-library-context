# #218 Fix/warnings [Merged]

> Username: awulkiew  
> Created at: 2015-02-11 15:08:57 UTC  
> Updated at: 2015-02-13 19:51:46 UTC  
> Merged at: 2015-02-11 19:41:58 UTC  
> Closed at: 2015-02-11 19:41:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/218  

This PR fixes the MSVC compiler warning C4127: conditional expression is constant in all parts of the library.  
  
For this purpose a macro BOOST_GEOMETRY_CONDITION was added. It should be used in cases when a conditional expression may be constant.  
Note that the same expression may or may not be constant in various cases when template specialization is involved.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-02-11 15:13:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/218#issuecomment-73897010  

This fixes the ticket: https://svn.boost.org/trac/boost/ticket/10666

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-02-11 18:09:25 UTC  
> Updated_at: 2015-02-11 19:29:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/218#discussion_r24517581  

Shouldn't these comments go away?

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-02-11 18:11:21 UTC  
> Updated_at: 2015-02-11 19:29:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/218#discussion_r24517759  

Macro name in comment does not match macro in `#ifndef` and `#define` directives at beginning of file.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2015-02-11 18:23:27 UTC  
> Updated_at: 2015-02-11 19:29:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/218#discussion_r24518835  

I'd prefer to keep it in case if some other or future version of MSVC didn't work as expected or started to generate a warning in additional cases, e.g. for the workaround below.

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2015-02-11 18:38:34 UTC  
> Updated_at: 2015-02-11 19:29:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/218#discussion_r24520272  

Please remove the extra spacing you added - it is not according to our guidelines, and never used in this sourcefile (at least not in the original version).

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2015-02-11 18:38:49 UTC  
> Updated_at: 2015-02-11 19:29:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/218#discussion_r24520296  

Idem, and everywhere

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2015-02-11 18:39:57 UTC  
> Updated_at: 2015-02-11 19:29:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/218#discussion_r24520410  

I don't object to extra spaces in multi line expressions as this makes the layout quite clear here

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2015-02-11 18:41:49 UTC  
> Updated_at: 2015-02-11 19:29:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/218#discussion_r24520563  

Hmm, I see why this is done, but this makes the review much more work.  
I would have preferred if these had been a separate commit  
Anyway, no need to change now

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2015-02-11 18:46:13 UTC  
> Updated_at: 2015-02-11 19:29:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/218#discussion_r24520964  

OK - please add a comment then, in case anyone later thinks to remove old code

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2015-02-11 18:49:33 UTC  
> Updated_at: 2015-02-11 19:29:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/218#discussion_r24521314  

Never seen this before. Does this work everywhere?  
I prefer the old version, but than with BOOST_GEOMETRY_CONDITION as done everywhere else

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2015-02-11 18:50:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/218#issuecomment-73939614  

Thanks for the PR. See my comments but basically I'm OK having this in our library

---

## Comment 12

> Username: awulkiew  
> Created_at: 2015-02-11 19:10:02 UTC  
> Updated_at: 2015-02-11 19:29:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/218#discussion_r24523177  

If by "everywhere" you mean "C++ standard-conformant compiler" then yes :)  
The result of conditional operator is an l-value if both the second and third operands are of the same type and both are l-values. In C this operator always returns r-value.  
  
In C++ this operator allows to do many more interesting things. E.g. see the example here: http://en.cppreference.com/w/cpp/language/operator_other#Conditional_operator  
  
I've implemented it this way because it's "compatible" with the implementation of `get()` method, the conditional operator is also used there. It doesn't require any additional utils/macros, the language allows us to do it. And it's only a test.  
  
Though I agree that this is not a well-known feature and a reader may be surprised seing this. So if you strongly prefer the "longer" version with macro I'll change it.

---

## Comment 13

> Username: barendgehrels  
> Created_at: 2015-02-11 19:20:58 UTC  
> Updated_at: 2015-02-11 19:29:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/218#discussion_r24524255  

Thanks for the link.  
OK, if this is standard, and supported by all compilers we support, we are allowed to use it. It is indeed much shorter.  
So I changed opinion, no need to change, unless we later see in the matrix that this breaks some compilers which are for the rest working for us.

---

## Comment 14

> Username: awulkiew  
> Created_at: 2015-02-11 19:31:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/218#discussion_r24525334  

Yes, this is why I started to use this convention in all cases, to be consistent.

---

## Comment 15

> Username: awulkiew  
> Created_at: 2015-02-11 19:41:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/218#issuecomment-73949557  

Ok, thanks for the review. I addressed all of the issues. I'm merging. In case of problems I'll act accordingly.

---
