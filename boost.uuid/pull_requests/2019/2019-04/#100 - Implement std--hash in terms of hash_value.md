# #100 Implement std::hash in terms of hash_value. [Merged]

> Username: Lastique  
> Created at: 2019-04-15 22:07:54 UTC  
> Updated at: 2019-04-19 10:45:26 UTC  
> Merged at: 2019-04-19 10:45:25 UTC  
> Closed at: 2019-04-19 10:45:25 UTC  
> Url: https://github.com/boostorg/uuid/pull/100  

Not only it makes the two implementations equivalent, it also fixes the bug  
of to_string throwing an exception in the noexcept function. It is also  
more efficient as there is no need for the temporary string construction.  
  
Alternative to https://github.com/boostorg/uuid/pull/99.

---

## Comment 1

> Username: jeking3  
> Created_at: 2019-04-16 14:30:03 UTC  
> Url: https://github.com/boostorg/uuid/pull/100#issuecomment-483686094  

I'm working on fixing the libc++ failing build through boost-ci.  bootstrap cannot find clang.

---

## Comment 2

> Username: jeking3  
> Created_at: 2019-04-17 12:46:25 UTC  
> Url: https://github.com/boostorg/uuid/pull/100#issuecomment-484068366  

There are other boost-ci issues that need to be resolved, I'm working on that as well.

---

## Comment 3

> Username: jeking3  
> Created_at: 2019-04-19 03:36:59 UTC  
> Url: https://github.com/boostorg/uuid/pull/100#issuecomment-484757492  

The MSVC 2010-2013 error is caused by a bad appveyor system image.  Switching to their 2015 image will allow it to work, as shown in the boostorg/rational repoisitory.

---
