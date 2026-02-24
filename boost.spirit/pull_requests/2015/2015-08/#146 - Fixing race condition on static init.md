# #146 Fixing race condition on static init [Merged]

> Username: biocomp  
> Created at: 2015-08-05 17:48:32 UTC  
> Updated at: 2015-08-12 21:25:23 UTC  
> Merged at: 2015-08-12 08:52:18 UTC  
> Closed at: 2015-08-12 08:52:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/146  

Static variables depend on each other and when static init is not thread-safe, this leads to race condition and failures. Ticket: https://svn.boost.org/trac/boost/ticket/11514

---

## Comment 1

> Username: K-ballo  
> Created_at: 2015-08-05 17:58:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/146#issuecomment-128091835  

The fix isn't actually a fix, is it? I'm surprised that you see a tear because of lack of magic statics on a platform that VS supports, but if you do see it I don't see how this fixes it. Would you explain what the issue is and how to reproduce it?

---

## Comment 2

> Username: biocomp  
> Created_at: 2015-08-05 18:41:00 UTC  
> Updated_at: 2015-08-05 18:42:42 UTC  
> Url: https://github.com/boostorg/spirit/pull/146#issuecomment-128103810  

The repro is just starting multiple threads which trigger the parser method. And though rarely, race condition happens and val becomes 0 (for me). I don't have a nice repro, I was testing with my ugly actual code.  
  
I guess you're right saying that this is not complete fix. Indeed, there's still a problem with static init, it's still not thread safe. BTW, good link about VS's implemenhtation of the thing: http://www.lenholgate.com/blog/2014/04/bitten-by-scoped-static-initialisation-in-c---turn-on-visual-studios-c4640-warning.html  
  
Basically, the static init is this (again, note that there's no thread safety here whatsoever):  
1. Check flag if initialized.  
2. If yes, goto other code.  
3. Init static variable  
4. Other code  
  
Clearly, two threads may check flag and go init variable several times, or (worse), thread may see "initialized" being set and skip init, while other thread did not actually finish the init. That second scenario will give problems with my fix as well.  
  
However, using TWO variables together makes things worse. If they're independent, init will happen EVENTUALLY, so maybe some threads will get incorrect value on the first run, but some time later one of the threads will set static variables and from there, it'll have valid value.  
  
With two variables, one scenario is:  
1. Race condition happens, and our current thread sees "max" being initialized, while the other thread has not done that yet, so max has some garbage value.  
2. Our thread proceeds initializing "val" with incorrect max value, and stores "val" to be incorrect forever (well, for the live of the process).  
  
That is what I've been experiencing, and to me, the fix is good enough.  
  
Now, for C++11 (with constexpr support, not msvc) compilers, of course constexpr should be used. But I'm not sure what boost magic to use (and if there's any) to fix this for both constexpr supporting compilers and for older ones.

---

## Comment 3

> Username: djowel  
> Created_at: 2015-08-05 23:27:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/146#issuecomment-128182871  

> Now, for C++11 (with constexpr support, not msvc) compilers, of course constexpr   
> should be used. But I'm not sure what boost magic to use (and if there's any) to fix   
> this for both constexpr supporting compilers and for older ones.  
  
BOOST_CONSTEXPR_OR_CONST perhaps?

---

## Comment 4

> Username: biocomp  
> Created_at: 2015-08-06 00:30:43 UTC  
> Updated_at: 2015-08-06 17:07:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/146#issuecomment-128193212  

> BOOST_CONSTEXPR_OR_CONST perhaps?  
  
This will make it to be const for non-constexpr compilers. Depending on compiler, it could be all optimized-out, but in worst-case scenario, call to max() will be there and done every time. Could affect performance. Not sure if that is really a big problem.

---

## Comment 5

> Username: djowel  
> Created_at: 2015-08-06 00:41:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/146#issuecomment-128195011  

I think a better approach is to use boost integer_traits::const_min and const_max, if T is a built-in integer, at least for compilers without constexpr.

---

## Comment 6

> Username: K-ballo  
> Created_at: 2015-08-06 00:57:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/146#issuecomment-128197624  

If the operations are expected to be cheap, using automatic storage instead of static storage would be a fix for the potential race. I'm having trouble imagining a bounded integer for which those operations or the values themselves would be costly.  
  
If the operations are expected or suspected to be expensive, making them global statics would solve the potential race for compilers without magic statics. Implementations with magic statics would potentially benefit as well, magic statics are not free as they involve some sort of `call_once`.

---

## Comment 7

> Username: djowel  
> Created_at: 2015-08-06 01:19:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/146#issuecomment-128200122  

in most cases, T is a builtin integer or a builtin floating point type. i think making them use const with automatic storage is the best approach plus boost integer_traits::const_min and const_max when constexpr is not available.

---

## Comment 8

> Username: djowel  
> Created_at: 2015-08-06 01:27:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/146#issuecomment-128200843  

Also, IIRC, this code won't be hit when T is a floating point type because check_overflow will be false for these types.

---

## Comment 9

> Username: biocomp  
> Created_at: 2015-08-06 04:05:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/146#issuecomment-128229226  

I guess I can't get away with my simple change, can't I? :) But you're right of course, let me see what would be the nicest way to use some sort of compile-time constants.  
  
Also, with all that modular movement, is it OK to take dependency on integer traits library (if not already depending on it).

---

## Comment 10

> Username: biocomp  
> Created_at: 2015-08-12 05:30:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/146#issuecomment-130175538  

Updated the code to use compile-time constants.

---

## Comment 11

> Username: djowel  
> Created_at: 2015-08-12 06:24:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/146#issuecomment-130184609  

Wonderful! Now it remains to be seen if the tests will fail on float or double T. I assume you ran the tests? Thanks a lot!

---

## Comment 12

> Username: djowel  
> Created_at: 2015-08-12 06:25:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/146#issuecomment-130184781  

integer traits is fairly low level and I assume it's part of the core boost. in any case, i don't mind the extra dependency on such a trivial header.

---

## Comment 13

> Username: biocomp  
> Created_at: 2015-08-12 08:19:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/146#issuecomment-130213915  

I did run the tests, they're passing. I only tested msvc-12 and msvc-14 on windows though.  
  
The description of the class I'm touching says it's only for integers, so there should be no concern about floats:  
  
```  
//  positive_accumulator/negative_accumulator: Accumulator policies for  
//  extracting integers. Use positive_accumulator if number is positive.  
//  Use negative_accumulator if number is negative.  
```

---

## Comment 14

> Username: djowel  
> Created_at: 2015-08-12 08:52:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/146#issuecomment-130220987  

Yes, there shouldn't be a concern.

---
