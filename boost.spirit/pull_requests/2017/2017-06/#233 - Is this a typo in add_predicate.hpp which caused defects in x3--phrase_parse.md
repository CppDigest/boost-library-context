# #233 Is this a typo in add_predicate.hpp which caused defects in x3::phrase_parse? [Merged]

> Username: glacierx  
> Created at: 2017-06-19 07:35:37 UTC  
> Updated at: 2017-11-24 08:36:00 UTC  
> Merged at: 2017-11-24 08:36:00 UTC  
> Closed at: 2017-11-24 08:36:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/233  

template <typename Iterator, typename Context  
          , typename RContext, typename Attribute>  
        bool parse(Iterator& first, Iterator const& last  
          , Context const& context, RContext& rcontext, Attribute& /*attr*/) const  
        {  
            Iterator i = first;  
            return this->subject.parse(i, last, context, rcontext, unused);  
        }  
  
Guys,  
Please take a look at : [add_predicate.hpp](https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/x3/operator/and_predicate.hpp)  
The parameter _attr_ is commented and an unused is passed instead which will cause x3::parse_phrase left input attribute result unchanged. Is that a typo?

---

## Comment 1

> Username: djowel  
> Created_at: 2017-06-19 09:09:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/233#issuecomment-309382815  

Not sure. Could you provide a test case that exhibits the problem?

---

## Comment 2

> Username: glacierx  
> Created_at: 2017-06-19 09:50:06 UTC  
> Url: https://github.com/boostorg/spirit/pull/233#issuecomment-309392392  

[emtpy_attribute.cpp](https://github.com/glacierx/spirit/blob/develop/test/x3/emtpy_attribute.cpp)

---

## Comment 3

> Username: djowel  
> Created_at: 2017-06-19 10:12:06 UTC  
> Url: https://github.com/boostorg/spirit/pull/233#issuecomment-309397611  

Ah, I should have said "minimal" as in: http://boost-spirit.com/home/feedback-and-support/

---

## Comment 4

> Username: glacierx  
> Created_at: 2017-06-19 10:47:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/233#issuecomment-309405034  

Updated.  
[empty_attribute.cpp](https://github.com/glacierx/spirit/blob/develop/test/x3/emtpy_attribute.cpp)  
  
Or you can try the live version from [here](http://coliru.stacked-crooked.com/a/a5591ed449c88ca5)

---

## Comment 5

> Username: djowel  
> Created_at: 2017-06-19 11:28:15 UTC  
> Updated_at: 2017-06-19 11:29:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/233#issuecomment-309413033  

Please try to make it more minimal,  stripped down to the most essential functionality, while still providing enough information to point out the problem.  (see the link above).

---

## Comment 6

> Username: glacierx  
> Created_at: 2017-06-19 13:59:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/233#issuecomment-309448127  

[Live demo](http://coliru.stacked-crooked.com/a/f3142d4ae8e6b72f)

---

## Comment 7

> Username: glacierx  
> Created_at: 2017-06-19 14:09:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/233#issuecomment-309451030  

[This](https://wandbox.org/permlink/68QiVmHjjDr9yfCV) is more stable. However this case reproduce the empty attribute but doesn't crash the attribute printing.

---

## Comment 8

> Username: glacierx  
> Created_at: 2017-06-19 14:13:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/233#issuecomment-309452377  

[This](http://coliru.stacked-crooked.com/a/a5591ed449c88ca5) one crashed the attribute printing.

---
