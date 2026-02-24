# #281 Export execution_aborted exception to allow catching it from outside the DLL [Closed]

> Username: pkl97  
> Created at: 2020-08-25 08:36:17 UTC  
> Updated at: 2022-03-04 10:03:40 UTC  
> Closed at: 2022-03-04 10:03:23 UTC  
> Url: https://github.com/boostorg/test/pull/281  

In our code we have encapsulated testcase execution into a dedicated method where we do some housekeeping.  
  
The code looks like this:  
  
```  
    try  
    {  
        // execute the testcase  
    }  
    catch (const boost::execution_aborted&) {  
        throw; // we let this exception propagate to ensure Boost.Test works as designed  
    }  
    catch (...) { // one of our exceptions  
        // we do some housekeeping and then call BOOST_FAIL()  
    }  
  
```  
  
However to let boost::execution_aborted exceptions propagate it is necessary for the type to be exported from the DLL. Otherwise the exception simply cannot be caught in the main program and always ends up in the catch-all handler.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2022-03-04 10:03:23 UTC  
> Url: https://github.com/boostorg/test/pull/281#issuecomment-1059017653  

Merged through #330 with rev 8b54099e15d63e8fe4127c75c9643d5fd4a956d0

---

## Comment 2

> Username: raffienficiaud  
> Created_at: 2022-03-04 10:03:39 UTC  
> Url: https://github.com/boostorg/test/pull/281#issuecomment-1059017845  

Thanks for the patch!

---
