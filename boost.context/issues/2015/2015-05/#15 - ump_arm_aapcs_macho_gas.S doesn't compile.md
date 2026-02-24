# #15 - ump_arm_aapcs_macho_gas.S doesn't compile [Closed]

> Username: gdochev  
> Created at: 2015-05-02 13:25:34 UTC  
> Updated at: 2015-05-02 13:46:17 UTC  
> Closed at: 2015-05-02 13:37:49 UTC  
> Url: https://github.com/boostorg/context/issues/15  

An attempt to compile using clang produces:  
  
libs/context/src/asm/jump_arm_aapcs_macho_gas.S:94:11: error: invalid operand for instruction  
    pop v1  
          ^  
  
The fix is straightforward:   
  
```  
put {v1}  
```

---

## Comment 1

> Username: olk  
> Created at: 2015-05-02 13:37:48 UTC  
> Url: https://github.com/boostorg/context/issues/15#issuecomment-98359092  

don't know which version you are using - branch develop already used curly brackets for pop/push

---

## Comment 2

> Username: gdochev  
> Created at: 2015-05-02 13:46:17 UTC  
> Url: https://github.com/boostorg/context/issues/15#issuecomment-98360641  

Sorry for the noise, it's already fixed in 1.58.
