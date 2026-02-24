# #350 - parse_nan() dereferences out of range iterator [Closed]

> Username: oracleoftroy  
> Created at: 2018-01-04 22:49:16 UTC  
> Updated at: 2018-01-05 23:20:55 UTC  
> Closed at: 2018-01-05 23:20:55 UTC  
> Url: https://github.com/boostorg/spirit/issues/350  

In parse_nan(), after detecting the string "nan", while looking for parens, there is no check to make sure we didn't already reach the end of the range before dereferencing the iterator.  
  
https://github.com/boostorg/spirit/blob/54d5ea1ea84013c66a138db57e6275a5aaed5035/include/boost/spirit/home/x3/numeric/real_policies.hpp#L112-L116  
  
I believe the second `if` (line 115) should  look like:  
  
`if (first != last && *first == '(')`  
  
This causes asserts to trigger with debug iterators in MSVC when using std::string or std::string_view. Here's an example with a custom iterator that throws when dereferencing the end.  
  
http://coliru.stacked-crooked.com/a/4301f38c79b25ed6

---

## Comment 1

> Username: djowel  
> Created at: 2018-01-04 23:07:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/350#issuecomment-355425172  

Good catch! Will you issue a PR for this?

---

## Comment 2

> Username: Kojoley  
> Created at: 2018-01-04 23:11:38 UTC  
> Url: https://github.com/boostorg/spirit/issues/350#issuecomment-355426000  

A smiley thing that Qi has the check https://github.com/boostorg/spirit/blob/54d5ea1ea84013c66a138db57e6275a5aaed5035/include/boost/spirit/home/qi/numeric/real_policies.hpp#L128-L132

---

## Comment 3

> Username: djowel  
> Created at: 2018-01-04 23:21:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/350#issuecomment-355427845  

@Kojoley, we should probably start thinking about unifying the common X3 and Qi facilities.

---

## Comment 4

> Username: Kojoley  
> Created at: 2018-01-04 23:36:38 UTC  
> Url: https://github.com/boostorg/spirit/issues/350#issuecomment-355430353  

Actually I would prefer that string to number parsing be a separate library. Current spirit parser does not use any speed up tricks except loop unrolling. Even `nan` parsing could be done in one full sized register compare while currently it loops over chars like an LL(1) parser.

---

## Comment 5

> Username: djowel  
> Created at: 2018-01-04 23:38:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/350#issuecomment-355430724  

> Actually I would prefer that string to number parsing be a separate library. Current spirit parser does not use any speed up tricks except loop unrolling. Even nan parsing could be done in one full sized register compare while currently it loops over chars like an LL(1) parser.  
  
Interesting ideas!

---

## Comment 6

> Username: octopus-prime  
> Created at: 2018-01-05 06:48:12 UTC  
> Url: https://github.com/boostorg/spirit/issues/350#issuecomment-355484858  

> we should probably start thinking about unifying the common X3 and Qi facilities  
  
Does this mean that Qi and X3 should share code?  
The shared code has to be in C++03?!  
So we give up the advantage of C++14 for a separated X3 impl...

---

## Comment 7

> Username: djowel  
> Created at: 2018-01-05 22:07:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/350#issuecomment-355678472  

> Does this mean that Qi and X3 should share code?  
> The shared code has to be in C++03?!  
> So we give up the advantage of C++14 for a separated X3 impl...  
  
Very good point actually. No, X3 should be free from C++03. But, it is very possible to separate 03 code from 14, in separate files even, just like what I did with Fusion, for example.
