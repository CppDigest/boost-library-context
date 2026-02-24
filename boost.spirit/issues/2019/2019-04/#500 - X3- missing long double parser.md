# #500 - X3: missing long double parser [Closed]

> Username: Xeverous  
> Created at: 2019-04-29 15:30:14 UTC  
> Updated at: 2019-05-04 09:51:14 UTC  
> Closed at: 2019-05-04 09:51:14 UTC  
> Url: https://github.com/boostorg/spirit/issues/500  

https://github.com/boostorg/spirit/blob/8d5429ad7b69a6a1b479f4216ef200a368472de2/include/boost/spirit/home/x3/numeric/real.hpp#L54-L58  
  
There is no `long_double` parser as mentioned by documentation  
  
https://www.boost.org/doc/libs/1_70_0/libs/spirit/doc/x3/html/spirit_x3/quick_reference/numeric.html

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-04-29 21:26:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/500#issuecomment-487750147  

Do you need that parser or you just pointing to the documentation problem?

---

## Comment 2

> Username: Xeverous  
> Created at: 2019-04-29 22:07:06 UTC  
> Url: https://github.com/boostorg/spirit/issues/500#issuecomment-487761209  

I don't need the parser (even if did, I could just write `real_parser<long double>`). Just pointing out mismatch between documentation and implementation.

---

## Comment 3

> Username: djowel  
> Created at: 2019-05-02 23:34:15 UTC  
> Url: https://github.com/boostorg/spirit/issues/500#issuecomment-488868732  

I'll add this. Thanks for noting!

---

## Comment 4

> Username: djowel  
> Created at: 2019-05-04 09:51:14 UTC  
> Url: https://github.com/boostorg/spirit/issues/500#issuecomment-489312499  

Done. Added some tests as well and along with it some tweaks to take advantage of constexpr in the pow10 tables.
