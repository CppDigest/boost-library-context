# #194 Spirit QI grammar doesn't work [Closed]

> Username: tiagokepe  
> Created at: 2016-06-09 16:08:58 UTC  
> Updated at: 2016-06-09 20:26:05 UTC  
> Closed at: 2016-06-09 20:26:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/194  

I've followed the Spirit QI tutorial based on the bellow grammar (http://www.boost.org/doc/libs/1_60_0/libs/spirit/doc/html/spirit/introduction.html):  
  
group       = '(' >> expression >> ')';  
factor      = integer | group;  
term        = factor >> _(('_' >> factor) | ('/' >> factor));  
expression  = term >> *(('+' >> term) | ('-' >> term));  
  
The parser returns true to the input: "1 blabla 2".

---

## Comment 1

> Username: vtnerd  
> Created_at: 2016-06-09 18:04:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/194#issuecomment-224978121  

There appears to be a lot more commits in this pull request than necessary, and more than likely Joel will want this pull request _into_ develop.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2016-06-09 18:15:26 UTC  
> Updated_at: 2016-06-09 18:21:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/194#issuecomment-224980999  

There are no commits at all. Just a question and I do not know why it is done as a pull request.  
  
> group = '(' >> expression >> ')';  
> factor = integer | group;  
> term = factor >> (('' >> factor) | ('/' >> factor));  
> expression = term >> *(('+' >> term) | ('-' >> term));  
  
This grammar is ok and it must succeed with the "1 blabla 2" input. It will just parse "1" (expression -> term -> factor -> integer), you can check it by comparing you first iterator to the last (iter != last)  
Also you can enable debugging (google for `BOOST_SPIRIT_DEBUG` and `BOOST_SPIRIT_DEBUG_NODES`)

---

## Comment 3

> Username: tiagokepe  
> Created_at: 2016-06-09 18:27:03 UTC  
> Url: https://github.com/boostorg/spirit/pull/194#issuecomment-224984244  

I disagree that "1 blabla 2" must succeed, because it is an expression that doesn't belong to the grammar. I thing that such expression must to be evaluated as a whole.

---

## Comment 4

> Username: djowel  
> Created_at: 2016-06-09 20:24:12 UTC  
> Updated_at: 2016-06-09 20:24:46 UTC  
> Url: https://github.com/boostorg/spirit/pull/194#issuecomment-225015161  

It is a partial match. If you do not agree that partial matches should be allowed, please ask your question to the spirit list for discussion instead: http://boost-spirit.com/home/feedback-and-support/. I'm closing this one. If there are patches, please be specific and file them separately.

---
