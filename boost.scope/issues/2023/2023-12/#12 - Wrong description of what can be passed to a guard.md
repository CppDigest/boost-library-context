# #12 - Wrong description of what can be passed to a guard [Closed]

> Username: akrzemi1  
> Created at: 2023-12-02 16:23:36 UTC  
> Updated at: 2023-12-02 16:51:37 UTC  
> Closed at: 2023-12-02 16:51:26 UTC  
> Url: https://github.com/boostorg/scope/issues/12  

https://github.com/Lastique/scope/blob/2cd37e97c91fb1e76bdb8d33464cf89ea42923be/doc/scope_guards.qbk#L17 says:  
  
> The wrapped action function object is specified on the scope guard construction and cannot be changed afterwards. It must be one of:  
>   
> * a user-defined class with a public `operator()` taking no arguments, or  
> * an lvalue reference to such class, or  
> * an lvalue reference or pointer to a function taking no arguments.  
  
There is something wrong with this description. Are you describing *types* that are allowed or *objects* that are allowed?

---

## Comment 1

> Username: Lastique  
> Created at: 2023-12-02 16:39:14 UTC  
> Url: https://github.com/boostorg/scope/issues/12#issuecomment-1837198268  

The requirements are placed on the type of the action function object. I.e. "It must be one of *the following types*:"

---

## Comment 2

> Username: akrzemi1  
> Created at: 2023-12-02 16:41:43 UTC  
> Url: https://github.com/boostorg/scope/issues/12#issuecomment-1837198827  

Then maybe change "It must be one of:" to "Its type must be one of:".

---

## Comment 3

> Username: Lastique  
> Created at: 2023-12-02 16:51:36 UTC  
> Url: https://github.com/boostorg/scope/issues/12#issuecomment-1837200886  

Fixed, thanks.
