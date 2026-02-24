# #115 - Dedicated adaptor fetching data from an object member [Open]

> Username: trueqbit  
> Created at: 2020-10-23 13:36:17 UTC  
> Updated at: 2020-10-23 13:42:44 UTC  
> Url: https://github.com/boostorg/range/issues/115  

In order to make code even more easily readable, I started encapsulating the creation of a "transform" range adaptor that accesses a member variable:  
  
    inline constexpr auto fetch = [](auto memberPtr)  
    {  
        return boost::adaptors::transformed(std::bind(memberPtr, std::placeholders::_1));  
    };  
      
    // example  
    sort(c | fetch(&Object::name));  
  
This solves the boiler-plate code required otherwise:  
  
    sort(c | transformed(std::bind(&Object::name, std::placeholders::_1)));  
  
Do you think this is a generic enough pattern worthwhile to be added to "boost range"?
