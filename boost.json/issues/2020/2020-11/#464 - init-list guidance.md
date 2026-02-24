# #464 - init-list guidance [Open]

> Username: vinniefalco  
> Created at: 2020-11-16 19:44:17 UTC  
> Updated at: 2022-05-24 13:27:38 UTC  
> Url: https://github.com/boostorg/json/issues/464  

The rule for construction should be  
  
"Always use `()` unless you want an `array`."

---

## Comment 1

> Username: Siddharth-coder13  
> Created at: 2020-11-26 09:57:55 UTC  
> Url: https://github.com/boostorg/json/issues/464#issuecomment-734198320  

Hey @vinniefalco, I am a new contributor. will you please explain this issue? I want to contribute to this repo.

---

## Comment 2

> Username: grisumbras  
> Created at: 2022-05-23 18:07:08 UTC  
> Url: https://github.com/boostorg/json/issues/464#issuecomment-1134981406  

Is this really true? We use init-lists in tests to create objects.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-05-23 18:20:56 UTC  
> Url: https://github.com/boostorg/json/issues/464#issuecomment-1134995572  

> Is this really true?  
  
I'm not sure but I know that init-list construction is problematic because of C++ and there is nothing we can do about it except warn users in the docs.

---

## Comment 4

> Username: grisumbras  
> Created at: 2022-05-24 07:41:17 UTC  
> Url: https://github.com/boostorg/json/issues/464#issuecomment-1135521898  

Ah, you mean that we should explicitly dissuade users from using complex init-list expressions?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-05-24 13:27:37 UTC  
> Url: https://github.com/boostorg/json/issues/464#issuecomment-1135924281  

Something like that, yes. We can probably ignore this until someone reports an actual issue.
