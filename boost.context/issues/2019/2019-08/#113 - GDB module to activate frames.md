# #113 - GDB module to activate frames [Open]

> Username: vinipsmaker  
> Created at: 2019-08-06 09:41:22 UTC  
> Updated at: 2020-04-24 16:40:37 UTC  
> Url: https://github.com/boostorg/context/issues/113  

Folly's fibers (based on Boost.Context) have a GDB script to debug fibers. The script only has a few helpers to list fibers and inspect a fiber.  
  
Once we find a valid pointer to a `boost::context::fiber` in the debugging session, it'd be useful to have two simple GDB commands to inspect its call stack. The lines related to this feature in the Facebook script are: https://github.com/facebook/folly/blob/85465c8ef64be895bd15a0a315f3698fb5dc685f/folly/fibers/scripts/gdb.py#L191-L247 ([supporting documentation](https://sourceware.org/gdb/current/onlinedocs/gdb/Unwinding-Frames-in-Python.html#Unwinding-Frames-in-Python))

---

## Comment 1

> Username: olk  
> Created at: 2020-04-24 09:01:55 UTC  
> Url: https://github.com/boostorg/context/issues/113#issuecomment-618894075  

I'm not familiar with GDB scipts...  
you are welcome to provide a patch that supports GDB scripts

---

## Comment 2

> Username: vinipsmaker  
> Created at: 2020-04-24 16:30:23 UTC  
> Url: https://github.com/boostorg/context/issues/113#issuecomment-619116081  

> [patches welcome]  
  
Good. Would x86-only support be okay for the scripts?

---

## Comment 3

> Username: lenerd  
> Created at: 2020-04-24 16:40:37 UTC  
> Url: https://github.com/boostorg/context/issues/113#issuecomment-619121545  

Hi,  
  
for debugging of some code using Boost.Fiber, I wrote some custom GDB commands for e.g. finding the scheduler object, listing the running worker fibers and their backtraces, as well as switching the context to a fiber.  
  
The code is available at [here](https://github.com/lenerd/fiber-gdb), but so far it has not been documented nor tested much (besides my own debugging sessions). Feel free to take a look. So far it requires Boost compiled in debug mode and is specific to x86-64.
