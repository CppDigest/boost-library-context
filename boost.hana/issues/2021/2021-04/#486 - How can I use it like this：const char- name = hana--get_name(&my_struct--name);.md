# #486 - How can I use it like this：const char* name = hana::get_name(&my_struct::name); [Closed]

> Username: segeter  
> Created at: 2021-04-20 05:37:15 UTC  
> Updated at: 2022-10-21 02:31:05 UTC  
> Closed at: 2022-10-21 02:31:05 UTC  
> Url: https://github.com/boostorg/hana/issues/486  

`  
struct my_struct  
{  
  BOOST_HANA_DEFINE_STRUCT(my_struct,  
        (std::string, name)  
    );  
};  
int main()  
{  
  const char* name = hana::get_name(&my_struct::name);  
}  
`  
  
Thanks

---

## Comment 1

> Username: ricejasonf  
> Created at: 2021-04-20 18:07:44 UTC  
> Url: https://github.com/boostorg/hana/issues/486#issuecomment-823491125  

It appears you are trying to get the name of a Struct member from its pointer-to-member.  
  
With the provided macros such as `BOOST_HANA_DEFINE_STRUCT` this is not supported. You would have to go dive into  
the implementation details to make such a function.  
  
One option is to bypass the macro and implement `hana::accessors_impl` yourself so you can make a function that reliably  
does what you want here.  
  
I made an example based on `hana::struct_detail` which holds info about the member pointer. This is not officially supported so  
use at your own risk.  
  
```cpp  
#include <boost/callable_traits.hpp>  
#include <boost/hana.hpp>  
#include <string>  
  
namespace hana = boost::hana;  
namespace callable_traits = boost::callable_traits;  
  
struct my_struct {  
    BOOST_HANA_DEFINE_STRUCT(my_struct, (std::string, name) );  
};  
  
template <typename T, typename M, M memptr>  
struct match_memptr_t : std::false_type { };  
  
template <typename Name, typename M, M memptr>  
struct match_memptr_t<hana::pair<Name,  
        hana::struct_detail::member_ptr<M, memptr>>, M, memptr>  
    : std::true_type  
{ };  
  
template <auto memptr>  
constexpr auto match_memptr = [](auto X) {  
    return match_memptr_t<decltype(X), decltype(memptr), memptr>();  
};  
  
template <auto memptr>  
constexpr auto get_name() {  
    using Struct = callable_traits::class_of_t<decltype(memptr)>;  
    auto result = hana::find_if(hana::accessors<Struct>(),  
                                match_memptr<memptr>).value();  
    return hana::first(result);  
}  
  
int main() {  
    static_assert(get_name<&my_struct::name>() == BOOST_HANA_STRING("name"));  
    char const* name = get_name<&my_struct::name>().c_str();  
}  
```  
https://godbolt.org/z/Kvfdor45z  
  
Note that you can't get static information from a function parameter's value so you have to pass it as a template parameter.  
Also, I used C++17 deduced auto non-type-template-parameter. Without it the interface would be more verbose.
