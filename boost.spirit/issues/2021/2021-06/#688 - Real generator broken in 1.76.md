# #688 - [Karma] Real generator broken in 1.76 [Open]

> Username: chambm  
> Created at: 2021-06-03 22:21:53 UTC  
> Updated at: 2025-05-09 10:29:10 UTC  
> Url: https://github.com/boostorg/spirit/issues/688  

```  
#include <iostream>  
#include <string>  
#include <limits>  
#include <algorithm>  
#include <boost/spirit/include/karma.hpp>  
  
int main()  
{  
    std::cout << BOOST_VERSION << std::endl;  
      
    double values[] = { 0.9999, 1.9999, 2.9999, 3.9999, 4.9999, 5.9999, 6.9999, 7.9999, 8.9999, 9.9, 9.9999, 10.99, 10.9999, 11.999, 11.9999 };};  
    for (auto value : values)  
    {  
        std::string s;  
        std::back_insert_iterator<std::string> sink(s);  
        boost::spirit::karma::generate(sink, boost::spirit::karma::real_generator<>(), value);  
        std::cout << s << std::endl;  
    }  
}  
```  
  
At Coliru as of 6/3/2021, it incorrectly generates:  
```  
107600  
1.0  
2.0  
3.0  
4.0  
5.0  
6.0  
7.0  
8.0  
9.0  
9.9  
1.0  
10.99  
1.0  
11.999  
1.0  
```  
  
At rextester it correctly generates:  
```  
106501  
1.0  
2.0  
3.0  
4.0  
5.0  
6.0  
7.0  
8.0  
9.0  
9.9  
10.0  
10.99  
11.0  
11.999  
12.0  
```  
  
I'm not sure when it broke, and it's not broken for every input value, but for fairly mundane values it's very wrong. It seems to have something to do with the number of decimals places.

---

## Comment 1

> Username: Kojoley  
> Created at: 2021-06-04 00:37:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/688#issuecomment-854273026  

Caused by #629. @hkaiser any ideas?

---

## Comment 2

> Username: chambm  
> Created at: 2021-06-07 13:44:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/688#issuecomment-855942355  

```  
                if (integer_part >= 10.)  
                {  
                    integer_part /= 10.;  
                    ++dim;  
                }  
```  
Looks very suspect for this bug. :) I can confirm that reverting #629 fixes the issue.

---

## Comment 3

> Username: fiesh  
> Created at: 2021-06-09 11:20:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/688#issuecomment-857611358  

Oh good, there's already a ticket for this.  This hit us pretty hard in production code.

---

## Comment 4

> Username: hkaiser  
> Created at: 2021-06-09 14:48:33 UTC  
> Url: https://github.com/boostorg/spirit/issues/688#issuecomment-857760801  

> Looks very suspect for this bug. :) I can confirm that reverting #629 fixes the issue.  
  
We'll have to find another fix for #628, then :/

---

## Comment 5

> Username: RockinRoel  
> Created at: 2021-11-22 12:28:04 UTC  
> Url: https://github.com/boostorg/spirit/issues/688#issuecomment-975472438  

My guess is that the code @chambm mentioned misses the `0 == (Policies::fmtflags::fixed & flags)` check that is used below to add the exponent.

---

## Comment 6

> Username: Kojoley  
> Created at: 2021-11-22 14:05:11 UTC  
> Url: https://github.com/boostorg/spirit/issues/688#issuecomment-975561527  

IIRC from my investigation back then: in any mode the value is rounded, for `fmtflags:fixed` to the number of digits (it is useful, but counterintuitive, because the standard library iostream does round in fixed mode), the issue is that it is not rounded in all the cases and the workaround in #629 just traded one cases for others. I have a fix but it uses `pow10(trunc(floor(log10(abs_n))))` which seems to be pretty heavy on performance and I still does not have a way to prove its correctness for all the cases.

---

## Comment 7

> Username: Kojoley  
> Created at: 2021-11-22 14:14:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/688#issuecomment-975571581  

Ah, I remembered that my fix also introduces 2 ULP error for max cases in scientific mode which might imply there are should be more, and I did not feel it is an acceptable, so I abandoned the whole idea.

---

## Comment 8

> Username: fiesh  
> Created at: 2022-05-09 11:04:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/688#issuecomment-1120956386  

This was fixed with the release of boost 1.79.0 and should therefore be closed I think.

---

## Comment 9

> Username: knogas  
> Created at: 2022-08-04 17:46:41 UTC  
> Url: https://github.com/boostorg/spirit/issues/688#issuecomment-1205577535  

We've upgraded to Boost 1.79.0 from 1.68.0 and have a unit test failure for the following:  
  
A double of 3.0000000000000001e-06 is being returned as the following string: "3.000000000e-06" whereas we are expecting "3.0e-06" which is what was returned with Boost 1.68.0.

---

## Comment 10

> Username: friedrichatgc  
> Created at: 2023-11-27 20:57:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/688#issuecomment-1828594205  

Please see also my last comment on #736 which fixed a similar issue but introduced a new issue:  
3.1 is output as 3.01000000000000001 if precision is >=16.
