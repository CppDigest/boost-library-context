# #232 #231 Added pfr extension [Closed]

> Username: denzor200  
> Created at: 2021-03-27 19:36:37 UTC  
> Updated at: 2022-01-21 18:31:34 UTC  
> Closed at: 2022-01-21 18:31:34 UTC  
> Url: https://github.com/boostorg/fusion/pull/232  

So, here is my implementation of pfr extension for boost fusion library. This extension may be useful for some boost spirit users.  
I am ready to answer any question you may have to facilitate the consideration of this PR. Thank you for attention.

---

## Comment 1

> Username: daminetreg  
> Created_at: 2021-03-29 11:14:22 UTC  
> Url: https://github.com/boostorg/fusion/pull/232#issuecomment-809295966  

Looks interesting 🙂 I have to accustomate myself with pfr first before reviewing here.

---

## Comment 2

> Username: djowel  
> Created_at: 2021-03-29 22:52:38 UTC  
> Url: https://github.com/boostorg/fusion/pull/232#issuecomment-809771651  

I haven't inspected... Why are the checks failing?

---

## Comment 3

> Username: denzor200  
> Created_at: 2021-03-30 18:41:12 UTC  
> Url: https://github.com/boostorg/fusion/pull/232#issuecomment-810490276  

> I haven't inspected... Why are the checks failing?  
  
Yes! There is some checks failing..   
All on my side - I'll fix it during this or next week.  
But i can't fix AppVeyor-side fails, just like this:  
`'cl' is not recognized as an internal or external command`

---

## Comment 4

> Username: denzor200  
> Created_at: 2021-06-21 20:31:35 UTC  
> Url: https://github.com/boostorg/fusion/pull/232#issuecomment-865325069  

@djowel i'm done. Appveyor's checks was fixed. Travis was fixed too, but now we cant verify this))  
It was tested with boost.spirt.x3 in my project, in my opiniton this extension is good.  
You can start to inspect, if you wish.

---

## Comment 5

> Username: djowel  
> Created_at: 2021-06-21 21:06:20 UTC  
> Url: https://github.com/boostorg/fusion/pull/232#issuecomment-865343919  

I'll look and study tomorrow

---

## Comment 6

> Username: denzor200  
> Created_at: 2021-09-01 10:37:49 UTC  
> Url: https://github.com/boostorg/fusion/pull/232#issuecomment-910157259  

Hello everyone! This PR will be extended in the future. I suppose, we need an alternative way to tell fusion that structure must be serialized by pfr, it's way without 'adapt' macro.  
```  
#include <iostream>  
#include <boost/spirit/home/x3.hpp>  
#include <boost/fusion/adapted/pfr.hpp>  
   
constexpr std::string_view str = "100 200";  
   
struct A  
{  
    int first;  
    int second;  
};  
  
/// No BOOST_FUSION_ADAPT_STRUCT here! And no BOOST_FUSION_ADAPT_PFR too..  
   
int main()  
{  
    A rec;  
    auto iter = str.begin();  
    auto const end = str.end();  
    using boost::spirit::x3::int_;  
    bool r = boost::spirit::x3::parse(iter, end, int_ >> ' ' >> int_, boost::fusion::pfr(rec));  
    std::cout << "done (" << std::boolalpha << (r && iter == end) << ")" << std::endl;  
    return 0;  
}  
```  
  
```  
#include <iostream>  
#include <boost/spirit/home/x3.hpp>  
#include <boost/fusion/adapted/pfr.hpp>  
  
constexpr std::string_view str = "100 200";  
  
struct A  
{  
    int first;  
    int second;  
};  
  
boost::spirit::x3::rule<class test, boost::fusion::pfr_reference<A>> const test = "test";  
auto const test_def = boost::spirit::x3::int_ >> ' ' >> boost::spirit::x3::int_;  
BOOST_SPIRIT_DEFINE(test);  
  
int main()  
{  
    A rec;  
    auto iter = str.begin();  
    auto const end = str.end();  
    bool r = boost::spirit::x3::parse(iter, end, test, boost::fusion::pfr(rec));  
    std::cout << "done (" << std::boolalpha << (r && iter == end) << ")" << std::endl;  
    return 0;  
}  
```

---

## Comment 7

> Username: djowel  
> Created_at: 2021-09-04 06:13:27 UTC  
> Updated_at: 2021-09-04 06:15:09 UTC  
> Url: https://github.com/boostorg/fusion/pull/232#issuecomment-912917300  

All checks have failed? What's up with that?  
  
Still having 'cl' is not recognized as an internal or external command... it seems. Hrmmm...

---

## Comment 8

> Username: denzor200  
> Created_at: 2021-09-08 19:49:30 UTC  
> Url: https://github.com/boostorg/fusion/pull/232#issuecomment-915523002  

> Hello everyone! This PR will be extended in the future. I suppose, we need an alternative way to tell fusion that structure must be serialized by pfr, it's way without 'adapt' macro.  
> ...  
>   
  
Addition to my previous comment.. Consider this simple fusion-able structure:  
```  
struct A  
{  
    int first;  
    int second;  
    int third;  
};  
  
BOOST_FUSION_ADAPT_STRUCT(A, first, second/*, third*/);  
```  
  
I suggest giving the user of the library the ability to choose from two ways:  
**1. boost::fusion::pfr approach**  
```  
auto a = A{100, 200, 300};  
boost::fusion::for_each(boost::fusion::pfr(a), [](auto val) {  
	std::cout << val << " ";  
});  
```  
Will print _"100 200 "_, because `boost::fusion::pfr` is intended to indicate that boost.fusion SHOULD serialize value using boost.pfr library.   
  
  
**2. boost::fusion::pfr_fields approach**  
```  
auto a = A{100, 200, 300};  
boost::fusion::for_each(boost::fusion::pfr_fields(a), [](auto val) {  
	std::cout << val << " ";  
});   
```  
Will print _"100 200 300 "_ because `boost::fusion::pfr_fields` is intended to indicate that boost.fusion MUST serialize value using boost.pfr library. This approach do ignore another ways to serialize value and work only with pfr library.  
  
For a structure that is not fusion-able, it makes no difference which way to choose, because `boost::fusion::pfr` and `boost::fusion::pfr_fields` behave the same in this case - both look to pfr for serialization.  
  
@djowel what do you say about this?  
PS Inspired by `boost::pfr::io` && `boost::pfr::io_fields` pair from the pfr library.  
https://www.boost.org/doc/libs/1_75_0/doc/html/boost_pfr/tutorial.html#boost_pfr.tutorial.three_ways_of_getting_operators

---

## Comment 9

> Username: denzor200  
> Created_at: 2022-01-21 18:31:34 UTC  
> Url: https://github.com/boostorg/fusion/pull/232#issuecomment-1018760159  

Duplicate of the https://github.com/boostorg/fusion/pull/243

---
