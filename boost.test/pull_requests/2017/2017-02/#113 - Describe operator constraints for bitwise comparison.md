# #113 [WIP] Describe operator constraints for bitwise comparison [Closed]

> Username: norbertwenzel  
> Created at: 2017-02-14 20:22:45 UTC  
> Updated at: 2017-03-19 15:36:48 UTC  
> Closed at: 2017-03-19 09:48:49 UTC  
> Url: https://github.com/boostorg/test/pull/113  

I've recently been surprised that bitwise comparison only works for integer types, not for arbitrary types. I would have expected it reinterprets whatever value it gets as an array of bytes and compares these bit by bit.  
A short discussion on the [Boost Users Mailinglist](http://lists.boost.org/boost-users/2017/02/87165.php) indicated the restriction to integer types only might be intentional and I see some reasons for that. Nevertheless I'd prefer these constraints to be documented prominently.  
This PR therefore proposes a possible update to the documentation and is mainly created to start a discussion, not to be merged right away.

---

## Comment 1

> Username: norbertwenzel  
> Created_at: 2017-02-25 07:42:20 UTC  
> Url: https://github.com/boostorg/test/pull/113#issuecomment-282467578  

any comments?

---

## Comment 2

> Username: raffienficiaud  
> Created_at: 2017-03-18 13:45:18 UTC  
> Url: https://github.com/boostorg/test/pull/113#issuecomment-287547296  

Yes, I am not in favor of having that feature. It rather looks like you have a specific need for comparing arrays. If really needed, I can add documentation on the fact that only integers are supported for this type of operations.

---

## Comment 3

> Username: norbertwenzel  
> Created_at: 2017-03-18 15:17:34 UTC  
> Url: https://github.com/boostorg/test/pull/113#issuecomment-287552697  

Yes, comparing (reinterpret_casted) arrays of uint8 was the solution I ended up with.   
  
I understand your stance against that feature. Nevertheless I'd prefer the documentation to clearly state the integer requirement as any other precondition. This would have saved me some time tracking avoidable compilation errors that should not happen *after* reading the documentation.  
  
My changes to the documentation are the minimal changes I'd hope to see in the docs, though if you as a maintainer added additional justifications for the precondition, I'd welcome that.

---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2017-03-19 09:48:49 UTC  
> Url: https://github.com/boostorg/test/pull/113#issuecomment-287605312  

Except that the changes you propose are factually incorrect. Please check with a class of your own that implements only `operator<<` and you will judge by yourself.  
  
All in all, I believe most of the C++ developers know when they can use bitwise operations, and float is a type that is know for not supporting this.

---

## Comment 5

> Username: norbertwenzel  
> Created_at: 2017-03-19 15:36:45 UTC  
> Url: https://github.com/boostorg/test/pull/113#issuecomment-287624793  

You are absolutely right about my proposed text. I did the test with `operator<<` and it fails. I would have preferred to create a Github issue but since this is not possible I quickly proposed this text and did not check if it is actually correct. I apologize for that.  
  
And I'm fully aware that bitwise operations are not supported on floats. I was just surprised that my type itself has to support bitwise operations for a check if the raw bit pattern matches. That's what I would have preferred to be explicitly documented.  
  
Anyway, that discussion has taken way longer than the time it took me to find out why the test failed in the first place. Since I'm obviously the only one here that was surprised by this requirement it probably is a non-issue.

---
