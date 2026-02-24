# #31 Improvement/test parse failure [Merged]

> Username: vtnerd  
> Created at: 2014-05-11 17:12:13 UTC  
> Updated at: 2014-07-09 19:02:39 UTC  
> Merged at: 2014-05-11 23:29:05 UTC  
> Closed at: 2014-05-11 23:29:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/31  

After seeing the mistake Thomas made in repeat parser, I realized seek and other parsers didn't have a check that verified the iterator position on failure. I have added such a function, and a test to seek. If this seems useful, I will start using this function in tests for other parsers. Or perhaps I missed this sort of utility somewhere else?

---

## Comment 1

> Username: K-ballo  
> Created_at: 2014-05-11 18:08:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/31#issuecomment-42778154  

This would be great to have. I think this PR should include adding test cases to every other component as well. Let's see what @djowel says.  
  
As far as I know, there is no existing test functionality checking that iterators are rolled back on failure.

---

## Comment 2

> Username: djowel  
> Created_at: 2014-05-11 23:28:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/31#issuecomment-42786893  

Looks good! I think it is a splendid idea.

---
