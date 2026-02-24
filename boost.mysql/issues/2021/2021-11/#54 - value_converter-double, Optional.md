# #54 - value_converter<double, Optional> [Closed]

> Username: tinyfos  
> Created at: 2021-11-12 08:33:13 UTC  
> Updated at: 2021-11-12 08:33:39 UTC  
> Closed at: 2021-11-12 08:33:39 UTC  
> Url: https://github.com/boostorg/mysql/issues/54  

template <template <class> class Optional>  
struct value_converter<double, Optional>  
{  
    static BOOST_CXX14_CONSTEXPR Optional<double> convert(  
        const value::variant_type& repr  
    ) noexcept  
    {  
        auto* val = std::variant::get_if<float>(&repr);  
        return val ? Optional<double>(*val) : Optional<double>();  
    }  
};  
  
I don't know if this is a mistake？  
  
float -----> double
