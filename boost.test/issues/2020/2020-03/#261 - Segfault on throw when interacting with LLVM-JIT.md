# #261 - Segfault on throw when interacting with LLVM-JIT [Open]

> Username: vaivaswatha  
> Created at: 2020-03-18 16:25:26 UTC  
> Updated at: 2020-04-04 04:54:05 UTC  
> Url: https://github.com/boostorg/test/issues/261  

I'm working on a project that uses LLVM to JIT compile code and execute it. The project uses `boost::unit_test` for its testsuite.  
  
I came across a weird behaviour:  
  1. Run a unit test that invokes the LLVM based JIT compiler code to compile and execute a piece of code  
  2. Run any other unit test (that doesn't interact with the LLVM parts) that `throw`s.  
  3. The throw results in a segfault (irrespective of whether it's handled or not) in the debugger. On the command line, it just hangs. When run under Valgrind or Address Sanitizer, both report an illegal read.  
  4. Running either of the tests without running the other shows expected behaviour. No segfault.  
  
And an equally weird workaround: In the test that invokes the LLVM based JIT compiler, at the end, add this piece of code:  
```  
// Without this dummy try-catch, test case "test_throw" will segfault.                                                                                                                                        
try {  
  throw std::exception();  
} catch (std::exception) {  
  ;  
}  
```  
  
For easier reproduction of this behaviour, I have a minimal setup (the best I could do to isolate the issue) here: https://github.com/vaivaswatha/lljit/blob/master/testsuite.cpp  
  
After building the project (by just running `make`), these can be run:  
```  
  $ ./testsuite.exe --run_test=lljit_testsuite/test_throw  
  $ ./testsuite.exe --run_test=lljit_testsuite/lljit_helloworld  
  # Both of these work fine, but the below fails (hangs on the terminal / segfaults under a debugger):  
  $  ./testsuite.exe --run_test=lljit_testsuite/*  
```  
Note: The workaround I have mentioned above is already in this demo code, and that needs to be commented out.  
  
Boost version: 1.65.1  
OS: Ubuntu 18.04

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2020-04-04 00:41:26 UTC  
> Url: https://github.com/boostorg/test/issues/261#issuecomment-608914407  

There were quite a good amount of work since Boost 1.65 on symbols visibility: I suspect that you are having an issue related to that. Do you have the possibility to test on a more recent version of Boost?

---

## Comment 2

> Username: vaivaswatha  
> Created at: 2020-04-04 04:54:05 UTC  
> Url: https://github.com/boostorg/test/issues/261#issuecomment-608973985  

Thank you @raffienficiaud for responding on this. After your suggestion, I now tried this with Boost 1.72 but the issue remains.
