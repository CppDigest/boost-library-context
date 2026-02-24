# #27 - What about unique_ptr? [Closed]

> Username: filcuc  
> Created at: 2018-10-10 14:35:12 UTC  
> Updated at: 2018-11-10 18:47:39 UTC  
> Closed at: 2018-11-10 18:47:39 UTC  
> Url: https://github.com/boostorg/pfr/issues/27  



---

## Comment 1

> Username: apolukhin  
> Created at: 2018-10-11 08:50:51 UTC  
> Url: https://github.com/boostorg/pfr/issues/27#issuecomment-428874248  

Could you please provide a test case that does not work, along with compiler version and flags?

---

## Comment 2

> Username: filcuc  
> Created at: 2018-10-11 10:00:25 UTC  
> Url: https://github.com/boostorg/pfr/issues/27#issuecomment-428896497  

Yes, but basically the library reject the introspection of a struct with an unique_ptr because a static assert request every field to be copy constructable

---

## Comment 3

> Username: filcuc  
> Created at: 2018-10-11 13:40:12 UTC  
> Url: https://github.com/boostorg/pfr/issues/27#issuecomment-428959249  

```  
#include <memory>  
#include <boost/pfr.hpp>  
  
struct Message {  
  std::unique_ptr<int> data;  
};  
  
int main(int argc, char* argv[]) {  
  Message message;  
  auto& ptr = boost::pfr::get<0>(message);  
  return 0;  
}  
  
```

---

## Comment 4

> Username: filcuc  
> Created at: 2018-10-11 13:42:51 UTC  
> Updated at: 2018-10-11 13:43:00 UTC  
> Url: https://github.com/boostorg/pfr/issues/27#issuecomment-428960170  

```  
arm-poky-linux-gnueabi-g++ (GCC) 6.3.0  
Copyright (C) 2016 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
```

---

## Comment 5

> Username: filcuc  
> Created at: 2018-10-11 13:43:34 UTC  
> Url: https://github.com/boostorg/pfr/issues/27#issuecomment-428960413  

```  
Linux sdevvs4n02 3.13.0-85-generic #129-Ubuntu SMP Thu Mar 17 20:50:15 UTC 2016 x86_64 x86_64 x86_64 GNU/Linux  
```

---

## Comment 6

> Username: filcuc  
> Created at: 2018-10-12 15:42:03 UTC  
> Url: https://github.com/boostorg/pfr/issues/27#issuecomment-429369604  

@apolukhin do you need more information?

---

## Comment 7

> Username: apolukhin  
> Created at: 2018-10-16 08:23:58 UTC  
> Url: https://github.com/boostorg/pfr/issues/27#issuecomment-430146949  

This is  tough case. I can enable reflection for move-only types, but such change will disable reflection for types that have references.  
  
This will take some time, not sure that the issue could be solved in a good way.

---

## Comment 8

> Username: filcuc  
> Created at: 2018-10-16 14:48:49 UTC  
> Url: https://github.com/boostorg/pfr/issues/27#issuecomment-430267405  

@apolukhin feel free to close this issue if you think that it's not solvable

---

## Comment 9

> Username: filcuc  
> Created at: 2018-10-16 15:50:21 UTC  
> Url: https://github.com/boostorg/pfr/issues/27#issuecomment-430291255  

@apolukhin if is simple for you please share the patches for supporting move only types..they can be usefull for me

---

## Comment 10

> Username: apolukhin  
> Created at: 2018-10-16 19:34:14 UTC  
> Url: https://github.com/boostorg/pfr/issues/27#issuecomment-430370238  

Just comment out this line https://github.com/apolukhin/magic_get/blob/c5130dd9e5f52d31bf32d3e93cbc728686a5f4be/include/boost/pfr/detail/fields_count.hpp#L33 and uncomment the following one. You'll have to comment out the static_assert for is_copyable too.

---

## Comment 11

> Username: apolukhin  
> Created at: 2018-11-01 05:51:47 UTC  
> Url: https://github.com/boostorg/pfr/issues/27#issuecomment-434938347  

Looks like I've found a way to support move only types or references/copyables at the same time. It's not perfect, you can not have a reference and a unique_ptr at the same time.  
  
I'll write some tests and push the changes into develop in a few days.

---

## Comment 12

> Username: apolukhin  
> Created at: 2018-11-06 04:12:43 UTC  
> Url: https://github.com/boostorg/pfr/issues/27#issuecomment-436125343  

@filcuc I've just merged the required patches to develop. Please test, and if there's no issues (or there's no objections in 3 days) I'll merge it to master branch.

---

## Comment 13

> Username: filcuc  
> Created at: 2018-11-06 22:23:02 UTC  
> Url: https://github.com/boostorg/pfr/issues/27#issuecomment-436430865  

@apolukhin Thank you!! i'll test it tomorrow or on Thursday

---

## Comment 14

> Username: apolukhin  
> Created at: 2018-11-10 18:47:39 UTC  
> Url: https://github.com/boostorg/pfr/issues/27#issuecomment-437608992  

No complains so far :)
