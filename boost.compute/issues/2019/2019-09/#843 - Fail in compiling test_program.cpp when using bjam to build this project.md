# #843 - Fail in compiling test_program.cpp when using bjam to build this project [Open]

> Username: yuxianch  
> Created at: 2019-09-30 02:24:38 UTC  
> Updated at: 2019-09-30 02:24:38 UTC  
> Url: https://github.com/boostorg/compute/issues/843  

Hi all,  
I noticed that there are CMakeLists.txt and Jamfile.v2 under compute/test directory. When I tried to use bjam to build, I got errors when running tests.  
For example, when compiling test_program.cpp, I saw errors like this:  
```  
test_program.cpp:136:27: error: unknown type name 'BOOST_COMPUTE_TEST_DATA_PATH'  
    std::string file_path(BOOST_COMPUTE_TEST_DATA_PATH);  
                          ^  
test_program.cpp:136:26: warning: parentheses were disambiguated as a function  
      declaration [-Wvexing-parse]  
    std::string file_path(BOOST_COMPUTE_TEST_DATA_PATH);  
                         ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
However, "BOOST_COMPUTE_TEST_DATA_PATH" is defined in CMakeLists.txt. So, this test should be compiled successfully if I use cmake to build this project, but why does Jamfile.v2 exist in this project? Should Jamfile.v2 be updated?
