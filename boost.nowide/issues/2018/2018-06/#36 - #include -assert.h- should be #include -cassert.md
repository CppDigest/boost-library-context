# #36 - #include <assert.h> should be #include <cassert> [Closed]

> Username: kivadiu  
> Created at: 2018-06-18 08:55:02 UTC  
> Updated at: 2018-06-18 09:28:10 UTC  
> Closed at: 2018-06-18 09:26:28 UTC  
> Url: https://github.com/boostorg/nowide/issues/36  

<cassert> exists already in C++03 (and 98 I guess) so we can safely use it?  
standalone/scoped_ptr.hpp:15

---

## Comment 1

> Username: kivadiu  
> Created at: 2018-06-18 09:22:33 UTC  
> Url: https://github.com/boostorg/nowide/issues/36#issuecomment-397993969  

Fixed here: https://github.com/artyom-beilis/nowide/pull/40

---

## Comment 2

> Username: artyom-beilis  
> Created at: 2018-06-18 09:26:28 UTC  
> Url: https://github.com/boostorg/nowide/issues/36#issuecomment-397995027  

Why to use `<cassert>` what problem it exactly solves? assert is macro and does not go to `std` namespace

---

## Comment 3

> Username: kivadiu  
> Created at: 2018-06-18 09:28:10 UTC  
> Url: https://github.com/boostorg/nowide/issues/36#issuecomment-397995566  

I agree that it is a macro so that std should not be involved but that looks more consistent with all other C headers?
