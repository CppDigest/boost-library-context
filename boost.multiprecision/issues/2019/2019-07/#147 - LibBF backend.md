# #147 - LibBF backend [Open]

> Username: mekhontsev  
> Created at: 2019-07-27 14:33:43 UTC  
> Updated at: 2020-02-03 09:01:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/147  

It would be nice to have LibBF (MIT license) backend: [https://bellard.org/libbf/](https://bellard.org/libbf/)

---

## Comment 1

> Username: ram-nad  
> Created at: 2020-01-31 08:25:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/147#issuecomment-580634789  

Sir, I think I could work on this issue. Could you please help me in getting started?

---

## Comment 2

> Username: ckormanyos  
> Created at: 2020-02-02 13:12:44 UTC  
> Updated at: 2020-02-02 13:13:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/147#issuecomment-581134234  

>> It would be nice to have LibBF (MIT license) backend  
  
> I think I could work on this issue. Could you please help me in getting started?  
  
Please observe possible maturity of [potential GSoC 2020 projects](http://github.com/boostorg/boost/wiki/Google-Summer-of-Code:-2020).  
  
I have extended the second project to also include a potential focus on the _libbf_ backend mentioned here.   
  
(Although I also mention that I observe potential opportunities to modify the backend mentioned here such that it avoids direct use of GCC specifics such as built-in bitfield manipulations like _clz-whatever_. This backend actually seems relatively so close to actually being portable, a little effort could make it be so.)

---

## Comment 3

> Username: jzmaddock  
> Created at: 2020-02-02 15:36:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/147#issuecomment-581146989  

In the mean time, I'm working on a "skeleton" backend that can be converted into a "real" backend with a bit of search and replace plus some fill-in-the-blanks kind of thing.  Should make life a bit easier/quicker for writing new backends, though as ever it's the testing that takes the time.  
  
BTW it would be nice to add libdecnumber support too:   
https://github.com/boostorg/multiprecision/issues/74  
https://github.com/boostorg/multiprecision/issues/73

---

## Comment 4

> Username: ckormanyos  
> Created at: 2020-02-02 21:51:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/147#issuecomment-581181390  

> In the mean time, I'm working on a "skeleton" backend that can be converted into a "real" backend  
  
Many thanks, John. That is a great idea.  
  
> BTW it would be nice to add libdecnumber support too:  
  
I will mention these to in the project proposal.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2020-02-03 09:01:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/147#issuecomment-581307200  

The "skeleton" backend, plus some instructions on writing new backends is in this commit: https://github.com/boostorg/multiprecision/commit/6f73233965a56300b57253196c1976d08a10f2bb.
