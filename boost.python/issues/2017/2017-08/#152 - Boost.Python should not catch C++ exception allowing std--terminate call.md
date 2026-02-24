# #152 - Boost.Python should not catch C++ exception allowing std::terminate call [Open]

> Username: PavelStishenko  
> Created at: 2017-08-25 06:36:46 UTC  
> Updated at: 2017-08-27 16:24:20 UTC  
> Url: https://github.com/boostorg/python/issues/152  

All calls of C/C++ functions and methods are wrapped in Boost.Python internals by try-catch constructions. The specific code is in  boost::python::handle_exception_impl() and in make_holder::apply::execute. There are good reasons to do it and they are described in the Reference. But due to handling of exceptions in Boost.Python internals core dumps are never created. Core dumps are generated only when unhandled exceptions are being thrown.  handle_exception_impl() handles all stdlib exception and even all unknown exceptions (with catch(...) block ). Although it is possible to catch exceptions from C/C++ code by registering custom exception translators, it is impossible to get core dump for post-mortem analysis. The fundamental problem here is that at the moment when any exception handler is invoked (in exception translator or in custom catch(){} block ) the stack of calls is already unwinded and information about point of exception throwing is already lost. Even if the exception is just rethrown by the handler, and is not handled further, the call stack into the core dump will show the handler as a point of exception throwing, without information about original problem source.  
  
I think that try-catch blocks in handle_exception_impl() and make_holder::apply::execute() should be removed. Alternatively, probably an option could be added to the def macros (the one nested in BOOST_PYTHON_MODULE() macros) that would control the strategy of exception handling. Anyway, current solution significantly complicates debugging of C/C++ modules and IMHO it should be fixed.  
  
The similar problem was considered here: [ Impossible to find/debug unhandled exceptions in an std::thread](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=55917)   
Finally they decided to "Remove try-block so that exceptions propagate out of the thread and terminate".

---

## Comment 1

> Username: garyfurnish  
> Created at: 2017-08-27 15:01:08 UTC  
> Url: https://github.com/boostorg/python/issues/152#issuecomment-325203730  

I can't help but feel that catching arbitrary exceptions could allow for the program to get into an undefined state unless there are very strong exception guarantees on boost_python, so I think you have the right idea (does boost_python handle bad allocs in arbitrary places correctly? Probably not).  Having said that I don't know if the default behavior would be changeable, wouldn't that be a potentially API breaking change?  This almost sounds like something that might need an ifdef as much as I hate those.

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2017-08-27 16:10:56 UTC  
> Url: https://github.com/boostorg/python/issues/152#issuecomment-325207883  

> (does boost_python handle bad allocs in arbitrary places correctly?  
> Probably not).  
>  
  
I don't think it is appropriate to say that boost_python handles  
exceptions. The only "handling" that occurs is the translation into  
Python. As it is impossible to handle arbitrary C++ exceptions from  
within Python code, the only possible solution is to require the wrapped  
C++ code to be exception-safe.  
  
Anything that leads to corrupt program state shouldn't result in a  
(catchable) exception, no matter where the catching happens.

---

## Comment 3

> Username: garyfurnish  
> Created at: 2017-08-27 16:24:20 UTC  
> Url: https://github.com/boostorg/python/issues/152#issuecomment-325208698  

The program state may be non-corrupt but inconsistent.  If there is not a mandate to deliver strong exception safety then catching everything and assuming the program can keep running as expected is going to lead to stability issues.  Consider an exception that occurs while holding the GIL that doesn't get handled. The proper behavior is to either always propagate unless safety can be guaranteed or put it behind a switch.  (Or to guarantee strong exception safety for certain classes of exceptions, and then only catch those exceptions, but this would take a lot of work to guarantee).
