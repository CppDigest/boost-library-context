# #24 Float to chars [Merged]

> Username: mborland  
> Created at: 2023-03-13 18:58:28 UTC  
> Updated at: 2023-05-09 12:41:47 UTC  
> Merged at: 2023-05-09 12:41:42 UTC  
> Closed at: 2023-05-09 12:41:42 UTC  
> Url: https://github.com/boostorg/charconv/pull/24  



---

## Comment 1

> Username: mborland  
> Created_at: 2023-04-07 15:05:42 UTC  
> Url: https://github.com/boostorg/charconv/pull/24#issuecomment-1500371441  

@pdimov I replaced the anonymous namespaces by templating the structs as you commented. Do you know of a workaround for the following error:  
  
```  
./boost/charconv/detail/floff.hpp:1504:49: error: cannot initialize aggregate of type 'const cache_entry_type [619] {aka const boost::charconv::detail::uint128 [619]}' with a compound literal  
         {0xf70867153aa2db38, 0xb8cbee4fc66d1ea8}};  
```  
  
This only arises on GCC 4.8 and 4.9.

---

## Comment 2

> Username: pdimov  
> Created_at: 2023-04-07 15:29:05 UTC  
> Url: https://github.com/boostorg/charconv/pull/24#issuecomment-1500390306  

I don't, sorry. It's probably because `uint128` is not an aggregate, but has a constructor. Why is GCC 4.x confused by that, I don't know.

---

## Comment 3

> Username: jk-jeon  
> Created_at: 2023-04-07 16:08:18 UTC  
> Url: https://github.com/boostorg/charconv/pull/24#issuecomment-1500423422  

> I don't, sorry. It's probably because `uint128` is not an aggregate, but has a constructor. Why is GCC 4.x confused by that, I don't know.  
  
I guess maybe we can just remove the constructor and make it into an aggregate? Will that fixes the issue?  
  
In Dragonbox/floff projects, that the corresponding struct has a constructor is more or less just a historical artifact, 'cause initially it had only one member if the compiler supports native 128-bit integers. I removed that and just made it to always hold a pair of 64-bits because native 128-bit integers didn't seem to produce good assemblies.

---

## Comment 4

> Username: mborland  
> Created_at: 2023-04-10 12:07:59 UTC  
> Url: https://github.com/boostorg/charconv/pull/24#issuecomment-1501745325  

>   
> I guess maybe we can just remove the constructor and make it into an aggregate? Will that fixes the issue?  
>   
  
That seems to do it. Thanks.

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-05-01 14:50:07 UTC  
> Url: https://github.com/boostorg/charconv/pull/24#issuecomment-1529792958  

An automated preview of the documentation is available at [https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-05-01 15:10:07 UTC  
> Url: https://github.com/boostorg/charconv/pull/24#issuecomment-1529817553  

An automated preview of the documentation is available at [https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-05-02 14:00:16 UTC  
> Url: https://github.com/boostorg/charconv/pull/24#issuecomment-1531530739  

An automated preview of the documentation is available at [https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2023-05-04 11:45:14 UTC  
> Url: https://github.com/boostorg/charconv/pull/24#issuecomment-1534625331  

An automated preview of the documentation is available at [https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-05-04 12:35:17 UTC  
> Url: https://github.com/boostorg/charconv/pull/24#issuecomment-1534701534  

An automated preview of the documentation is available at [https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2023-05-04 13:00:18 UTC  
> Url: https://github.com/boostorg/charconv/pull/24#issuecomment-1534736370  

An automated preview of the documentation is available at [https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2023-05-04 13:15:18 UTC  
> Url: https://github.com/boostorg/charconv/pull/24#issuecomment-1534763248  

An automated preview of the documentation is available at [https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2023-05-05 11:10:13 UTC  
> Url: https://github.com/boostorg/charconv/pull/24#issuecomment-1536095872  

An automated preview of the documentation is available at [https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2023-05-05 13:13:51 UTC  
> Url: https://github.com/boostorg/charconv/pull/24#issuecomment-1536245433  

An automated preview of the documentation is available at [https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2023-05-05 13:40:11 UTC  
> Url: https://github.com/boostorg/charconv/pull/24#issuecomment-1536279428  

An automated preview of the documentation is available at [https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2023-05-05 14:00:12 UTC  
> Url: https://github.com/boostorg/charconv/pull/24#issuecomment-1536306302  

An automated preview of the documentation is available at [https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2023-05-05 14:06:11 UTC  
> Url: https://github.com/boostorg/charconv/pull/24#issuecomment-1536315369  

An automated preview of the documentation is available at [https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2023-05-05 14:20:13 UTC  
> Url: https://github.com/boostorg/charconv/pull/24#issuecomment-1536335787  

An automated preview of the documentation is available at [https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2023-05-08 10:55:13 UTC  
> Url: https://github.com/boostorg/charconv/pull/24#issuecomment-1538172508  

An automated preview of the documentation is available at [https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2023-05-08 13:25:13 UTC  
> Url: https://github.com/boostorg/charconv/pull/24#issuecomment-1538358221  

An automated preview of the documentation is available at [https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2023-05-08 13:55:07 UTC  
> Url: https://github.com/boostorg/charconv/pull/24#issuecomment-1538400677  

An automated preview of the documentation is available at [https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 21

> Username: mborland  
> Created_at: 2023-05-08 14:28:03 UTC  
> Url: https://github.com/boostorg/charconv/pull/24#issuecomment-1538456813  

@pdimov Do you have any further comments on this one? I addressed all of your existing comments along the way.

---

## Comment 22

> Username: pdimov  
> Created_at: 2023-05-08 15:17:41 UTC  
> Url: https://github.com/boostorg/charconv/pull/24#issuecomment-1538548096  

`BOOST_CHARCONV_SAFEBUFFERS` is defined twice.  
  
Check your uses of `_MSC_VER`. It means "MSVC and compatible compilers", but most of the current uses seem to only apply to MSVC. Use `BOOST_MSVC` if you mean "MSVC".

---

## Comment 23

> Username: pdimov  
> Created_at: 2023-05-08 15:19:54 UTC  
> Url: https://github.com/boostorg/charconv/pull/24#issuecomment-1538553854  

> Scientific format will be of the form `+1.3e+3`.  
  
Does scientific include the plus sign for positive numbers?

---

## Comment 24

> Username: mborland  
> Created_at: 2023-05-09 11:11:32 UTC  
> Url: https://github.com/boostorg/charconv/pull/24#issuecomment-1539976472  

> > Scientific format will be of the form `+1.3e+3`.  
>   
> Does scientific include the plus sign for positive numbers?  
  
It does not, and I was also missing the fact that in scientific format the exponent will have a minimum of 2 digits. Correctly represented is: `1.3e+03`.

---

## Comment 25

> Username: cppalliance-bot  
> Created_at: 2023-05-09 11:22:31 UTC  
> Url: https://github.com/boostorg/charconv/pull/24#issuecomment-1539991199  

An automated preview of the documentation is available at [https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://24.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---
