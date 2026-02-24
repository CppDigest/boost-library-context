# #525 - value('a').is_int64() [Closed]

> Username: doganulus  
> Created at: 2021-03-09 10:53:18 UTC  
> Updated at: 2021-06-23 05:25:37 UTC  
> Closed at: 2021-06-23 05:25:37 UTC  
> Url: https://github.com/boostorg/json/issues/525  

I would expect `value('a').kind()` to be a `string` but it is `int64`. Bug or feature?

---

## Comment 1

> Username: grisumbras  
> Created at: 2021-03-09 11:03:14 UTC  
> Url: https://github.com/boostorg/json/issues/525#issuecomment-793721143  

That's an interesting question. @vinniefalco ?

---

## Comment 2

> Username: grisumbras  
> Created at: 2021-03-09 11:04:13 UTC  
> Updated at: 2021-03-09 11:04:23 UTC  
> Url: https://github.com/boostorg/json/issues/525#issuecomment-793722213  

> Vinnie: value( "a" ) is a string. value( 'a' ) is not. Working as intended  
  
So, not a bug.

---

## Comment 3

> Username: WPMGPRoSToTeMa  
> Created at: 2021-03-11 00:15:54 UTC  
> Url: https://github.com/boostorg/json/issues/525#issuecomment-796312491  

Probably `value` with a char-type argument should be prohibited (`delete`d) to avoid any surprising behavior to users?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-03-11 00:35:24 UTC  
> Url: https://github.com/boostorg/json/issues/525#issuecomment-796324171  

> Probably value with a char-type argument should be prohibited (deleted) to avoid any surprising behavior to users?  
  
I'm not opposed to it although I am not an expert. Maybe try submitting a pull request and see what CI thinks about it?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-03-11 02:16:27 UTC  
> Url: https://github.com/boostorg/json/issues/525#issuecomment-796378459  

Hmm... come to think of it, deleting that constructor would break conversion from `int8`, which is allowed to be an alias for `char`.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2021-03-11 02:16:47 UTC  
> Url: https://github.com/boostorg/json/issues/525#issuecomment-796378655  

Unfortunately it looks like this is a corner case of C++ where there is no perfect solution.

---

## Comment 7

> Username: WPMGPRoSToTeMa  
> Created at: 2021-03-11 22:54:48 UTC  
> Updated at: 2021-03-11 22:54:56 UTC  
> Url: https://github.com/boostorg/json/issues/525#issuecomment-797104153  

>   
>   
> Hmm... come to think of it, deleting that constructor would break conversion from `int8`, which is allowed to be an alias for `char`.  
  
Yep, it is true on AArch64 (https://reviews.llvm.org/D85225)...

---

## Comment 8

> Username: vinniefalco  
> Created at: 2021-06-23 05:25:37 UTC  
> Url: https://github.com/boostorg/json/issues/525#issuecomment-866532651  

Working as designed, this is the best we can do with the current specification of C++.
