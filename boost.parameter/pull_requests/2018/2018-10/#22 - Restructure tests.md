# #22 Restructure tests [Merged]

> Username: CromwellEnage  
> Created at: 2018-10-28 17:15:26 UTC  
> Updated at: 2018-10-29 11:40:38 UTC  
> Merged at: 2018-10-29 09:02:58 UTC  
> Closed at: 2018-10-29 09:02:58 UTC  
> Url: https://github.com/boostorg/parameter/pull/22  

<boost/parameter/parameters.hpp>:  
* Add preprocessor conditional statement to prevent generation of ill-formed function call operator overloads.  
  
"test/maybe.cpp"  
"test/singular.cpp"  
"test/tutorial.cpp"  
"test/sfinae.cpp"  
"test/earwicker.cpp"  
* Replace deprecated BOOST_PARAMETER_KEYWORD statements with BOOST_PARAMETER_NAME statements.  
  
"test/optional_deduced_sfinae.cpp"  
"test/normalized_argument_types.cpp"  
"test/literate/*.cpp"  
* Use Boost.Core.LightweightTest where int main() is available.  
* Replace assert statements with BOOST_TEST_EQ statements.  
  
"test/basics.hpp"  
* Remove preprocessor statements regarding borland, gcc-2, and msvc workarounds.  
  
"test/ntp.cpp"  
"test/sfinae.cpp"  
"test/earwicker.cpp"  
"test/normalized_argument_types.cpp"  
"test/basics.hpp"  
* Add preprocessor conditional statement to #error out if BOOST_PARAMETER_MAX_ARITY is set to an insufficient value.  
  
"test/basics.cpp"  
"test/deduced.cpp"  
"test/macros.cpp"  
"test/preprocessor.cpp"  
"test/preprocessor_deduced.cpp"  
* Replace S and char const* expressions with boost::container::string expressions.  
* Uncomment any code that fails to compile, but add preprocessor conditional statement so that test suites can incorporate compile-fail statements regarding the code in question.  
* Ensure that int main() returns boost::process_errors().  
  
"test/literate/deduced-template-parameters0.cpp":  
"test/literate/exercising-the-code-so-far0.cpp":  
* Enclose BOOST_MPL_ASSERT statements within MPL_TEST_CASE block.  
  
"test/literate/defining-the-keywords1.cpp":  
* Add graphs::tag::graph::qualifier type definition because perfect forwarding code will check for it.  
* Replace deprecated keyword::get() invocation with keyword::instance invocation.  
  
test/Jamfile.v2:  
* Add modifier setting macro BOOST_PARAMETER_MAX_ARITY=# to run, run-fail, compile, and compile-fail statements to conserve compiler memory usage on GitHub's side.  
* Add modifier setting preserve-target-tests=off to run and run-fail statements to conserve executable space on GitHub's side.  
* Separate bpl-test statement into its own target, parameter_python_test, which fails on xcode8.3 as well as on mingw and msvc compilers with address-model=64.  
* The next commit (which will implement perfect forwarding) will subsume test/literate/Jamfile.v2 into this file.  Strangely enough, attempting to do so now will result in compiler errors.  
  
.travis.yml:  
* Add g++-4.7, g++-4.8, g++-4.9, clang++-3.5, clang++-3.6, clang++-3.7, clang++-3.8, clang++-3.9, clang++-4.0, xcode7.3, and xcode8.3 compiler configurations.  
* Split compiler configurations by available CXXSTD values.  (This will keep the job times within limits for the next commit.)  
* Ensure that the xcode8.3 compiler configurations exclude parameter_python_test from the test suite.  
  
appveyor.yml:  
* Add compiler configurations that support address-model=64 to the test matrix.  
* Ensure that the new configurations exclude parameter_python_test from the test suite.

---

## Comment 1

> Username: eldiener  
> Created_at: 2018-10-29 01:51:51 UTC  
> Url: https://github.com/boostorg/parameter/pull/22#issuecomment-433765214  

In some tests you have: "Replace S and char const* expressions with boost::container::string expressions."  
  
Why do we want to do that ? If you feel that we should use strings rather than "char const *' why are we not using std::string ? Adding a dependency on boost::container::string needs a good reason.

---

## Comment 2

> Username: CromwellEnage  
> Created_at: 2018-10-29 02:16:42 UTC  
> Url: https://github.com/boostorg/parameter/pull/22#issuecomment-433768380  

Oops.  I just saw your response after I pushed my latest commit.  I'll see about changing boost::container::string to std::string.

---
