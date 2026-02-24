# #1069 - Better diagnostic for value_to errors on described structures [Open]

> Username: sdebionne  
> Created at: 2025-02-06 11:55:06 UTC  
> Updated at: 2025-06-21 12:29:39 UTC  
> Url: https://github.com/boostorg/json/issues/1069  

Given the following use case that comes from the documentation:  
  
```c++  
#include <iostream>  
#include <string>  
  
#include <boost/json/src.hpp>  
#include <boost/describe.hpp>  
#include <boost/exception/diagnostic_information.hpp>  
  
struct money  
{  
    std::string currency;  
    double value;  
};  
  
BOOST_DESCRIBE_STRUCT(money, (), (currency, value))  
  
struct test  
{  
    double pi;  
    bool happy;  
    money object;  
};  
  
BOOST_DESCRIBE_STRUCT(test, (), (pi, happy, object))  
  
int main()  
{  
    boost::json::value jv = {  
        { "pi", 3.141 },  
        { "happy", true },  
        {"object", {  
            //{ "currency", "USD" },  
            { "value", 42.99 }  
        }}  
        };  
  
    try {  
        test res = boost::json::value_to<test>(jv);  
    }  
    catch (std::exception const& ex) {  
        std::cerr << boost::diagnostic_information(ex, true) << std::endl;  
    }  
  
    return 0;  
}  
```  
  
The missing `currency` member gives the following exception:  
  
```bash  
Dynamic exception type: boost::detail::with_throw_location<boost::system::system_error>  
std::exception::what: unknown name [boost.json:39 at /nobackup/lid00lima21/debionne/miniconda3/envs/lima2/include/boost/json/detail/value_to.hpp:371 in function 'operator()']  
```  
  
It would be interesting to have an information where the exception occurred in the json value during `value_to` conversion. Ideally something like `errinfo_json_path="object.concurreny"`.

---

## Comment 1

> Username: kiwixz  
> Created at: 2025-02-08 00:03:24 UTC  
> Url: https://github.com/boostorg/json/issues/1069#issuecomment-2644350319  

I also miss that feature very much but I don't know if that's possible with only the error code, so I worry this will get wontfix-ed...  
  
FYI I implemented it with exceptions in my boost::pfr tag_invoke (which I use instead of describing structs):  
```cpp  
template <typename T, typename Context>  
requires IsJsonAggregate<T>::value  
T tag_invoke(boost::json::value_to_tag<T>, const boost::json::value& json, const Context& ctx) {  
    const boost::json::object& obj = json.as_object();  
  
    T r;  
  
    boost::pfr::for_each_field(r, [&](auto& field, auto i) {  
        std::string_view name = boost::pfr::get_name<i, T>();  
  
        auto it = obj.find(name);  
        if (it == obj.end())  
            throw Exception{"Missing field {}", name};  
  
        try {  
            field = boost::json::value_to<std::decay_t<decltype(field)>>(it->value(), ctx);  
        }  
        catch (const std::exception& ex) {  
            throw Exception{"Could not parse {}: {}", name, ex.what()};  
        }  
    });  
  
    return r;  
}  
```

---

## Comment 2

> Username: grisumbras  
> Created at: 2025-02-11 08:04:21 UTC  
> Url: https://github.com/boostorg/json/issues/1069#issuecomment-2650066888  

A contributor is working on something in this area, and I also have some ideas. But most likely such feature will require customisation, potentially defining a macro.

---

## Comment 3

> Username: sdebionne  
> Created at: 2025-03-31 08:15:37 UTC  
> Url: https://github.com/boostorg/json/issues/1069#issuecomment-2765464768  

With #1070 and #1075, is there anything that we could with? Like testing?

---

## Comment 4

> Username: RoyBellingan  
> Created at: 2025-06-21 12:28:33 UTC  
> Updated at: 2025-06-21 12:29:39 UTC  
> Url: https://github.com/boostorg/json/issues/1069#issuecomment-2993556318  

@sdebionne sorry for the long absence, if you want I can send you the patch (https://github.com/dublinbranch/rbk/blob/master/BoostJson/intrusivedebug.h and related file in https://github.com/dublinbranch/rbk/tree/master/BoostJson/override depending on the boost version)  
  
From that code I created the one in https://github.com/boostorg/json/pull/1070  
  
Is very crude, and is mostly only for my use case, but I would love feedback.  
Also I am an amateur, so code is definitively subpar and need some input / love.  
  
I will try to check the #1075 too
