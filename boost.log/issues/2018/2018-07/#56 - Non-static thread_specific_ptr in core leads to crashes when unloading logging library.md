# #56 - Non-static thread_specific_ptr in core leads to crashes when unloading logging library [Closed]

> Username: ctnewbold  
> Created at: 2018-07-03 20:11:08 UTC  
> Updated at: 2018-07-13 15:06:27 UTC  
> Closed at: 2018-07-13 15:06:27 UTC  
> Url: https://github.com/boostorg/log/issues/56  

We've got an extremely large, dynamic environment where we might decide to unload a component (a set of shared libraries). After unloading a component that included Boost.Log (i.e. Boost.Log got unloaded along with application code), we're often seeing a crash from the Boost.Thread library (which remains resident). The problem appears to be that boost::log::core::implementation contains a non-static instance of thread_specific_ptr: m_thread_data.  
  
Instances of thread_specific_ptr install cleanup functions into Boost.Thread; these cleanup functions are called when threads exit. Destroying an instance of thread_specific_ptr removes the cleanup functions so they are not called in the future.  
  
We cannot guarantee that the singleton instance of boost::log::core::implementation is destroyed before Boost.Log gets unloaded. (We do go to fairly heroic lengths to ensure that sinks, attributes, etc. that may have been installed are all removed.) If Boost.Log gets unloaded without destroying the core singleton, Boost.Thread is left holding a dangling cleanup function pointer for m_thread_data. This results in a crash the next time any thread in the process exits.  
  
Since the core is a singleton and thread_specific_ptr is ... per thread ... , there is no need for this to be a regular member variable. It could be a static member. (And in fact the related m_thread_data_cache that's used when compiler TLS is enabled is declared static.) If m_thread_data was declared static, it would be destructed at the time Boost.Log was unloaded, and there would be no dangling cleanup function in Boost.Thread.  
  
I've verified that the following simple patch has resolved all of the remaining crashes we've observed during our unloading stress test. If this is a reasonable solution, I'm happy to submit as a pull request.  
  
```  
@@ -271,7 +271,7 @@  
     attribute_set m_global_attributes;  
 #if !defined(BOOST_LOG_NO_THREADS)  
     //! Thread-specific data  
-    thread_specific_ptr< thread_data > m_thread_data;  
+    static thread_specific_ptr< thread_data > m_thread_data;  
   
 #if defined(BOOST_LOG_USE_COMPILER_TLS)  
     //! Cached pointer to the thread-specific data  
@@ -465,6 +465,11 @@  
     }  
 };  
   
+#if !defined(BOOST_LOG_NO_THREADS)  
+//! Thread-specific data  
+thread_specific_ptr< core::implementation::thread_data > core::implementation::m_thread_data;  
+#endif  
+  
 #if defined(BOOST_LOG_USE_COMPILER_TLS)  
 //! Cached pointer to the thread-specific data  
 BOOST_LOG_TLS core::implementation::thread_data* core::implementation::m_thread_data_cache = NULL;  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2018-07-04 07:09:36 UTC  
> Url: https://github.com/boostorg/log/issues/56#issuecomment-402384980  

If the core singleton is not destroyed then there is some component holding   
a reference to it in your program. Unloading the library in this condition   
is not correct.  
  
The singleton is held by the library as a static local variable, so it   
should normally get destroyed when the library is unloaded. This is not   
different from making TLS static. So I think the correct fix is to release   
every reference to the core in your program. In particular, this includes   
destroying all loggers.  
  
  
On July 3, 2018 11:11:09 PM ctnewbold <notifications@github.com> wrote:  
  
> We've got an extremely large, dynamic environment where we might decide to   
> unload a component (a set of shared libraries). After unloading a component   
> that included Boost.Log (i.e. Boost.Log got unloaded along with application   
> code), we're often seeing a crash from the Boost.Thread library (which   
> remains resident). The problem appears to be that   
> boost::log::core::implementation contains a non-static instance of   
> thread_specific_ptr: m_thread_data.  
>  
> Instances of thread_specific_ptr install cleanup functions into   
> Boost.Thread; these cleanup functions are called when threads exit.   
> Destroying an instance of thread_specific_ptr removes the cleanup functions   
> so they are not called in the future.  
>  
> We cannot guarantee that the singleton instance of   
> boost::log::core::implementation is destroyed before Boost.Log gets   
> unloaded. (We do go to fairly heroic lengths to ensure that sinks,   
> attributes, etc. that may have been installed are all removed.) If   
> Boost.Log gets unloaded without destroying the core singleton, Boost.Thread   
> is left holding a dangling cleanup function pointer for m_thread_data. This   
> results in a crash the next time any thread in the process exits.  
>  
> Since the core is a singleton and thread_specific_ptr is ... per thread ...   
> , there is no need for this to be a regular member variable. It could be a   
> static member. (And in fact the related m_thread_data_cache that's used   
> when compiler TLS is enabled is declared static.) If m_thread_data was   
> declared static, it would be destructed at the time Boost.Log was unloaded,   
> and there would be no dangling cleanup function in Boost.Thread.  
>  
> I've verified that the following simple patch has resolved all of the   
> remaining crashes we've observed during our unloading stress test. If this   
> is a reasonable solution, I'm happy to submit as a pull request.  
>  
> `@@ -271,7 +271,7 @@  
>     attribute_set m_global_attributes;  
> #if !defined(BOOST_LOG_NO_THREADS)  
>     //! Thread-specific data  
> -    thread_specific_ptr< thread_data > m_thread_data;  
> +    static thread_specific_ptr< thread_data > m_thread_data;  
>  
> #if defined(BOOST_LOG_USE_COMPILER_TLS)  
>     //! Cached pointer to the thread-specific data  
> @@ -465,6 +465,11 @@  
>     }  
> };  
>  
> +#if !defined(BOOST_LOG_NO_THREADS)  
> +//! Thread-specific data  
> +thread_specific_ptr< core::implementation::thread_data >   
> core::implementation::m_thread_data;  
> +#endif  
> +  
> #if defined(BOOST_LOG_USE_COMPILER_TLS)  
> //! Cached pointer to the thread-specific data  
> BOOST_LOG_TLS core::implementation::thread_data*   
> core::implementation::m_thread_data_cache = NULL;  
> `  
>  
> --  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly or view it on GitHub:  
> https://github.com/boostorg/log/issues/56

---

## Comment 2

> Username: ctnewbold  
> Created at: 2018-07-09 18:29:45 UTC  
> Url: https://github.com/boostorg/log/issues/56#issuecomment-403576304  

Admittedly this may be specific to our situation, but we do know that it's safe to unload the log library because we also know that there are no references to it from libraries that are _not_ going to be unloaded. There may still be loggers in existence, but they're all from code that is also being unloaded.  
  
I do agree with you in principle about having the application completely clean up. But in practice we've found it exceedingly difficult to get developers to use the logging library in a way that guarantees that loggers are not "leaked". For example, a logger might be declared as a non-static data member of a larger object that itself is "leaked".  
  
Our guiding principle for logging has been Do No Harm. Developers should be able to (within reason) sprinkle logging in their code without an unnecessary level of concern that doing so may break their code. Unfortunately the consequences of leaking loggers ends up violating this principle for us. Developers add a logger and then the maintainers of our logging system get a bug report that logging is causing crashes...  
  
I'm going to investigate a bit more to see if we can find some other local solution to this, but it would be nice for Boost.Log to be a bit more tolerant of "abuse" from application code.

---

## Comment 3

> Username: Lastique  
> Created at: 2018-07-10 14:16:49 UTC  
> Url: https://github.com/boostorg/log/issues/56#issuecomment-403838593  

I'm sorry, but Boost.Log, as any other library, relies on a certain   
contract with the user and it will not work as expected if the contract is   
broken. Leaking references to the library components definitely is not   
expected, as well as unloading libraries in incorrect order (dependent   
libraries that use loggers are supposed to be unloaded first).  
  
Unloading libraries can be difficult and is generally advised against.   
Boost.Log does not and will not make this use case easier. You may want to   
use a different library or a custom solution that is better suited for your   
needs.  
  
  
On July 9, 2018 9:29:46 PM ctnewbold <notifications@github.com> wrote:  
  
> Admittedly this may be specific to our situation, but we do know that it's   
> safe to unload the log library because we also know that there are no   
> references to it from libraries that are _not_ going to be unloaded. There   
> may still be loggers in existence, but they're all from code that is also   
> being unloaded.  
>  
> I do agree with you in principle about having the application completely   
> clean up. But in practice we've found it exceedingly difficult to get   
> developers to use the logging library in a way that guarantees that loggers   
> are not "leaked". For example, a logger might be declared as a non-static   
> data member of a larger object that itself is "leaked".  
>  
> Our guiding principle for logging has been Do No Harm. Developers should be   
> able to (within reason) sprinkle logging in their code without an   
> unnecessary level of concern that doing so may break their code.   
> Unfortunately the consequences of leaking loggers ends up violating this   
> principle for us. Developers add a logger and then the maintainers of our   
> logging system get a bug report that logging is causing crashes...  
>  
> I'm going to investigate a bit more to see if we can find some other local   
> solution to this, but it would be nice for Boost.Log to be a bit more   
> tolerant of "abuse" from application code.  
>  
> --  
> You are receiving this because you commented.  
> Reply to this email directly or view it on GitHub:  
> https://github.com/boostorg/log/issues/56#issuecomment-403576304

---

## Comment 4

> Username: ctnewbold  
> Created at: 2018-07-13 12:15:51 UTC  
> Url: https://github.com/boostorg/log/issues/56#issuecomment-404816029  

Fair enough. We'll make local changes as necessary then in order to make Boost.Log robust for us. You still might want to take a look at the thread-local data management in core::implementation, though. The thread_specific_ptr is a non-static member, but then there is also the TLS-based member that is static--the discrepancy is odd. FWIW, we do build Boost.Log with compiler TLS support for the slight performance benefit. There's also a mutex used during initialization of thread-local values that's unnecessary.

---

## Comment 5

> Username: Lastique  
> Created at: 2018-07-13 15:06:27 UTC  
> Url: https://github.com/boostorg/log/issues/56#issuecomment-404861115  

The compiler-based TLS is a cache, which is a performance optimization compared to directly accessing `thread_specific_ptr`.  
  
Note that making TLS static won't save you from leaking other resources allocated or held indirectly by the core. This includes memory, open files and Windows kernel objects.
