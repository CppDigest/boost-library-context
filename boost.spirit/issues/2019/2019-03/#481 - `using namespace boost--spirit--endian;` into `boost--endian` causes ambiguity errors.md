# #481 - `using namespace boost::spirit::endian;` into `boost::endian` causes ambiguity errors [Closed]

> Username: pdimov  
> Created at: 2019-03-12 13:43:09 UTC  
> Updated at: 2019-05-02 23:48:43 UTC  
> Closed at: 2019-03-13 22:05:08 UTC  
> Url: https://github.com/boostorg/spirit/issues/481  

The using directive in  
  
https://github.com/boostorg/spirit/blob/5a80befedd5f9821d0d0ea890a7edd6d84d5a561/include/boost/spirit/home/support/detail/endian/endian.hpp#L560-L563  
  
causes ambiguity errors when using Spirit and Endian together. A library is not supposed to inject things into another library's namespace. Please fix this.

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-03-12 13:52:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/481#issuecomment-472009177  

I noticed it recently that Spirit injects into endian namespace, but as you may know, this was living before Endian appeared. I would like to drop internal endian stuff completely, but Endian does not support floats and from the docs it looks like Endian does not want to support floats either. Moreover it does not case ambiguity errors for me, I tried with:  
  
```cpp  
#include <boost/spirit/home/support/detail/endian/endian.hpp>  
#include <boost/endian/conversion.hpp>  
#include <boost/endian/arithmetic.hpp>  
int main(){}  
```

---

## Comment 2

> Username: djowel  
> Created at: 2019-03-12 13:58:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/481#issuecomment-472012029  

> I noticed it recently that Spirit injects into endian namespace, but as you may know, this was living before Endian appeared.   
  
I did not know about this. Do you know when this happened?

---

## Comment 3

> Username: Kojoley  
> Created at: 2019-03-12 14:01:18 UTC  
> Url: https://github.com/boostorg/spirit/issues/481#issuecomment-472013000  

> Do you know when this happened?  
  
From the very beginning, in 0d75b4307afa27724e794dd29f503e219bb6805f

---

## Comment 4

> Username: djowel  
> Created at: 2019-03-12 14:05:31 UTC  
> Url: https://github.com/boostorg/spirit/issues/481#issuecomment-472014675  

OK, thoughts, @hkaiser ?

---

## Comment 5

> Username: pdimov  
> Created at: 2019-03-12 14:08:37 UTC  
> Url: https://github.com/boostorg/spirit/issues/481#issuecomment-472015888  

I'm not saying that you need to drop anything. Just don't mess with `namespace boost::endian`.

---

## Comment 6

> Username: Kojoley  
> Created at: 2019-03-12 14:20:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/481#issuecomment-472020669  

> I'm not saying that you need to drop anything. Just don't mess with `namespace boost::endian`.  
  
And I am not saying this should stay, what I am trying to tell you that it had not been there for a long time already if Endian supported floats. IIUC that is the reason it outlived adding the Endian library.

---

## Comment 7

> Username: pdimov  
> Created at: 2019-03-12 14:28:24 UTC  
> Url: https://github.com/boostorg/spirit/issues/481#issuecomment-472023833  

The ambiguity was reported in https://github.com/boostorg/endian/issues/33.  
  
Re floats, the Endian library is probably due to an internal rewrite, I don't know when I'll find the time to get to it. Periodical reminders might help. :-)  
  
(Its whole underlying idea - that a `T`, with endianness reversed, can still be stored as `T` and passed and returned by value, is flawed.)

---

## Comment 8

> Username: Kojoley  
> Created at: 2019-03-12 15:06:11 UTC  
> Updated at: 2019-03-12 16:16:15 UTC  
> Url: https://github.com/boostorg/spirit/issues/481#issuecomment-472039640  

> Re floats, the Endian library is probably due to an internal rewrite, I don't know when I'll find the time to get to it. Periodical reminders might help. :-)  
  
I have https://github.com/Kojoley/endian/commit/452222c9d9d57140c1fca93ee3e8ca66a2b710ed in my stash, if you ok with the general idea I can prepare a PR.  
  
> (Its whole underlying idea - that a `T`, with endianness reversed, can still be stored as `T` and passed and returned by value, is flawed.)  
  
Unaligned Endian types store the value as array of chars and does type punning on store/load. Aligned ones could make the same with C++11 `alignas`, and on C++03 store as unsigned integers as long as there is unsigned integer type with the requested size and it does not have a trap representation.

---

## Comment 9

> Username: pdimov  
> Created at: 2019-03-12 15:37:06 UTC  
> Url: https://github.com/boostorg/spirit/issues/481#issuecomment-472052459  

I need more context to evaluate this patch - what code doesn't work before it and works after it?

---

## Comment 10

> Username: Kojoley  
> Created at: 2019-03-12 16:17:53 UTC  
> Updated at: 2019-03-12 16:24:43 UTC  
> Url: https://github.com/boostorg/spirit/issues/481#issuecomment-472070601  

> I need more context to evaluate this patch - what code doesn't work before it and works after it?  
  
```cpp  
#include <boost/endian/arithmetic.hpp>  
  
int main()  
{  
    boost::endian::endian_arithmetic<boost::endian::order::big, float, sizeof(float) * 8> a;  
    a = 123.4f;  
    float b = a;  
    return b == 123.4f ? 0 : 1;  
}  
```  
  
PS: I tried to rebase the patch on top of the current develop and it is no longer works, https://github.com/boostorg/endian/commit/9f67f19d35408943bb2692022e189df7531d824d broke it =\

---

## Comment 11

> Username: pdimov  
> Created at: 2019-03-12 18:53:55 UTC  
> Url: https://github.com/boostorg/spirit/issues/481#issuecomment-472135775  

I rewrote load/store and now the above float example works, but the test using  
  
```  
  class MyInt  
  {  
    int32_t mx;  
  public:  
    MyInt(int32_t x = 0) : mx(x) {}  
    operator int32_t() const {return mx;}  
  
    //friend int32_t operator+(const MyInt& x) {return x;}  
  };  
```  
  
now fails, because MyInt isn't trivial and g++ 8 warns on memcpy-ing it. :-/

---

## Comment 12

> Username: Kojoley  
> Created at: 2019-03-12 19:03:00 UTC  
> Url: https://github.com/boostorg/spirit/issues/481#issuecomment-472138920  

It is ok, is not it?  
  
PS: I rebased the patch https://github.com/Kojoley/endian/commit/9becd36acfa67de2ef4d07444f58827c4e5045f0

---

## Comment 13

> Username: pdimov  
> Created at: 2019-04-30 16:17:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/481#issuecomment-488017128  

The Endian library (on develop) now supports `float` and `double`.

---

## Comment 14

> Username: Kojoley  
> Created at: 2019-05-01 18:55:00 UTC  
> Url: https://github.com/boostorg/spirit/issues/481#issuecomment-488378836  

> The Endian library (on develop) now supports `float` and `double`.  
  
That's awesome, thanks!

---

## Comment 15

> Username: djowel  
> Created at: 2019-05-02 23:48:43 UTC  
> Url: https://github.com/boostorg/spirit/issues/481#issuecomment-488871593  

Great! Thanks,  Peter!
