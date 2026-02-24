# #341 - Better cmake/ctest integration [Open]

> Username: t-b  
> Created at: 2022-06-13 20:02:22 UTC  
> Updated at: 2023-10-11 13:48:17 UTC  
> Url: https://github.com/boostorg/test/issues/341  

I started using Boost.Test (again) in a new project. I would like to use cmake/ctest for building and test management. Unfortunately this is not easy possible with 1.74 (debian bullseye). In case it is interesting my current version is in https://gitlab.com/tango-controls/TangoDatabase/-/merge_requests/47. I've followed the https://eb2.co/blog/2015/06/driving-boost.test-with-cmake/.  
  
Current shortcomings:  
- The test source code needs to be parsed with cmake to be able to generate a list of tests.This is suboptimal.  
- Due to the approach of calling the boost test runner separately for each testcase and excluding all other tests, the JUNIT files are a bit confusing. For three test cases you get three JUNIT files where in each file two test cases are ignored. This would need a flag for boost.Test to skip outputting skipped test cases.  
  
Is there a better approach? Or am I forcing ctest over Boost.Test which is the "wrong" approach?

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2022-06-13 21:47:30 UTC  
> Url: https://github.com/boostorg/test/issues/341#issuecomment-1154471727  

Hi there,  
  
The blog post your are pointing me to basically makes CTest the first citizen. I believe this was written before the integration of Junit into Boost.Test and the approach can be simplified.  
  
I use CTest as a test driver driver only (running things in parallel, indicating the resources etc) and it is quite handy in case something really goes wrong with the test module itself. However I never use CMake or CTest for introspection of the code, as Boost.Test can generate JUnit files that are detailed enough. Those JUnit reports are then interpreted by the CI framework (Bamboo, Jenkins, etc) and a clear report can be visualized.  
  
Junit files can contain many tests, and they also show the test suite.  
  
To summarize:  
  
1. one test module (ie many test units and test cases) => one JUnit  
2. no introspection of the code from an external tool   
3. report from the CI directly from the JUnit  
  
Does that work?

---

## Comment 2

> Username: t-b  
> Created at: 2023-10-11 13:48:17 UTC  
> Url: https://github.com/boostorg/test/issues/341#issuecomment-1757738242  

Thanks for the detailed answer.  
  
The main reason for the code instrospection is that this allows to execute single test cases with CTest and also allows per test case progress output in ctest. I don't see a way currently to do that without code introspection.
