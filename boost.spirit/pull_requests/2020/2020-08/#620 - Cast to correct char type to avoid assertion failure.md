# #620 Cast to correct char type to avoid assertion failure [Merged]

> Username: pepsiman  
> Created at: 2020-08-25 14:15:53 UTC  
> Updated at: 2020-12-04 00:31:01 UTC  
> Merged at: 2020-12-04 00:30:54 UTC  
> Closed at: 2020-12-04 00:30:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/620  

I'm getting an assertion failure in boost::spirit using boost 1.74.0:  
```  
boost/spirit/home/support/char_encoding/standard.hpp:59: static bool boost::spirit::char_encoding::standard::isalnum(int): Assertion `strict_ischar(ch)' failed.  
```  
This is being called from `boost::spirit::char_class::classify<boost::spirit::char_encoding::standard>::is<unsigned int>()`.  
  
Comparing that function with the functions in `home/x3/support/no_case.hpp`, the wrong type is being used in the cast.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2020-08-25 16:57:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/620#issuecomment-680148238  

Could we have a test for this?

---

## Comment 2

> Username: pepsiman  
> Created_at: 2020-08-27 15:55:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/620#issuecomment-682037113  

Is it possible to write a portable test for `char_encoding::standard` using characters greater than 127?

---

## Comment 3

> Username: Kojoley  
> Created_at: 2020-08-27 18:59:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/620#issuecomment-682133548  

> Is it possible to write a portable test for char_encoding::standard using characters greater than 127?  
  
Well, I asked the question having the same doubt and thinking that you have more expertise in the domain since you should be doing it to hit the assertion.

---

## Comment 4

> Username: pepsiman  
> Created_at: 2020-08-27 20:55:22 UTC  
> Url: https://github.com/boostorg/spirit/pull/620#issuecomment-682185941  

The grammar was written by someone else and uses char_encoding::unicode everywhere, so I'm not sure why char_encoding::standard is being invoked.  
  
If I were deliberately using char_encoding::standard, I'd expect it to allow the same inputs as the standard functions.  
  
I suppose a test could check that no assertions fail without caring about the result of the function.

---

## Comment 5

> Username: pepsiman  
> Created_at: 2020-09-02 16:24:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/620#issuecomment-685848251  

Test added.  
  
F1 is a printable character in all charsets on http://www.madore.org/~david/computers/unicode/cstab.html

---

## Comment 6

> Username: sapi33  
> Created_at: 2020-12-03 20:08:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/620#issuecomment-738278642  

Hi,  
  
do you have any clue when this PR is reviewed and considered for merging?  
We also faced the assertion errors in Boost 1.74.0. In our case it was due to method `isspace()` in standard.hpp, which is applied when using `boost::spirit::qi::space` in a parser. E.g. for the input 'Ä' the resulting value -67 does obviously not fulfill the condition >= 0 in `strict_ischar()`. With the PR applied the value is 195 and it seemingly works as intended.

---

## Comment 7

> Username: djowel  
> Created_at: 2020-12-04 00:29:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/620#issuecomment-738470982  

> do you have any clue when this PR is reviewed and considered for merging?  
> We also faced the assertion errors in Boost 1.74.0. In our case it was due to method `isspace()` in standard.hpp, which is applied when using `boost::spirit::qi::space` in a parser. E.g. for the input 'Ä' the resulting value -67 does obviously not fulfill the condition >= 0 in `strict_ischar()`. With the PR applied the value is 195 and it seemingly works as intended.  
  
From above, @Kojoley requested for a test case. Can anyone add one here so we can proceed?

---

## Comment 8

> Username: djowel  
> Created_at: 2020-12-04 00:30:22 UTC  
> Url: https://github.com/boostorg/spirit/pull/620#issuecomment-738471231  

> From above, @Kojoley requested for a test case. Can anyone add one here so we can proceed?  
  
Oh ignore that! I see a test has been added!

---

## Comment 9

> Username: djowel  
> Created_at: 2020-12-04 00:31:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/620#issuecomment-738471459  

Merged.

---
