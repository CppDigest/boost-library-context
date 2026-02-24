# #24 - Cannot construct optional<T&> a different but compatible type of optional<U&> [Closed]

> Username: tghosgor  
> Created at: 2016-09-27 06:50:43 UTC  
> Updated at: 2016-09-27 11:08:41 UTC  
> Closed at: 2016-09-27 07:19:27 UTC  
> Url: https://github.com/boostorg/optional/issues/24  

Because it tries to access private ptr_ member of optional<U&> template from optional<T&> constructor.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2016-09-27 07:17:44 UTC  
> Url: https://github.com/boostorg/optional/issues/24#issuecomment-249785415  

Thank you for the submission. If you can turn the commit into a pull request, I will be able to merge it.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2016-09-27 07:19:59 UTC  
> Url: https://github.com/boostorg/optional/issues/24#issuecomment-249785800  

Oh, you did submit a pull request. Sorry. Already merged. Thanks!

---

## Comment 3

> Username: tghosgor  
> Created at: 2016-09-27 11:08:41 UTC  
> Url: https://github.com/boostorg/optional/issues/24#issuecomment-249834943  

Thank you for this useful library.
