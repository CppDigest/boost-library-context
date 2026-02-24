# #1095 - Bug: boost::json::number_precision::none not working [Closed]

> Username: cryptochassis  
> Created at: 2025-07-30 21:00:26 UTC  
> Updated at: 2025-08-11 19:39:25 UTC  
> Closed at: 2025-08-11 19:39:25 UTC  
> Url: https://github.com/boostorg/json/issues/1095  

Use version 1.87.0.  
```  
#include <boost/json.hpp>  
#include <iostream>  
#include <boost/json/parse_options.hpp>  
  
int main() {  
    std::string input = R"({ "value": 3.14000, "int_val": 42 })";  
  
    boost::json::parse_options opts;  
    opts.numbers = boost::json::number_precision::none;  
  
    boost::json::value jv = boost::json::parse(input, {}, opts);  
  
    const auto& obj = jv.as_object();  
    std::cout << obj.at("value").as_string() << "\n";    // Should Outputs: 3.14000  
    std::cout << obj.at("int_val").as_string() << "\n";  // Should Outputs: 42  
}  
```  
Then program can compile. But when running, it throws an exception:  
```  
terminate called after throwing an instance of 'boost::detail::with_throw_location<boost::system::system_error>'  
  what():  value is not a string [boost.json:32 at ./boost/json/impl/value.ipp:425 in function 'try_as_string']  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2025-07-31 09:39:32 UTC  
> Url: https://github.com/boostorg/json/issues/1095#issuecomment-3139237439  

See here: https://www.boost.org/doc/libs/latest/libs/json/doc/html/json/ref/boost__json__parse_options/numbers.html  
  
The mode doesn't change the resulting type of numbers parsed, it just doesn't actually parse them (but still validates them). You can look at it as parsing with no precision (which is why it is called `number_precision::none`).  
  
The mode is intended to be used with custom instantiations of [`basic_parser`](https://www.boost.org/doc/libs/latest/libs/json/doc/html/json/ref/boost__json__basic_parser.html).  
  
The `handler` used by `basic_parser` has these member functions:  
```c++  
bool on_number_part( string_view s, error_code& ec );  
bool on_int64( int64_t i, string_view s, error_code& ec );  
bool on_uint64( uint64_t u, string_view s, error_code& ec );  
bool on_double( double d, string_view s, error_code& ec );  
```  
  
With `number_precision::none` the number arguments would contain some unspecified numbers, and you are supposed to deal with number parsing yourself (using the `string_view` argument).

---

## Comment 2

> Username: cryptochassis  
> Created at: 2025-07-31 17:04:27 UTC  
> Updated at: 2025-07-31 17:05:35 UTC  
> Url: https://github.com/boostorg/json/issues/1095#issuecomment-3140714649  

I've read the detailed documentation about basic_parser, but it is a bit hard to follow from an external user's perspective. Could you show me a quick example of how to use a custom instantiation so that this   
```  
int main() {  
    std::string input = R"({ "value": 3.14000, "int_val": 42 })";  
  
    boost::json::parse_options opts;  
    opts.numbers = boost::json::number_precision::none;  
  
    boost::json::value jv = boost::json::parse(input, {}, opts);  
  
    const auto& obj = jv.as_object();  
    std::cout << obj.at("value").as_string() << "\n";    // Should Outputs: 3.14000  
    std::cout << obj.at("int_val").as_string() << "\n";  // Should Outputs: 42  
}  
```  
could work (i.e. get all the numbers as strings)? Thank you!

---

## Comment 3

> Username: grisumbras  
> Created at: 2025-08-04 20:48:09 UTC  
> Url: https://github.com/boostorg/json/issues/1095#issuecomment-3152369388  

Sorry for the big delay.  
  
See here: https://godbolt.org/z/KeW333szG.  
  
There's an issue with the snippet I've provided, though: `detail::handler` is not public API. Ideally, you'd copy its implementation for your handler. I do plan to make the handler public in the future.
