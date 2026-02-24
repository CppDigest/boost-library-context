# #191 - empty test should pass successfuly [Closed]

> Username: ohhmm  
> Created at: 2018-12-04 12:18:37 UTC  
> Updated at: 2019-02-28 07:28:27 UTC  
> Closed at: 2018-12-16 17:34:45 UTC  
> Url: https://github.com/boostorg/test/issues/191  

lets say we have single disabled test:  
BOOST_AUTO_TEST_CASE(test_name ,*disabled())  
  
it should not crash/fail:  
boost::unit_test::ut_detail::throw_exception<boost::unit_test::framework::setup_error>(e=<unavailable>) at throw_exception.hpp:48 [opt]  
   45  	  
   46  	template<typename E>  
   47  	BOOST_NORETURN inline void  
-> 48  	throw_exception(E const& e) { [4mt[0mhrow e; }  
   49  	  
   50  	#define BOOST_TEST_I_TRY try  
   51  	#define BOOST_TEST_I_CATCH( T, var ) catch( T const& var )  
  
instead it may successfully end with 0 tests passed

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-12-16 17:34:45 UTC  
> Url: https://github.com/boostorg/test/issues/191#issuecomment-447661323  

It was always so and on design: there should be at least one test active in the test module. This is mostly to prevent silent mistakes that can come from the test configuration or the command line filter.

---

## Comment 2

> Username: ohhmm  
> Created at: 2018-12-17 16:21:50 UTC  
> Url: https://github.com/boostorg/test/issues/191#issuecomment-447904612  

Thanks. What about disabled tests? What should I do if I disable the only test in a module. Also what should I do if I want optionally to declare tests right in the cpp where the tested class is implemented (try  to imagine cmakelists.txt file)? And can it crash w/o tests but not crash with disabled tests?

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2019-02-28 07:28:26 UTC  
> Url: https://github.com/boostorg/test/issues/191#issuecomment-468167243  

Hi, sorry for the late answer, I did not see your post.   
The behaviour is the same in case you have disabled tests, whether this is done at runtime or compile time: if, when running the test module, boost.test detects an empty test-tree, it flags this as an error. If you do not want the set of tests to be an issue, you can always declare a dummy test that is executed in all cases, and force its execution with command line.   
  
Would that work for you?
