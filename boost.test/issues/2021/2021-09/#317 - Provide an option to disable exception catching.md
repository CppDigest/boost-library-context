# #317 - Provide an option to disable exception catching [Open]

> Username: TheQuantumPhysicist  
> Created at: 2021-09-14 07:39:57 UTC  
> Updated at: 2022-10-10 20:51:07 UTC  
> Url: https://github.com/boostorg/test/issues/317  

Some times it's useful to just let unexpected exceptions do their work in tests. Especially when debugging. For example, gtest [provides the option `--gtest_catch_exceptions=0` or the environment variable `GTEST_CATCH_EXCEPTIONS`](https://github.com/google/googletest/blob/master/docs/advanced.md), where the test framework doesn't catch any exceptions if any of these options is set to 0.  
  
Granted, catching unexpected exceptions by default is helpful because otherwise the tests will abort. But then during debugging (at the dev's will, for example, using a command line argument like the one above), it's much easier to catch unintended bug by just letting the tests/program fire and abort the program. A debugger will immediately show the correct stack trace and the source of the problem.  
  
I think adding such a command line argument (and maybe also environment variable) is very useful and saves lots of time when debugging.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2022-03-02 09:47:28 UTC  
> Url: https://github.com/boostorg/test/issues/317#issuecomment-1056706526  

Isn't the command line argument [`--catch_system_errors`](https://www.boost.org/doc/libs/1_78_0/libs/test/doc/html/boost_test/utf_reference/rt_param_reference/catch_system.html) doing this already? Or maybe it is still catching things?

---

## Comment 2

> Username: TheQuantumPhysicist  
> Created at: 2022-03-02 10:00:10 UTC  
> Updated at: 2022-03-02 10:00:24 UTC  
> Url: https://github.com/boostorg/test/issues/317#issuecomment-1056731062  

Well, given that the default value of [`--catch_system_errors`](https://www.boost.org/doc/libs/1_78_0/libs/test/doc/html/boost_test/utf_reference/rt_param_reference/catch_system.html) is `no`, then it's obviously not working.

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2022-03-02 14:10:34 UTC  
> Url: https://github.com/boostorg/test/issues/317#issuecomment-1056972370  

The documentation seems to be wrong ! I can see the following code  
  
```  
#ifdef BOOST_TEST_DEFAULTS_TO_CORE_DUMP  
            false,  
#else  
            true,  
#endif  
```  
  
which seems to be doing the exact opposite of the documentation.  
Would it be possible for you to explicitly indicate `--catch-system-error=no` and let me know if this works for you?

---

## Comment 4

> Username: TheQuantumPhysicist  
> Created at: 2022-03-03 06:42:08 UTC  
> Url: https://github.com/boostorg/test/issues/317#issuecomment-1057726776  

I'm sorry, but I'm in a very bad health condition that I'm almost incapacitated I can't do anything right now. If someone else can do this, that'd be great.

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2022-03-03 06:49:03 UTC  
> Url: https://github.com/boostorg/test/issues/317#issuecomment-1057730445  

I am sorry to hear that, take care and get well soon

---

## Comment 6

> Username: krwalker  
> Created at: 2022-10-10 20:51:06 UTC  
> Url: https://github.com/boostorg/test/issues/317#issuecomment-1273808421  

I can confirm the framework does not catch system exceptions with `--catch_system_errors=no`.  
  
I also believe the documentation and implementation have the values swapped.  
  
Locally we have `#define`d `BOOST_TEST_DEFAULTS_TO_CORE_DUMP` to get the expected default.
