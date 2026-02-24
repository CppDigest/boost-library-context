# #20 Added BOOST_TEST_NOT to lightweight test. [Merged]

> Username: eldiener  
> Created at: 2016-05-19 14:07:07 UTC  
> Updated at: 2016-09-09 22:22:37 UTC  
> Merged at: 2016-09-09 22:19:10 UTC  
> Closed at: 2016-09-09 22:19:10 UTC  
> Url: https://github.com/boostorg/core/pull/20  



---

## Comment 1

> Username: pdimov  
> Created_at: 2016-05-19 14:18:23 UTC  
> Url: https://github.com/boostorg/core/pull/20#issuecomment-220338047  

What is the purpose of this change?

---

## Comment 2

> Username: eldiener  
> Created_at: 2016-05-19 14:37:03 UTC  
> Url: https://github.com/boostorg/core/pull/20#issuecomment-220343905  

Purely ease of use. Manually specifying BOOST_TEST(!(expr)) is a pain. It also parallels the BOOST_TEST_EQ/BOOST_TEST_NE pair in its own way.

---

## Comment 3

> Username: eldiener  
> Created_at: 2016-05-19 14:50:43 UTC  
> Url: https://github.com/boostorg/core/pull/20#issuecomment-220348139  

It is a convenient way for testing that some expression is not true.

---

## Comment 4

> Username: pdimov  
> Created_at: 2016-05-19 14:52:27 UTC  
> Url: https://github.com/boostorg/core/pull/20#issuecomment-220348699  

I'd like to see an example where it's an improvement over the equivalent BOOST_TEST, if you don't mind.  
  
And a doc patch. :-)

---

## Comment 5

> Username: eldiener  
> Created_at: 2016-05-19 15:07:20 UTC  
> Url: https://github.com/boostorg/core/pull/20#issuecomment-220353570  

Is there documentation for lightweight_test ? I have never found any.

---

## Comment 6

> Username: pdimov  
> Created_at: 2016-05-19 15:12:01 UTC  
> Url: https://github.com/boostorg/core/pull/20#issuecomment-220355168  

There wasn't, but there is now, in `doc/lightweight_test.qbk`.

---

## Comment 7

> Username: eldiener  
> Created_at: 2016-05-19 15:53:51 UTC  
> Updated_at: 2016-05-19 16:01:52 UTC  
> Url: https://github.com/boostorg/core/pull/20#issuecomment-220368752  

A simple example might be:  
  
`#include <boost/type_traits.hpp>`  
`#include <boost/detail/lightweight_test.hpp>`  
  
`int main()`  
`{`  
`BOOST_TEST_NOT(boost::is_compound<int>::value);`  
`return boost::report_errors();`  
`}`  
  
I will update the doc and tests accordingly as part of the pull request.

---

## Comment 8

> Username: pdimov  
> Created_at: 2016-05-19 16:03:09 UTC  
> Url: https://github.com/boostorg/core/pull/20#issuecomment-220371626  

That's `BOOST_TEST_TRAIT_FALSE((boost::is_compound<int>))`.

---

## Comment 9

> Username: eldiener  
> Created_at: 2016-05-19 16:58:51 UTC  
> Url: https://github.com/boostorg/core/pull/20#issuecomment-220387296  

That's nice to know. thanks ! But my point is still that BOOST_TEST_NOT is still a syntactical convenience in any situation whenever you want to test that some expression should not be true.

---

## Comment 10

> Username: Lastique  
> Created_at: 2016-05-19 17:04:19 UTC  
> Url: https://github.com/boostorg/core/pull/20#issuecomment-220388792  

Frankly, I don't see much advantage compared to prepending '!' to the expression (or changing comparison operator to the opposite). Just my 2 cents.

---

## Comment 11

> Username: eldiener  
> Created_at: 2016-09-05 04:38:24 UTC  
> Url: https://github.com/boostorg/core/pull/20#issuecomment-244657485  

Do you want me to close this PR. It seems you have no intention of accepting it.

---

## Comment 12

> Username: Lastique  
> Created_at: 2016-09-05 11:20:53 UTC  
> Url: https://github.com/boostorg/core/pull/20#issuecomment-244723581  

I'm leaving that to Peter to decide as he's the main maintainer of that header.

---

## Comment 13

> Username: pdimov  
> Created_at: 2016-09-09 22:22:37 UTC  
> Url: https://github.com/boostorg/core/pull/20#issuecomment-246057202  

Merged, sorry for the delay and thanks for the patience.

---
