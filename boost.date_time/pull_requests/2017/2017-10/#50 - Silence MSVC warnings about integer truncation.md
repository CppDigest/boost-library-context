# #50 Silence MSVC warnings about integer truncation [Merged]

> Username: Lastique  
> Created at: 2017-10-24 21:16:25 UTC  
> Updated at: 2017-11-05 13:49:46 UTC  
> Merged at: 2017-11-05 07:04:11 UTC  
> Closed at: 2017-11-05 07:04:11 UTC  
> Url: https://github.com/boostorg/date_time/pull/50  

Also for day/month/year types use the value_type typedef to accept and return numeric values of the date component.

---

## Comment 1

> Username: mclow  
> Created_at: 2017-10-24 21:49:52 UTC  
> Url: https://github.com/boostorg/date_time/pull/50#issuecomment-339143692  

I think you're going to find that you're going down a rathole here; scattering `static_cast`s all over the place, because the VS compiler throws bogus warnings all over the place. You're basically plastering `static_cast` everywhere to say "SHUT UP, COMPILER!", and you'll end up with a code base that no one wants to touch.  
  
I'd prefer to say "the compiler is wrong to warn here", and leave it at that (unless, of course, you can show a loss of data)

---

## Comment 2

> Username: mclow  
> Created_at: 2017-10-24 21:50:35 UTC  
> Url: https://github.com/boostorg/date_time/pull/50#issuecomment-339143907  

See the comment that I left on #50; that applies here as well.

---

## Comment 3

> Username: Lastique  
> Created_at: 2017-10-24 22:02:03 UTC  
> Url: https://github.com/boostorg/date_time/pull/50#issuecomment-339147541  

Technically, the compiler is not wrong here because there is indeed conversion from `int` to `unsigned short`.  
  
This PR does not add a new `static_cast` but rather fixes the existing one. It was casting to the wrapper class type, which required an implicit conversion to `unsigned short` (the argument type for the conversion constructor). I changed it so that it casts to the argument type, and wrapper constructor is called by the variable initialization.  
  
BTW, I'm not a fan of bogus warnings either, but it happens that I have to run tests of many libraries on MSVC and warnings really complicate reading logs afterwards.  
  
> See the comment that I left on #50; that applies here as well.  
  
Not sure which comment you refer to. #50 is this PR.

---

## Comment 4

> Username: mclow  
> Created_at: 2017-10-24 23:48:42 UTC  
> Url: https://github.com/boostorg/date_time/pull/50#issuecomment-339169942  

> Technically, the compiler is not wrong here because there is indeed conversion from int to unsigned short.  
  
The compiler is wrong here because (as far as I can tell) the code will never overflow.  
If I convert `int(4)` to `unsigned short`, the compiler should not warn.

---

## Comment 5

> Username: mclow  
> Created_at: 2017-10-24 23:49:54 UTC  
> Updated_at: 2017-10-24 23:50:03 UTC  
> Url: https://github.com/boostorg/date_time/pull/50#issuecomment-339170153  

> Not sure which comment you refer to. #50 is this PR.  
  
Sorry; I thought I left that comment on #46

---

## Comment 6

> Username: Lastique  
> Created_at: 2017-10-25 08:30:27 UTC  
> Url: https://github.com/boostorg/date_time/pull/50#issuecomment-339255153  

Maybe I'm missing something, but I don't think the compiler has a way to know the range of `f_`, which means the result of `ymd.year + wi.add(f_)` or `ymd.year + wi.subtract(f_)` may exceed the range of `unsigned short`.  
  
Even if the range can be deduced from a global program analysis, I don't think it is fair to *require* the compiler do that.

---

## Comment 7

> Username: mclow  
> Created_at: 2017-10-25 14:25:43 UTC  
> Url: https://github.com/boostorg/date_time/pull/50#issuecomment-339347646  

I do. Otherwise you get warnings on code like this:  
  
    int foo = 4;  
    short bar = foo;

---

## Comment 8

> Username: Lastique  
> Created_at: 2017-10-25 14:34:58 UTC  
> Url: https://github.com/boostorg/date_time/pull/50#issuecomment-339350796  

When optimizations are disabled, I wouldn't be surprised if you do get warnings on code like that.  
  
Also, the Boost.DateTime code is more complicated than this. The `f_` value does not have to be initialized with a constant and can be a purely runtime value.

---

## Comment 9

> Username: mclow  
> Created_at: 2017-10-25 15:53:16 UTC  
> Url: https://github.com/boostorg/date_time/pull/50#issuecomment-339377955  

> When optimizations are disabled, I wouldn't be surprised if you do get warnings on code like that.  
  
Which is why I believe that this is a bogus warning, and that we should ignore them, rather than cruddying up the code base to shut the compiler up when it is wrong.

---

## Comment 10

> Username: Lastique  
> Created_at: 2017-10-25 17:15:34 UTC  
> Url: https://github.com/boostorg/date_time/pull/50#issuecomment-339403180  

I don't think "just ignore the warning" is a good answer for Boost users. We should either silence it with pragmas or work around it with casts or otherwise.

---

## Comment 11

> Username: mclow  
> Created_at: 2017-10-25 17:21:31 UTC  
> Url: https://github.com/boostorg/date_time/pull/50#issuecomment-339404968  

My advice is to silence it, then - because I believe that it's broken.

---

## Comment 12

> Username: eldiener  
> Created_at: 2017-11-05 00:58:12 UTC  
> Url: https://github.com/boostorg/date_time/pull/50#issuecomment-341941262  

I would like to merge this PR as it now stands. Anyone have any objections ?

---

## Comment 13

> Username: mclow  
> Created_at: 2017-11-05 03:10:30 UTC  
> Url: https://github.com/boostorg/date_time/pull/50#issuecomment-341945949  

I still think this is the wrong approach.  But if you want to land it, I won't forbid.

---

## Comment 14

> Username: Lastique  
> Created_at: 2017-11-05 11:48:56 UTC  
> Url: https://github.com/boostorg/date_time/pull/50#issuecomment-341967241  

Thanks. You might also want to close issue https://github.com/boostorg/date_time/issues/48.

---

## Comment 15

> Username: eldiener  
> Created_at: 2017-11-05 13:49:46 UTC  
> Url: https://github.com/boostorg/date_time/pull/50#issuecomment-341974539  

I closed the issue.

---
