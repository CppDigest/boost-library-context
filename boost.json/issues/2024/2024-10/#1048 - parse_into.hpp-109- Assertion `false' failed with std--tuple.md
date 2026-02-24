# #1048 - parse_into.hpp:109: Assertion `false' failed with std::tuple [Closed]

> Username: tyler92  
> Created at: 2024-10-06 14:14:15 UTC  
> Updated at: 2024-10-07 19:41:20 UTC  
> Closed at: 2024-10-07 19:41:20 UTC  
> Url: https://github.com/boostorg/json/issues/1048  

### Version of Boost  
  
`devel` branch, commit `85f9e92c18283d42737ebd067df4f881824e4edb`  
  
### Steps necessary to reproduce the problem  
  
```cpp  
#include <boost/json/parse_into.hpp>  
#include <boost/describe/class.hpp>  
  
struct Test  
{  
  std::tuple<bool, int> t1;  
};  
  
BOOST_DESCRIBE_STRUCT(Test, (), (t1))  
  
int main()  
{  
  Test object{};  
  std::error_code ec;  
  
  std::string data = R"({"t1":[]})";  
  boost::json::parse_into(object, data, ec);  
  
  return 0;  
}  
```  
  
Output:  
  
```  
test-parse: boost/json/detail/parse_into.hpp:109: bool boost::json::detail::handler_error_base<boost::json::error::not_bool>::on_object_end(system::error_code &) [E = boost::json::error::not_bool]: Assertion `false' failed.  
Aborted (core dumped)  
```  
  
### All relevant compiler information  
  
Clang version 17.0.6

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-10-07 18:36:21 UTC  
> Url: https://github.com/boostorg/json/issues/1048#issuecomment-2397621964  

Thank you for catching this!
