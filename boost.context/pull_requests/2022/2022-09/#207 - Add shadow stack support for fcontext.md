# #207 Add shadow stack support for fcontext [Merged]

> Username: PeterYang12  
> Created at: 2022-09-30 06:14:23 UTC  
> Updated at: 2022-09-30 13:31:01 UTC  
> Merged at: 2022-09-30 13:31:01 UTC  
> Closed at: 2022-09-30 13:31:01 UTC  
> Url: https://github.com/boostorg/context/pull/207  

Shadow stack is part of Intel's Control-Flow Enforcement Technology.  
  
    Whenever a function is called, the return address is pushed onto both  
    the regular stack and the shadow stack. When that function returns, the  
    return addresses are popped off both stacks and compared; if they fail  
    to match, #CP raised.  
  
    Backport this commit from https://github.com/php/php-src/pull/9283  
    With this commit, we create shadow stack with syscall map_shadow_stack  
    (no.451) for each fiber context and switch the shadow stack accordingly  
    during fcontext switch.  
  
Signed-off-by: PeterYang12 <yuhan.yang@intel.com>  
Signed-off-by: chen-hu-97 <hu1.chen@intel.com>

---

## Comment 1

> Username: olk  
> Created_at: 2022-09-30 13:30:57 UTC  
> Url: https://github.com/boostorg/context/pull/207#issuecomment-1263580037  

ty

---
