# #292 - Feature request: helper macro to unpack tuples of test types [Open]

> Username: DarkerStar  
> Created at: 2020-11-01 09:07:13 UTC  
> Updated at: 2020-11-01 09:07:13 UTC  
> Url: https://github.com/boostorg/test/issues/292  

The template test API lets me do this:  
  
```cpp  
using test_types = std::tuple<int, char, std::string>;  
  
BOOST_AUTO_TEST_CASE_TEMPLATE(test_1, T, test_types)  
{  
    // tests for `T`  
}  
  
// 3 tests generated  
```  
  
This is great. I love this.  
  
But sometimes I need to do tests on *sets* of types rather than on a *single* type.  
  
I can handle that situation like this:  
  
```cpp  
using test_types = std::tuple<  
	std::tuple<int, char>,  
	std::tuple<float, double>,  
	std::tuple<std::vector<char>, std::string>  
>;  
  
BOOST_AUTO_TEST_CASE_TEMPLATE(test_1, Ts, test_types)  
{  
    using T = std::tuple_element_t<0, Ts>;  
    using U = std::tuple_element_t<1, Ts>;  
  
    // tests for `T`, `U`  
}  
  
// 3 tests generated  
```  
  
And this works fine, too. Nothing wrong with any of this.  
  
But when there’s a test failure, discerning the types that triggered the failure from the test output can be, to put it mildly, unmild.  
  
So what I’d like to propose is a new preprocessor macro. Let’s call it `BOOST_TEST_EXPAND_TYPES(type_list, T...)` (bike-shedding TBD).  
  
What it would do is two-fold:  
  
- It would alias each type in the given type list to the identifier given in the variadic list, in order.  
- It would *effectively* add a `BOOST_TEST_INFO_SCOPE()` that records the alias in the test context.  
  
For example:  
  
```cpp  
using test_types = std::tuple<  
	std::tuple<int, float>  
>;  
  
BOOST_AUTO_TEST_CASE_TEMPLATE(test_1, Ts, test_types)  
{  
    BOOST_TEST_EXPAND_TYPES(Ts, T, U)  
  
    // The above *BASICALLY* expands to:  
    //     using T = std::tuple_element_t<0, Ts>;  
    //     BOOST_TEST_INFO_SCOPE("using T = " << __name_of_type(T));  
    //     using U = std::tuple_element_t<1, Ts>;  
    //     BOOST_TEST_INFO_SCOPE("using U = " << __name_of_type(U));  
  
    // `T` is now `int`  
    // `U` is now `float`  
  
    BOOST_TEST(sizeof(T) != sizeof(U));  
}  
```  
  
Note that if `std::tuple_size_v<Ts>` is less than the number of identifiers provided, or if Ts doesn’t support the tuple-like (it really just needs to support `std::tuple_element<N, Ts>` and/or the Boost.MP11, Hana, whatever, equivalents), then that should be a compile error.  
  
And when the test fails, the output would be something like:  
  
```txt  
test.cpp(??): error: in "test_1<std__tuple<int_ float>>": check sizeof(T) != sizeof(U) has failed [4 == 4]  
Failure occurred in a following context:  
    using T = int  
    using U = float  
```  
  
For the above example, it’s not hard to spot the `int` and the `float` in the first line, but even then it’s still not clear what `T` and `U` are in the actual test. The context provided is enormously helpful.  
  
Implementing `BOOST_TEST_EXPAND_TYPES()` shouldn’t be too hard, but it’s not something the user can do because (so far as I can tell), while Boost.Test has some `__name_of_type(???)` function internally, it isn’t exposed to the user, and implementing it is far from trivial. (As an aside, perhaps Boost.Test *should* expose a way to pretty-print types!)  
  
In summary:  
  
```cpp  
BOOST_TEST_EXPAND_TYPES(Types, T1, ...)  
  
Expands *as-if* to:  
  
using T1 = std::tuple_element_t<0, Types>;  
BOOST_TEST_INFO_SCOPE("using T1 = " << __name_of_type(T1));  
// ... above repeated, replacing T1 with each subsequent identifier  
  
Where:  
- __name_of_type(T): pretty-prints the type T  
```
