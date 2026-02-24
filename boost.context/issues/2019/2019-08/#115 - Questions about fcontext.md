# #115 - Questions about fcontext [Closed]

> Username: rockeet  
> Created at: 2019-08-10 06:44:35 UTC  
> Updated at: 2019-08-10 15:26:33 UTC  
> Closed at: 2019-08-10 15:26:33 UTC  
> Url: https://github.com/boostorg/context/issues/115  

## Question 1  
in fiber fcontext implementation, new fiber jump to fcontext then jump back immediately, I am confusing about such implementation, could you please make an explaination?  
  
```c++  
// in create_fiber1/create_fiber2:  
    // transfer control structure to context-stack  
    return jump_fcontext( fctx, record).fctx;  
  
// in fiber_entry:  
        // jump back to `create_context()`  
        t = jump_fcontext( t.fctx, nullptr);  
        // start executing  
        t.fctx = rec->run( t.fctx);  
```  
  
If I write my fiber by `make_fcontext/jump_fcontext`, can I omit the jump to and back?  
  
## Question 2  
  
 Why fiber proc takes a 2-pointer sized struct as parameter rather than 2 pointer type parameter?  
  
```c++  
struct transfer_t {  
    fcontext_t  fctx; // typedef void* fcontext_t;  
    void    *   data;  
};  
fcontext_t BOOST_CONTEXT_CALLDECL  
make_fcontext( void * sp, std::size_t size,  
               void (* fn)( transfer_t) );  
```  
  
Why do not use:  
```c++  
fcontext_t BOOST_CONTEXT_CALLDECL  
make_fcontext( void * sp, std::size_t size,  
               void (* fn)( void* fcontext, void* data) );  
```

---

## Comment 1

> Username: olk  
> Created at: 2019-08-10 15:26:33 UTC  
> Url: https://github.com/boostorg/context/issues/115#issuecomment-520157332  

1.   the assembler exchanges stack and instruction pointer - > take look at the files  
  
2. it is easier in assembler to pay two parameters instead of a strict (varies betwen architectures)
