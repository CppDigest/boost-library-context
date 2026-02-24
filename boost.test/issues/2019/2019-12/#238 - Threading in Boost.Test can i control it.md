# #238 - Threading in Boost.Test can i control it? [Closed]

> Username: martopad  
> Created at: 2019-12-01 12:30:13 UTC  
> Updated at: 2020-01-24 10:12:21 UTC  
> Closed at: 2020-01-24 10:12:21 UTC  
> Url: https://github.com/boostorg/test/issues/238  

Greetings Im back XD.   
  
Thanks to you, I have successfully migrated to 1.64!  I now want to use the full functionality of Boost.Test 1.64. Currently, by how this test framework is set up I am unable to use the new functionalities of Boost.test specifically test-case decorators and filters.  
  
Our test runner executes test cases roughly like this:  
    1) Our testing framework will first traverse the whole test tree first, (if there is a pattern provided it will create a "filter" then traverse the tree disabling test cases that didn't pass the filter)  
     2) The testing framework will create the test runner (a singleton).   
     3) The test runner will then discover all the enabled test cases and will assemble the complete absolute path for each (I.E. test_case_1 will result to "MT/test_suite_1/test_case_1")  
     4) Now the test runner will then call **unit_test_main** for each test cases (note that it will pass the complete absolute path for each)  
  
So before Boost.Test runs, our testing framework already does a lot of magic. I think the main reason for this is that our testing framework needs to identify each individual test-case in order for each test-case to be run on their own separate thread (which I assume will lead to stable test-cases, since each test-case will only be ran on only one thread).   
  
I have tried removing this custom test runner, and relying more on boost test. Running the whole suite results in getting a lot of memory-related issues(albeit consistently failing). But manually running them one by one just passes.   
  
I would assume that this is a threading issue. Is there a way to control this while at the same time relying on boost.test's test runner?
