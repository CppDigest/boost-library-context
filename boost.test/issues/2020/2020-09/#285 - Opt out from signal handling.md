# #285 - Opt out from signal handling [Closed]

> Username: ytimenkov  
> Created at: 2020-09-23 09:54:44 UTC  
> Updated at: 2020-10-01 12:45:23 UTC  
> Closed at: 2020-10-01 12:45:23 UTC  
> Url: https://github.com/boostorg/test/issues/285  

The fact that boost intercepts system signals (and worse, tries to continue execution) makes it really hard to develop robust tests.  
  
I'm mostly concerned about `SIGABRT`... We use in multiple cases and most of the time the intent is (one or both):  
1. Stop execution  
2. Create a core dump for further analysis  
  
Few cases:  
1. `assert()` macro when something unordinary happened and we want to capture the state.  
2. External driver detected something wrong (e.g. timeout) and wants the test to stop with capturing the state.  
3. Program can't continue and calls `std::terminate` This may (and quite often) in the case when further execution is not desirable as global state can be broken. One example is when mocking framework reports failure from the destructor. Exception can't be used either and we want it to fail fast.  
  
`SIGSEGV` is another annoying one which happens all the time. And it's fine if it was some nullptr dereference, but can be also the result of totally broken state. IIRC valgrind sends it when detects memory issue (or at least it happens much more often).  
  
Interference with signal handling makes investigation (especially when happening on CI) of the root cause close to impossible and quite often result in the explosion of follow-up failures and totally broken program state.  
  
I'm not arguing that signal interception can be good in some cases, but there should be at least some way to opt out from it.  
At least I couldn't find any...

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2020-09-23 19:02:57 UTC  
> Url: https://github.com/boostorg/test/issues/285#issuecomment-697899112  

Hi,  
  
In Boost.Test, some signals are considered as "ok to continue" while some others are not. The rationale is that every test execution has a cost (compilation, run, log extraction), and we want to extract as much info out of run as possible. For that, we do not want to stop the execution of the tests set at first recoverable issue, but we do want to stop at first non-recoverable issue while maintaining some basic services before shut down (logging, especially to file for instance).  
  
For the example of `assert`: imagine you have many test cases, one of those `assert`s. This is a programmatic signal given by a developer to signal another developer that something went wrong with the program run path. In that case, we want to report this error and continue on the next test case. If functions do as they wish, for example call `exit(0)`, then the test framework will not be able to report the failure, nor any test driver in front of it (such as `ctest`). This would make the tool unreliable for not detecting false negatives.   
  
For the case of `Program can't continue and calls "std::terminate"`, we precisely want to know why we ended up in this "program cannot continue" state. This is done first through test cases that are being able to reproduce this buggy state and then debugging time, but the test framework should be able to know that such a call is happening such that it can report an error if this is the case (and then make sure that such a code is not shipped to the client).  
  
> Interference with signal handling makes investigation (especially when happening on CI)  
  
I would rather say that on CI, you cannot really get a dump easily and inspect the state. So anything that reports with good precision what happened (parsing the logs or resulting junit files) usually helps the developers to get a better idea of what is going wrong.  
  
Signal handling is platform dependent. Normally, you should be able to remove the signal handling by passing this option on the command line: https://www.boost.org/doc/libs/1_74_0/libs/test/doc/html/boost_test/utf_reference/rt_param_reference/catch_system.html . So maybe this would solve your problem at hand.

---

## Comment 2

> Username: ytimenkov  
> Created at: 2020-10-01 12:45:22 UTC  
> Url: https://github.com/boostorg/test/issues/285#issuecomment-702109244  

Ah, thanks! It seems what I was looking for (although initialization is a bit obscure).  
  
There are 2 things: The defaults in the code and in the docs are opposite: docs say "default is no" while in the code it's `true`.  
  
Another one,  
I totally agree with your statement:  
  
> The rationale is that every test execution has a cost (compilation, run, log extraction), and we want to extract as much info out of run as possible.  
  
However I would apply this only to the checks performed by the test itself (i.e. `BOOST_TEST` macros and co). `std::terminate` means terminate. One can't assume any reasonable state of the program and continue execution or try to do anything. As in my example with exception thrown from the destructor: the program is corrupted, no invariant holds and there is no programmatic way to recover from it.  
  
> This is a programmatic signal given by a developer to signal another developer that something went wrong with the program run path.  
  
Yes, **to developer**. Not to the testing framework (otherwise I would use `BOOST_TEST` macros again).  
  
>  In that case, we want to report this error and continue on the next test case.   
  
Why would you? This just destroys the message to the developer. The core dump is the message.  
  
> If functions do as they wish, for example call exit(0), then the test framework will not be able to report the failure, nor any test driver in front of it (such as ctest).  
  
That's why I'd like to get SIGABRT so it won't be an `exit(0)`. It will be `-6` perfectly caught by any runner.  
  
> I would rather say that on CI, you cannot really get a dump easily and inspect the state.  
  
Why do you think so? In my practice this is the most reliable way to read this **message to the developer** (the call stack, memory, loaded modules, local variables, global variables). How could testing framework know which information is needed?
