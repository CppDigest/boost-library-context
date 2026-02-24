# #369 Fix integer overflow when negating smallest signed value [Closed]

> Username: jwakely  
> Created at: 2018-02-23 18:07:38 UTC  
> Updated at: 2018-02-23 22:38:45 UTC  
> Closed at: 2018-02-23 19:49:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/369  

`-INT_MIN` cannot be represented as `int` so results in an undefined overflow.  
  
Testcase from https://bugzilla.redhat.com/show_bug.cgi?id=1545092#c4  
  
````  
#include <cassert>  
#include <boost/spirit/include/karma.hpp>  
  
bool karma_to_string(std::string & str, int value) __attribute__((noinline));  
  
bool karma_to_string(std::string & str, int value)  
{  
  namespace karma = boost::spirit::karma;  
  std::back_insert_iterator<std::string> sink(str);  
  return karma::generate(sink, value);  
}  
  
int main(int argc, char **argv)  
{  
  std::string out;  
  karma_to_string(out, int(-2147483648));  
  assert(out == "-2147483648");  
  return 0;  
}  
````  
  
With UBSan this shows:  
  
/usr/include/boost/spirit/home/karma/numeric/detail/numeric_utils.hpp:90:5: runtime error: negation of -2147483648 cannot be represented in type 'int'; cast to an unsigned type to negate this value to itself

---

## Comment 1

> Username: Kojoley  
> Created_at: 2018-02-23 18:23:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/369#issuecomment-368095452  

Now I understand what was PR #246 dealing with.

---

## Comment 2

> Username: jwakely  
> Created_at: 2018-02-23 18:25:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/369#issuecomment-368096133  

Oh I missed that one, sorry. @ecatmur's patch probably produces better code.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2018-02-23 19:49:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/369#issuecomment-368119921  

Nothing to be sorry about, thanks for the PR!  
I will merge #246 after a CI cycle.

---

## Comment 4

> Username: djowel  
> Created_at: 2018-02-23 22:38:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/369#issuecomment-368158845  

H @jwakely! Thanks for this patch!

---
