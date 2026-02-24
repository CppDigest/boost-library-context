# #31 - How to use name of the member with Boost Hana? [Closed]

> Username: denzor200  
> Created at: 2022-07-04 10:44:04 UTC  
> Updated at: 2022-07-05 04:11:02 UTC  
> Closed at: 2022-07-05 04:11:02 UTC  
> Url: https://github.com/boostorg/describe/issues/31  

```  
#include <boost/describe.hpp>  
#include <boost/mp11.hpp>  
#include <boost/hana.hpp>  
#include <cstdio>  
  
enum E {  
    v1 = 11,  
    v2,  
    v3 = 5  
};  
BOOST_DESCRIBE_ENUM(E, v1, v2, v3)  
  
int main()  
{  
    constexpr auto D = boost::mp11::mp_front< boost::describe::describe_enumerators<E> >();  
    std::printf( "%s: %d\n", D.name, D.value );  
}  
```  
Is there a way to convert value of `D.name` to `boost::hana::string`?  
https://www.boost.org/doc/libs/1_79_0/libs/hana/doc/html/structboost_1_1hana_1_1string.html  
If not, is it planned to introduce a similar feature on the **Boost Describe** side?

---

## Comment 1

> Username: pdimov  
> Created at: 2022-07-04 12:06:33 UTC  
> Url: https://github.com/boostorg/describe/issues/31#issuecomment-1173741118  

Like this:  
```  
namespace hana = boost::hana;  
  
template<class D, std::size_t... I> auto to_hana_string_impl( std::index_sequence<I...> )  
{  
    return hana::string_c< D::name[I]... >;  
}  
  
template<class D> auto to_hana_string()  
{  
    return to_hana_string_impl<D>( std::make_index_sequence<__builtin_strlen(D::name)>() );  
}  
```  
(https://godbolt.org/z/G5c51er4W)  
  
It's a bit more complex than it needs to be, but see https://github.com/boostorg/describe/pull/10 for discussion why this is so.

---

## Comment 2

> Username: denzor200  
> Created at: 2022-07-05 04:11:02 UTC  
> Url: https://github.com/boostorg/describe/issues/31#issuecomment-1174586530  

Thanks for the detailed answer!
