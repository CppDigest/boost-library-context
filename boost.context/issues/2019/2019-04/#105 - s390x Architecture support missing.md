# #105 - s390x Architecture support missing [Closed]

> Username: Naveenaidu  
> Created at: 2019-04-13 17:37:36 UTC  
> Updated at: 2019-12-26 20:46:16 UTC  
> Closed at: 2019-12-26 20:46:16 UTC  
> Url: https://github.com/boostorg/context/issues/105  

Boost.Context does not currently support s390x architectures. This in turn blocks the coroutine module from being available.

---

## Comment 1

> Username: Naveenaidu  
> Created at: 2019-04-13 17:38:13 UTC  
> Url: https://github.com/boostorg/context/issues/105#issuecomment-482848454  

I am currently working on this issue as a part of `OpenMainframe Project Internship`. Will be sending a PR soon :)

---

## Comment 2

> Username: nealef  
> Created at: 2019-04-23 13:32:00 UTC  
> Url: https://github.com/boostorg/context/issues/105#issuecomment-485804036  

Will your fcontext support the vector registers? Also, what did you do to enable context to know about s390x? Adding entries to Jamfile.v2 and architecture.jam are part of the process but what else is needed?

---

## Comment 3

> Username: Naveenaidu  
> Created at: 2019-04-23 16:56:45 UTC  
> Updated at: 2019-04-23 18:28:46 UTC  
> Url: https://github.com/boostorg/context/issues/105#issuecomment-485887662  

@nealef I'm sorry - I won't be able to do a complete justice to your questions. My internship hasn't yet started and I am still reading the documentation to get started. I haven't yet started the implementation part :sweat_smile:   Though I will be able to give a brief idea about what needs to be done.  
  
1. `Boost's Build system` doesn't currently support the `s390x` architecture - So the first change would be to update the Build system to detect the appropriate assembly file ( There's an open issue for this at [Boost.build](https://github.com/boostorg/build/issues/411 )). Along with it as you said, we would also have to update the Jamfile.v2 and architecture.jam  
  
2.  I would have to implement three assembly files for s390x arch:  
```  
- jump_s39x_sysv_elf_gas.S: context switch between two fibers/fiber_contexts  
- make_s39x_sysv_elf_gas.S: prepares stack for the first invocation  
- ontop_s39x_sysv_elf_gas.S: execute function on top of a fiber/fiber_context  
```  
I'll be using the ELF binary format and sysv as the ABI for the assembly files.

---

## Comment 4

> Username: olk  
> Created at: 2019-12-26 20:46:16 UTC  
> Url: https://github.com/boostorg/context/issues/105#issuecomment-569130990  

Now supported
