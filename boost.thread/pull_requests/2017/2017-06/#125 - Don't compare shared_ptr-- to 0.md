# #125 Don't compare shared_ptr<> to 0 [Merged]

> Username: vslavik  
> Created at: 2017-06-07 15:33:20 UTC  
> Updated at: 2017-06-07 15:57:22 UTC  
> Merged at: 2017-06-07 15:57:22 UTC  
> Closed at: 2017-06-07 15:57:22 UTC  
> Url: https://github.com/boostorg/thread/pull/125  

Update the code to consistently use a check for `.get() == 0`, as was already done in most, but not all, places (presumably precisely because issues like this), to avoid issues with ambiguous overloaded `operator==` and `operator!=`.  
  
The motivating example is use with [JSON for Modern C++](https://github.com/nlohmann/json) that does some unfortunate operator overloading (see https://github.com/nlohmann/json/issues/610 for the respective bug) and this simple use of futures doesn't compile:  
  
```cpp  
#define BOOST_THREAD_VERSION 4  
#include <boost/thread/future.hpp>  
#include <json.hpp>  
  
int main()  
{  
    boost::future<nlohmann::json> f;  
    f.get();  
    return 0;  
}  
```  
  
This fails due to ambiguous operators:  
```  
boost/thread/future.hpp:1683:31: error: use of overloaded operator '==' is  
      ambiguous (with operand types 'future_ptr' (aka 'shared_ptr<detail::shared_state<basic_json<std::map, std::vector,  
      basic_string<char>, bool, long long, unsigned long long, double, std::allocator, adl_serializer> > >') and 'int')  
            if (this->future_ == 0)  
                ~~~~~~~~~~~~~ ^  ~  
test.cpp:11:7: note: in instantiation of member function 'boost::future<nlohmann::basic_json<std::map, std::vector,  
      std::__1::basic_string<char>, bool, long long, unsigned long long, double, std::allocator, adl_serializer> >::get'  
      requested here  
    f.get();  
      ^  
boost/smart_ptr/shared_ptr.hpp:814:31: note: candidate function [with T =  
      boost::detail::shared_state<nlohmann::basic_json<std::map, std::vector, std::__1::basic_string<char>, bool, long  
      long, unsigned long long, double, std::allocator, adl_serializer> >]  
template<class T> inline bool operator==( shared_ptr<T> const & p, boost::detail::sp_nullptr_t ) BOOST_NOEXCEPT  
                              ^  
json.hpp:6334:17: note: candidate function  
      [with ScalarType = int, $1 = 0]  
    friend bool operator==(const_reference lhs, const ScalarType rhs) noexcept  
                ^  
json.hpp:6253:17: note: candidate function  
    friend bool operator==(const_reference lhs, const_reference rhs) noexcept  
                ^  
1 error generated.  
```  
  
While it can be reasonably argued that this is an issue with the `json` class, I think this should be addressed in `boost::future<>` too, because  
  
1. `std::future<>` doesn't have this problem  
2. `boost::future<>` should arguably work with any movable time  
3. it makes the code consistent, instead of being a mix of two kinds of null checks  
  
(My preferred fix would be to compare to `nullptr` instead, but that can't be done for obvious compatibility reasons. I thought about using implicit bool conversion instead (`if (this->future_)`) but then I noticed the many existing uses of `.get() == 0` already in there, so opted for continuing in that style.)

---

## Comment 1

> Username: viboes  
> Created_at: 2017-06-07 15:56:53 UTC  
> Updated_at: 2017-06-07 15:57:10 UTC  
> Url: https://github.com/boostorg/thread/pull/125#issuecomment-306840282  

Ok, I accept the PR.   
  
I would had preferred to have something like BOOST_NULLPTR having type boost::detail::sp_nullptr_t.

---
