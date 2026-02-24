# #128 - Unhandled exceptions while debugging with MS Visual Studio [Open]

> Username: pps83  
> Created at: 2018-02-04 17:50:30 UTC  
> Updated at: 2021-03-18 09:28:56 UTC  
> Url: https://github.com/boostorg/test/issues/128  

I'm using some ancient version of boost.test (perhaps 1.58), however, by looking at the latest code I think latest version has the same issue. Boost.test captures c++ exceptions as well as SEH to be able to handle all kinds of errors that normally result in crashes. The problem with that, however, it makes debugging tests difficult. I have some test that somehow triggers an unhanded C++ in my library. Normally, unhandled exception gets handled by the visual studio debugger and it stops basically on the line where exception is thrown and it's clear and obvious where it came from. With boost.test: forget about it, no way to see anything.  
  
So, how can I make boost.test not try to capture SEH or c++ exceptions while debugger is attached? For now I added code to wrap `test_method` calls in BOOST_FIXTURE_TEST_CASE to capture SEH myself and write a dmp (core dump) for later debugging, but I would definitely prefer that boost.test wouldn't try to capture unhandled c++ or SEH exceptions when `debug_present`. What can be done to have this behavior, does boost.test have it?

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-02-04 18:38:27 UTC  
> Url: https://github.com/boostorg/test/issues/128#issuecomment-362929030  

Not sure if this will answer your question, but from the top of my head, I can think of the following options:  
  
* in Visual studio, you can say that you want to break on exceptions, and you have the option to choose if you want to break at the time the exception is raised, if or if not the exception is handled. See this for example: https://blogs.msdn.microsoft.com/devops/2015/02/23/the-new-exception-settings-window-in-visual-studio-2015/  
* have you tried the option [``catch_system_errors``](http://www.boost.org/doc/libs/1_66_0/libs/test/doc/html/boost_test/utf_reference/rt_param_reference/catch_system.html) ?   
* maybe you can try any of the debugging facilities? see [here](http://www.boost.org/doc/libs/1_66_0/libs/test/doc/html/boost_test/testing_tools/debugging.html) for more details.  
  
Keep us posted!

---

## Comment 2

> Username: pps83  
> Created at: 2018-02-04 20:41:27 UTC  
> Url: https://github.com/boostorg/test/issues/128#issuecomment-362937836  

I know I can break on exceptions, but in my case I cannot break when exception is thrown, since the code that I'm working with throws them very heavily, but some places end up without handling these exceptions. So, only default handling is acceptable: break on unhandled exceptions.  
  
I didn't try [catch_system_errors](http://www.boost.org/doc/libs/1_66_0/libs/test/doc/html/boost_test/utf_reference/rt_param_reference/catch_system.html), seems like a very useful option. There should be matching catch_unhandled_exceptions, and both of them should be off when debugger attached :) It would be also helpful if all that code went through some single freestanding function that handles all that capturing of uncaught exceptions or system errors, otherwise it's not even clear where I'd need to modify boost.test not to capture unhandled c++ exceptions when debug_present()

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2019-11-12 08:09:47 UTC  
> Url: https://github.com/boostorg/test/issues/128#issuecomment-552781487  

I believe I can close this issue now. If you have further questions, please do not hesitate to open a new ticket.

---

## Comment 4

> Username: pps83  
> Created at: 2019-11-21 08:21:48 UTC  
> Url: https://github.com/boostorg/test/issues/128#issuecomment-556972729  

I don't think that it should be closed. Proper solution is to fix boost-test: it shouldn't capture unhandled or seh exceptions when debugger is present

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2019-11-21 09:26:30 UTC  
> Url: https://github.com/boostorg/test/issues/128#issuecomment-556996071  

Have you tried to disable the handling of exceptions with the `catch_system_errors` option?   
  
From the code I see this is also the default behaviour of Boost.Test: if the framework detects a debugger, it automatically sets the option to `false`.   
  
1. If you see a different behaviour in VS when you are explicitly disabling the `catch_system_error`, then there might be a bug in the way the framework detects for the debugger (in that case, please let me know the version of VS you are using)  
1. If you do not see any difference, there might be a bug in the way the option is handled in the framework.  
  
It would be very helpful if you provide me with this information.  
  
In the meantime, as a workaround and if this works for you with the startup options from Visual Studio (option 1/), this can also be easily made as a default option in the Visual Studio environment if you are using eg. cmake for your project (see for instance [VS_DEBUGGER_COMMAND_ARGUMENTS](https://cmake.org/cmake/help/git-stage/prop_tgt/VS_DEBUGGER_COMMAND_ARGUMENTS.html) ).  
  
I am reopening the ticket.

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2020-04-08 17:32:43 UTC  
> Url: https://github.com/boostorg/test/issues/128#issuecomment-611091194  

Boost.Test does not capture SEH exceptions when it detects that a debugger is attached, even if you provide the `catch_system_errors`. An only issue may be when you attach the debugger on a running program. Even then, the check "being under debugger" is performed prior to each test case start.  
  
So I believe the behavior should work for you as well. I tested myself and it seems to do the job.  
  
So the only issue I could see (apart from me unable to reproduce) is that the debugger is not detected properly in boost.test, or some particular exception are caught. The check about the debugger on Win is done by calling the `kernel32` function `IsDebuggerPresent`, so it should be fairly ok, except maybe if the test module is not compiled with VS tools.  
  
A way to make progress on this would be with your help:  
  
* compile a test module with the header only variant  
* place a breakpoint in `debug.ipp` in the function `under_debugger` and inspect the result of that function (here: https://github.com/boostorg/test/blob/develop/include/boost/test/impl/debug.ipp#L641 )  
* and please report back!  
  
Thanks

---

## Comment 7

> Username: raffienficiaud  
> Created at: 2020-05-07 22:59:47 UTC  
> Url: https://github.com/boostorg/test/issues/128#issuecomment-625538937  

@pps83 any update?

---

## Comment 8

> Username: mhier  
> Created at: 2021-03-18 09:28:55 UTC  
> Url: https://github.com/boostorg/test/issues/128#issuecomment-801769487  

I have the same issue when debugging with QtCreator on Linux. I have debugged into `under_debugger()`, which correctly detects the debugger and returns true. Yet it catches std exceptions via `CATCH_AND_REPORT_STD_EXCEPTION( std::logic_error )` in `execution_monitor.ipp`. I found out that #defining `BOOST_NO_EXCEPTIONS` (e.g. via command line option of the compiler) I can tell BOOST do no longer catch those exceptions. Maybe this helps @pps83?
