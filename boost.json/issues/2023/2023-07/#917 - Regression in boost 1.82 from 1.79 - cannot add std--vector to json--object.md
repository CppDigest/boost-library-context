# #917 - Regression in boost 1.82 from 1.79 - cannot add std::vector to json::object [Closed]

> Username: steve-numeus  
> Created at: 2023-07-14 14:07:48 UTC  
> Updated at: 2023-07-18 15:47:56 UTC  
> Closed at: 2023-07-14 14:12:37 UTC  
> Url: https://github.com/boostorg/json/issues/917  

### Version of Boost  
  
1.82  
  
### Steps necessary to reproduce the problem  
  
Upgrade from boost 1.79 to 1.82  
  
Previously it was possible to add a `std::vector<std::string>` to a `boost::json::obejct`, this no longer works.  
  
```  
#include <iostream>  
#include <vector>  
#include <boost/json.hpp>  
  
int main()  
{  
    std::vector<std::string> channels = {"foo", "bar"};  
    boost::json::object params = {{"channels", std::move(channels)}};  
    std::cout << params << '\n';  
    return 0;  
}  
```  
  
Error:  
  
```  
/usr/include/boost/json/impl/value_ref.hpp:49:12: error: no matching function for call to  
    boost::json::value::value(std::remove_reference<std::vector<std::__cxx11::basic_string<char> >&>::type,  
        std::remove_reference<boost::json::storage_ptr&>::type)  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-07-14 14:12:22 UTC  
> Url: https://github.com/boostorg/json/issues/917#issuecomment-1635924106  

Duplicate of #823. In short, this was never supposed to work. Replace `{{"channels", std::move(channels)}}` with  `{{"channels", boost::json::value_from(channels)}}`

---

## Comment 2

> Username: steve-numeus  
> Created at: 2023-07-14 14:17:55 UTC  
> Url: https://github.com/boostorg/json/issues/917#issuecomment-1635931639  

Thanks for the very quick update!  
  
Since it did work before, (even if not supposed to) what is the reasoning not to support it?  
  
Seems like a fairly ergonomic qol improvement to support?

---

## Comment 3

> Username: grisumbras  
> Created at: 2023-07-14 15:46:22 UTC  
> Url: https://github.com/boostorg/json/issues/917#issuecomment-1636047080  

The syntax was duplicitious. For example, you were trying to use a move there, which implies you expected some kind of move to happen. In reality, a conversion using `value_from` was performed. Now you need to use the conversion explicitly.

---

## Comment 4

> Username: steve-numeus  
> Created at: 2023-07-17 06:41:46 UTC  
> Url: https://github.com/boostorg/json/issues/917#issuecomment-1637464699  

Fair point. Thanks for the response

---

## Comment 5

> Username: steve-numeus  
> Created at: 2023-07-17 11:40:47 UTC  
> Url: https://github.com/boostorg/json/issues/917#issuecomment-1637957801  

Sorry for coming back to this, but I'm still struggling to understand the rationale here  
  
If I've created a `tag_invoke` customisation point for my type:  
  
```cpp  
inline void tag_invoke(boost::json::value_from_tag, boost::json::value& jv, Foo f)  
{  
    jv = boost::json::value_from(f.to_string(), jv.storage());  
}  
```  
  
Why doesn't it participate in overload resolution when creating an `object` using an initializer list?  
  
```cpp  
std::string s = "hello world";  
Foo f = {};  
  
boost::json::object o = {  
    { "msg": s },  
    { "foo": f }             // tag_invoke found  
};  
```  
  
Now we're required to specify `boost::json::value_from`:  
  
```cpp  
Foo f;  
boost::json::object o = {  
    { "msg": s },                          // implicit  
    { "foo": boost::json::value_from(f) }  // tag_invoke no longer found  
};  
```  
  
Rather unfortunately it results in arguably quite boilerplatey usage:  
  
Before:  
  
```cpp  
    return boost::json::object {  
        {"time", time},  
        {"inst", inst},  
        {"ttm", ttm},  
        {"domestic_rate", domestic_rate},  
        {"foreign_rate", foreign_rate},  
        {"spot_bid", spot_bid},  
        {"spot_price", spot_price},  
        {"spot_ask", spot_ask},  
        {"underlying_price", underlying_price},  
        {"usd_bid", usd_bid},  
        {"usd_ask", usd_ask},  
        {"iv_bid", iv_bid},  
        {"iv_ask", iv_ask},  
        {"iv_fit", iv_fit},  
        {"iv_atmf_fit", iv_atmf_fit},  
        {"log_moneyness", log_moneyness},  
        {"log_moneyness_norm_fit", log_moneyness_norm_fit},  
        {"tv_fit", tv_fit},  
        {"delta_fit", delta_fit},  
        {"gamma_fit", gamma_fit},  
        {"theta_fit", theta_fit},  
        {"vega_fit", vega_fit},  
        {"vega_atmf_fit", vega_atmf_fit},  
        {"rho_fit", rho_fit}  
    };  
```  
  
After:  
  
```cpp  
    return boost::json::object {  
        {"time", boost::json::value_from(time)},  
        {"inst", inst},  
        {"ttm", boost::json::value_from(ttm)},  
        {"domestic_rate", boost::json::value_from(domestic_rate)},  
        {"foreign_rate", boost::json::value_from(foreign_rate)},  
        {"spot_bid", boost::json::value_from(spot_bid)},  
        {"spot_price", boost::json::value_from(spot_price)},  
        {"spot_ask", boost::json::value_from(spot_ask)},  
        {"underlying_price", boost::json::value_from(underlying_price)},  
        {"usd_bid", boost::json::value_from(usd_bid)},  
        {"usd_ask", boost::json::value_from(usd_ask)},  
        {"iv_bid", boost::json::value_from(iv_bid)},  
        {"iv_ask", boost::json::value_from(iv_ask)},  
        {"iv_fit", boost::json::value_from(iv_fit)},  
        {"iv_atmf_fit", boost::json::value_from(iv_atmf_fit)},  
        {"log_moneyness", boost::json::value_from(log_moneyness)},  
        {"log_moneyness_norm_fit", boost::json::value_from(log_moneyness_norm_fit)},  
        {"tv_fit", boost::json::value_from(tv_fit)},  
        {"delta_fit", boost::json::value_from(delta_fit)},  
        {"gamma_fit", boost::json::value_from(gamma_fit)},  
        {"theta_fit", boost::json::value_from(theta_fit)},  
        {"vega_fit", boost::json::value_from(vega_fit)},  
        {"vega_atmf_fit", boost::json::value_from(vega_atmf_fit)},  
        {"rho_fit", boost::json::value_from(rho_fit)}  
    };  
```

---

## Comment 6

> Username: grisumbras  
> Created at: 2023-07-18 15:17:16 UTC  
> Url: https://github.com/boostorg/json/issues/917#issuecomment-1640424518  

`tag_invoke` overloads _are meant_ for `value_from` and `value_to`. So the question is why would it be picked up by `initializer_list` assignment, rather than why wouldn't it be.  
  
The boilerplate is unfortunate. I can recommend you using Boost.Describe interop instead of a custom tag_invoke overload.

---

## Comment 7

> Username: steve-numeus  
> Created at: 2023-07-18 15:47:55 UTC  
> Url: https://github.com/boostorg/json/issues/917#issuecomment-1640488900  

Ok, thanks for the tip, will look for boost.describe!
