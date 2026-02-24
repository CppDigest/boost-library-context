# #291 - Feature request: templated data test case [Open]

> Username: DarkerStar  
> Created at: 2020-11-01 07:31:20 UTC  
> Updated at: 2020-11-01 07:31:20 UTC  
> Url: https://github.com/boostorg/test/issues/291  

I can generate tests from data using the data-driven API:  
  
```cpp  
constinit auto test_data = std::array{-1, 0, 1, 99};  
  
BOOST_DATA_TEST_CASE(test_1, test_data)  
{  
    // tests using `sample`  
}  
  
// 4 tests generated  
```  
  
I can also generate tests from types using the template test API:  
  
```cpp  
using test_types = std::tuple<int, char, std::string>;  
  
BOOST_AUTO_TEST_CASE_TEMPLATE(test_1, T, test_types)  
{  
    // tests for `T`  
}  
  
// 3 tests generated  
```  
  
But what I often need to do is generate tests from *both* types *and* data:  
  
| Type          | Values         |  
| ------------- | -------------- |  
| `int`         | -1, 0, 1, 99   |  
| `char`        | 'a', 'B', 'z'  |  
| `std::string` | "foo"s, "bar"s  |  
  
The obvious way to do this:  
  
```cpp  
auto const test_data_int = std::array{-1, 0, 1, 99};  
auto const test_data_char = std::array{'a', 'B', 'z'};  
auto const test_data_string = std::array{"foo"s, "bar"s};  
  
BOOST_DATA_TEST_CASE(test_1_with_int,  
	boost::unit_test::data::make(test_data_int))  
{  
	// test 1 for `int` using `sample`  
}  
  
BOOST_DATA_TEST_CASE(test_1_with_char,  
	boost::unit_test::data::make(test_data_char))  
{  
	// test 1 for `char` using `sample`  
}  
  
BOOST_DATA_TEST_CASE(test_1_with_string,  
	boost::unit_test::data::make(test_data_string))  
{  
	// test 1 for `std::string` using `sample`  
}  
  
// 9 tests generated  
```  
  
But that’s obviously a lot of duplication. It gets even worse when you add another type, or another test. You can mitigate this somewhat by delegating all the tests to templated test functions (like: `template <typename T> void test_1(T const& sample)`). But that still leaves a ton of boilerplate.  
  
Another option is to do something like this:  
  
```cpp  
template <typename T>  
auto const test_data = std::array<T, 0>{};  
  
template <>  
auto const test_data<int> = std::array{-1, 0, 1, 99};  
template <>  
auto const test_data<char> = std::array{'a', 'B', 'z'};  
template <>  
auto const test_data<std::string> = std::array{"foo"s, "bar"s};  
  
using test_types = std::tuple<int, char, std::string>;  
  
BOOST_AUTO_TEST_CASE_TEMPLATE(test_1, T, test_types)  
{  
    for (auto&& sample : test_data<T>)  
    {  
        BOOST_TEST_CONTEXT("sample = " << sample)  
        {  
            // test 1 for `T` using `sample`  
        }  
    }  
}  
  
// 3 tests generated  
```  
  
But this only generates 3 tests (one for each type) and not 9 (one for each sample, for each type). There are also other issues that having individual test cases for each sample are supposed to solve, like that if anything throws unexpectedly in the inner test context, all the remaining tests for that type are aborted, and so on and so forth.  
  
What I’d like to be able to do is:  
  
```cpp  
template <typename T>  
auto const test_data = std::array<T, 0>{};  
  
template <>  
auto const test_data<int> = std::array{-1, 0, 1, 99};  
template <>  
auto const test_data<char> = std::array{'a', 'B', 'z'};  
template <>  
auto const test_data<std::string> = std::array{"foo"s, "bar"s};  
  
using test_types = std::tuple<int, char, std::string>;  
  
BOOST_AUTO_TEST_CASE_???(test_1, test_types, T, test_data /*, sample_var */)  
{  
    // test 1 for `T`  (from the elements of tuple test_types)  
    // using `sample` (from the elements of dataset `test_data<T>`)  
}  
  
// 9 tests generated  
```  
  
***  
  
The interface above would work just fine for me. It also jibes with the rest of the Boost.Test API, and probably wouldn’t be too hard to implement. It’s probably all that’s needed.  
  
But, while I was musing over the idea, self-medicating with various mental lubricants, I started to wonder if there might not be a more interesting way to do this. Perhaps it might be possible to define a templated test suite - a test suite generated for each of a set of types - within which a set of tests could be generated. For example:  
  
```cpp  
BOOST_TEST_?TEMPLATED_TEST_SUITE?_BEGIN(name, T)  
  
    BOOST_DATA_TEST_CASE_?(test_1, test_data<T>)  
    {  
        // test 1 for `T`  
        // using `sample` (from the elements of dataset `test_data<T>`)  
    }  
  
BOOST_TEST_?TEMPLATED_TEST_SUITE?_END()  
  
BOOST_TEST_?REGISTER_TEMPLATED_TEST_SUITES?(name, test_types);  
// generates:  
//     name<int>  
//         name<int>::test_1(-1)  
//         name<int>::test_1(0)  
//         name<int>::test_1(1)  
//         name<int>::test_1(99)  
//     name<char>  
//         name<char>::test_1('a')  
//         name<char>::test_1('B')  
//         name<char>::test_1('z')  
//     name<string>  
//         name<string>::test_1("foo"s)  
//         name<string>::test_1("bar"s)  
```  
  
One interesting thing about this is that you could potentially specialize:  
  
```cpp  
BOOST_TEST_?TEMPLATED_TEST_SUITE?_BEGIN(name, T)  
  
    BOOST_DATA_TEST_CASE_?(test_1, test_data<T>) {}  
    BOOST_DATA_TEST_CASE_?(test_2, test_data<T>) {}  
  
BOOST_TEST_?TEMPLATED_TEST_SUITE?_END()  
  
BOOST_TEST_?TEMPLATED_TEST_SUITE?_SPECIALIZE_BEGIN(name, std::string)  
  
    BOOST_DATA_TEST_CASE_?(test_1, test_data<std::string>) {}  
    // test_2 doesn't apply for std::string  
    BOOST_TEST_CASE_?(string_only_test) {}  
  
BOOST_TEST_?TEMPLATED_TEST_SUITE?_SPECIALIZE_END()  
```  
  
But that might be more flexibility than is necessary, because you could also just do:  
  
```cpp  
BOOST_AUTO_TEST_CASE_???(test_1, test_types, T, test_data) {}  
BOOST_AUTO_TEST_CASE_???(test_2, test_types_without_string, T, test_data) {}  
BOOST_AUTO_TEST_CASE(string_only_test) {}  
```  
  
The more interesting thing about the idea of a templated test suite is that maybe you could *nest* them:  
  
```cpp  
using test_types2 = std::tuple<float, double>;  
  
BOOST_TEST_?TEMPLATED_TEST_SUITE?_BEGIN(name, T)  
  
    BOOST_TEST_?TEMPLATED_TEST_SUITE?_BEGIN(inner, U)  
  
        BOOST_DATA_TEST_CASE_?(test_1, test_data<T>)  
        {  
            // test 1 for `T`  
            // using `sample` (from the elements of dataset `test_data<T>`)  
        }  
  
    BOOST_TEST_?TEMPLATED_TEST_SUITE?_END()  
  
    BOOST_TEST_?REGISTER_TEMPLATED_TEST_SUITES?(inner, test_types2);  
  
BOOST_TEST_?TEMPLATED_TEST_SUITE?_END()  
  
BOOST_TEST_?REGISTER_TEMPLATED_TEST_SUITES?(name, test_types);  
// generates:  
//     name<int>  
//         name<int>::inner<float>  
//             name<int>::inner<float>::test_1(-1)  
//             name<int>::inner<float>::test_1(0)  
//             name<int>::inner<float>::test_1(1)  
//             name<int>::inner<float>::test_1(99)  
//         name<int>::inner<double>  
//             name<int>::inner<double>::test_1(-1)  
//             name<int>::inner<double>::test_1(0)  
//             name<int>::inner<double>::test_1(1)  
//             name<int>::inner<double>::test_1(99)  
//     name<char>  
//         name<char>::inner<float>  
//             name<char>::inner<float>::test_1(-1)  
//             name<char>::inner<float>::test_1(0)  
//             name<char>::inner<float>::test_1(1)  
// ... and so on  
```  
  
This could be handy for, for example, testing conversions to/from `T`s to `U`s.  
  
However, all of this is far more complex than what I generally need, and if it’s worth consideration at all, it’s best left for some far future feature set.  
  
***  
  
So this is what I’d actually like to be able to do:  
  
```cpp  
// Assuming:  
template <typename T>  
auto const test_dataset = /* */;  
// ... specialized for each T of interest, as necessary  
  
// And:  
using tuple_like_of_test_types = std::tuple<int, char, std::string>;  
  
BOOST_AUTO_TEST_CASE_???(  
    test_name,  
    tuple_like_of_test_types,  
    T,  
    test_dataset  
    /*, sample_var (etc... same as for BOOST_DATA_TEST_CASE) */)  
{  
    // test 1 for `T`  (from the elements of tuple_like_of_test_types)  
    // using `sample` (from the elements of dataset `test_dataset<T>`)  
}  
  
// The above generates a test case for each sample, for each type.  
```
