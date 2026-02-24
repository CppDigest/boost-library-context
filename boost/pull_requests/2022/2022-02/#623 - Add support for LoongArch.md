# #623 Add support for LoongArch [Closed]

> Username: loongson-zn  
> Created at: 2022-02-15 10:05:14 UTC  
> Updated at: 2022-04-16 08:50:57 UTC  
> Closed at: 2022-04-16 08:50:57 UTC  
> Url: https://github.com/boostorg/boost/pull/623  

Add support for LoongArch, please review, thanks!  
Boost related sub modules have been submitted ,  and I have completed the relevant tests on PC.  
  
```  
Test result:  
1) libs/context/test:  
cc1plus: error: ‘-fsplit-stack’ is not supported by this compiler configuration （ It's can be ignored）  
2) libs/fiber/test : all pass  
3) libs/predef/test: all pass  
4) libs/config/test:  all pass  
```

---

## Comment 1

> Username: mclow  
> Created_at: 2022-02-15 15:51:39 UTC  
> Url: https://github.com/boostorg/boost/pull/623#issuecomment-1040444680  

A procedural comment: We don't merge PRs into the master branch.  We make changes in`develop`, and then, once the tests have passed, we merge from `develop` into `master`

---

## Comment 2

> Username: loongson-zn  
> Created_at: 2022-02-16 02:18:19 UTC  
> Url: https://github.com/boostorg/boost/pull/623#issuecomment-1041023571  

@mclow  I got it, Does require me to resubmit this PR to the develop branch?   
  
Others informations：  
LoongArch is a new CPU architecture. LoongArch is an Instruction Set  Architecture (ISA) that has Reduced Instruction Set Computer (RISC) style.   
ISA:  
https://loongson.github.io/LoongArch-Documentation/LoongArch-Vol1-EN.html

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2022-03-02 18:27:26 UTC  
> Url: https://github.com/boostorg/boost/pull/623#issuecomment-1057244665  

@loongson-zn : yes this needs to go into develop not master.  
  
@mclow : there is a related patch for Config which is relevant *only* if we intend to support loongarch here but which would need to be merged before this one.  What's our criteria for platforms supported here, and what's the consequence of a platform being unsupported?  I would assume not much consequence?

---

## Comment 4

> Username: loongson-zn  
> Created_at: 2022-03-03 01:55:29 UTC  
> Url: https://github.com/boostorg/boost/pull/623#issuecomment-1057581461  

Ok, I have comleted the PR resubmission in develop.  https://github.com/boostorg/boost/pull/629#issue-1157871256 @mclow @jzmaddock   
In order to support boost  on LoongArch ,  related sub modules :  
https://github.com/boostorg/config/pull/424#issue-1136899182    
https://github.com/boostorg/context/pull/197#issue-1138419444   
https://github.com/boostorg/predef/pull/122#issue-1137167650  
https://github.com/bfgroup/b2/pull/140#issue-1136960750

---

## Comment 5

> Username: mclow  
> Created_at: 2022-03-04 01:21:48 UTC  
> Url: https://github.com/boostorg/boost/pull/623#issuecomment-1058731704  

> What's our criteria for platforms supported here, and what's the consequence of a platform being unsupported? I would assume not much consequence?  
  
If someone wants to do the work of supporting a platform, we should accommodate them.  
However, I'd feel better if someone said that they would run a test bot for that platform.  
Otherwise, the support is (almost) certain to bit rot.

---

## Comment 6

> Username: loongson-zn  
> Created_at: 2022-03-19 06:37:07 UTC  
> Updated_at: 2022-03-19 06:37:58 UTC  
> Url: https://github.com/boostorg/boost/pull/623#issuecomment-1072953957  

We can provide ssh port, you can provide your email to me if needed.   
zhangna@loongson.cn

---
