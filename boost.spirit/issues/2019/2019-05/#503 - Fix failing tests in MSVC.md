# #503 - Fix failing tests in MSVC [Closed]

> Username: djowel  
> Created at: 2019-05-02 23:40:28 UTC  
> Updated at: 2019-05-04 19:20:29 UTC  
> Closed at: 2019-05-04 19:20:28 UTC  
> Url: https://github.com/boostorg/spirit/issues/503  

Those tests are failing on MSVC in our CI https://ci.appveyor.com/project/Kojoley/spirit-jod4k/builds/24112491/job/w1lcg5vgrw9mta32?fullLog=true

---

## Comment 1

> Username: djowel  
> Created at: 2019-05-04 01:37:21 UTC  
> Url: https://github.com/boostorg/spirit/issues/503#issuecomment-489283294  

Hmmm, I just tested with VS Studio 2017 and all tests pass just fine.

---

## Comment 2

> Username: djowel  
> Created at: 2019-05-04 01:59:41 UTC  
> Url: https://github.com/boostorg/spirit/issues/503#issuecomment-489284829  

Let me see if I can get VS 2015

---

## Comment 3

> Username: djowel  
> Created at: 2019-05-04 19:20:28 UTC  
> Url: https://github.com/boostorg/spirit/issues/503#issuecomment-489357381  

OK, it should be address-model=64. Fixed.
