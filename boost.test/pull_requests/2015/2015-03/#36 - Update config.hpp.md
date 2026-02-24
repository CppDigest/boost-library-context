# #36 Update config.hpp [Closed]

> Username: akumta  
> Created at: 2015-03-03 19:42:49 UTC  
> Updated at: 2015-03-29 10:43:09 UTC  
> Closed at: 2015-03-29 10:43:09 UTC  
> Url: https://github.com/boostorg/test/pull/36  

For ticket# 6766

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2015-03-21 15:49:17 UTC  
> Url: https://github.com/boostorg/test/pull/36#issuecomment-84375862  

Hi,  
  
Would you please tell me if the branch tractickets/6766-incorrect-declaration-for-putenv-in-config.hpp corrects the issue?  
I rebased your patch onto 0f34822   
  
Thanks,  
Raffi

---

## Comment 2

> Username: akumta  
> Created_at: 2015-03-23 05:18:44 UTC  
> Url: https://github.com/boostorg/test/pull/36#issuecomment-84814930  

I'm not clear on what needs to be verified.  
I looked at  
https://github.com/boostorg/test/commit/0f95ca8896647d324f2131d9cb20d292d4ca6a80  
 and it looks OK.  
If I need to check on something else, do let me know. I will do so.  
  
Thanks.

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2015-03-23 08:28:26 UTC  
> Url: https://github.com/boostorg/test/pull/36#issuecomment-84883519  

The idea is that you checkout this branch `tractickets/6766-incorrect-declaration-for-putenv-in-config.hpp` and test it. I added some changes to your PR.

---

## Comment 4

> Username: akumta  
> Created_at: 2015-03-23 13:02:58 UTC  
> Url: https://github.com/boostorg/test/pull/36#issuecomment-84989678  

I'm not familiar with git. How do I check out this branch?  
Could you point me to some documentation on it, if possible?  
  
Thanks.

---

## Comment 5

> Username: raffienficiaud  
> Created_at: 2015-03-23 13:09:56 UTC  
> Updated_at: 2015-03-23 13:23:39 UTC  
> Url: https://github.com/boostorg/test/pull/36#issuecomment-84991251  

Let's call `$boost` your boost clone:  
  
```  
cd $boost/libs/test  
git fetch  
git checkout tractickets/6766-incorrect-declaration-for-putenv-in-config.hpp  
```

---

## Comment 6

> Username: akumta  
> Created_at: 2015-03-23 13:57:05 UTC  
> Url: https://github.com/boostorg/test/pull/36#issuecomment-85007729  

Thanks, I will clone the repository and follow the steps, test and report back here.

---

## Comment 7

> Username: raffienficiaud  
> Created_at: 2015-03-23 13:59:18 UTC  
> Url: https://github.com/boostorg/test/pull/36#issuecomment-85009054  

Ok thanks. If you clone, you have to do the following before the commands I sent above  
  
```  
git clone https://github.com/boostorg/boost.git  
git submodule init  
git submodule update --recursive  
```

---

## Comment 8

> Username: akumta  
> Created_at: 2015-03-23 14:03:55 UTC  
> Url: https://github.com/boostorg/test/pull/36#issuecomment-85011221  

Yes, will do. Thanks.

---

## Comment 9

> Username: akumta  
> Created_at: 2015-03-24 04:21:31 UTC  
> Url: https://github.com/boostorg/test/pull/36#issuecomment-85333540  

I have tested the changes. It looks good. Thanks.

---

## Comment 10

> Username: raffienficiaud  
> Created_at: 2015-03-24 08:57:06 UTC  
> Url: https://github.com/boostorg/test/pull/36#issuecomment-85409007  

Thanks, I merge the changes to develop once we have a slot.

---

## Comment 11

> Username: akumta  
> Created_at: 2015-03-24 13:12:13 UTC  
> Url: https://github.com/boostorg/test/pull/36#issuecomment-85488107  

Thank you, truly appreciate your help on this ticket(#6766). It has been around for 3 years and getting this into 1.58 would mean around 15% improvement in the pass rate for the test runs with our compiler.

---

## Comment 12

> Username: akumta  
> Created_at: 2015-03-25 14:47:23 UTC  
> Url: https://github.com/boostorg/test/pull/36#issuecomment-86062824  

Any idea when the slot would be open to merge?

---

## Comment 13

> Username: raffienficiaud  
> Created_at: 2015-03-29 10:43:09 UTC  
> Url: https://github.com/boostorg/test/pull/36#issuecomment-87389443  

Merged to develop

---
