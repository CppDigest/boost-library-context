# #307 - Comparing resulting files [Open]

> Username: ohhmm  
> Created at: 2021-04-01 22:35:15 UTC  
> Updated at: 2022-03-07 20:25:09 UTC  
> Url: https://github.com/boostorg/test/issues/307  

It would be nice and cozy to have a macro for file result comparision.  
  
A potential body for the file comparision test macro:  
  
	std::ifstream ifs1(TEST_SRC_DIR "input/file/path");  
	std::ifstream ifs2(TEST_SRC_DIR "gold/file/path");  
  
	std::istream_iterator<char> b1(ifs1), b2(ifs2), e;  
  
	BOOST_CHECK_EQUAL_COLLECTIONS(b1, e, b2, e);

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2022-03-07 20:25:09 UTC  
> Url: https://github.com/boostorg/test/issues/307#issuecomment-1061102187  

There is already something close to what you are asking: https://www.boost.org/doc/libs/1_78_0/libs/test/doc/html/boost_test/testing_tools/output_stream_testing.html . We need to polish it a bit and/or document it better.  
  
Would this work for you? What do you think of that feature?
