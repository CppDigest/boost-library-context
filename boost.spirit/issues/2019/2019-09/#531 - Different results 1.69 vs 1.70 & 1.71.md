# #531 - Different results 1.69 vs 1.70 & 1.71 [Closed]

> Username: OBorce  
> Created at: 2019-09-09 10:08:30 UTC  
> Updated at: 2019-09-12 14:40:33 UTC  
> Closed at: 2019-09-12 14:40:33 UTC  
> Url: https://github.com/boostorg/spirit/issues/531  

Just a question if the change of behavior is intended or not. Or maybe I am misusing the rules.  
  
https://wandbox.org/permlink/j9ZpuJIE25yqfq1q

---

## Comment 1

> Username: djowel  
> Created at: 2019-09-10 22:26:24 UTC  
> Url: https://github.com/boostorg/spirit/issues/531#issuecomment-530144407  

This looks like an issue related to https://github.com/boostorg/spirit/issues/527 "Alternative parser does not rollback on failure". This is the correct behavior and consistent with Qi. See https://www.boost.org/doc/libs/1_71_0/libs/spirit/doc/html/spirit/qi/reference/operator/alternative.html: "Alternative parsers do not roll back changes made to the outer attribute because of a failed alternative."

---

## Comment 2

> Username: OBorce  
> Created at: 2019-09-12 07:34:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/531#issuecomment-530703984  

Just to be sure, the rollback does not happen only for Alternative parser with same result e.g. (A | A) => A and if A is a sequence (e.g. string, vector), but it does rollback(replace the result) for other non sequence alternative parsers, (e.g. int, variant<string, int>)?

---

## Comment 3

> Username: djowel  
> Created at: 2019-09-12 08:49:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/531#issuecomment-530730077  

> Just to be sure, the rollback does not happen only for Alternative parser with same result e.g. (A | A) => A and if A is a sequence (e.g. string, vector), but it does rollback(replace the result) for other non sequence alternative parsers, (e.g. int, variant<string, int>)?  
  
Sorry, I'm not following. Do you have an example?

---

## Comment 4

> Username: OBorce  
> Created at: 2019-09-12 09:58:19 UTC  
> Updated at: 2019-09-12 09:59:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/531#issuecomment-530755120  

I modified the above example to return variant<string, int> instead of the std::string  
https://wandbox.org/permlink/WfByQ7XWn389Ct6u  
and now v is replaced by the empty string of the second alternative parser.

---

## Comment 5

> Username: djowel  
> Created at: 2019-09-12 13:16:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/531#issuecomment-530820806  

Variant attributes do not really "roll back", there are different target attribute types for each alternative and the one that matches will be used. Since variants do not hold each type simultaneously, it will look as-if there's a rollback, but the reality is that the data is just overwritten by a new value when the previous attempt failed.

---

## Comment 6

> Username: OBorce  
> Created at: 2019-09-12 13:52:14 UTC  
> Url: https://github.com/boostorg/spirit/issues/531#issuecomment-530836334  

yes, but it is not unique to variant, it will be true for any other non sequence type.  
My question is, can I relay on this behavior to stay the same in future versions?
