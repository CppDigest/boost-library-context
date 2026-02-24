# #367 - Using Numbers review feedback [Closed]

> Username: vinniefalco  
> Created at: 2020-09-19 18:47:12 UTC  
> Updated at: 2020-09-24 01:34:54 UTC  
> Closed at: 2020-09-24 01:34:54 UTC  
> Url: https://github.com/boostorg/json/issues/367  

> In Using Numbers, the documentation should explain more clearly and prominently that v.as_double() does not succeed when v contains an integer, but throws an exception. This is a common user expectation. Instead of focusing on number_cast, It should advertise value_to<double>(v) as the way to obtain a double from v, converting if necessary; and similarly, value_to<int>(v) as the way to obtain an int, range-checking if necessary. number_cast should be considered legacy at this point, and not featured. I understand that it offers an error_code& overload for those who don't want exceptions, but the throwing version is entirely superseded by value_to.
