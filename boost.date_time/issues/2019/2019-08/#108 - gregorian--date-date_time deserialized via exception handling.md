# #108 - gregorian::date/date_time deserialized via exception handling [Open]

> Username: Fritz1729  
> Created at: 2019-08-16 12:18:35 UTC  
> Updated at: 2019-08-16 12:18:35 UTC  
> Url: https://github.com/boostorg/date_time/issues/108  

The deserialization of boost::gregorian::date in greg_serialize.hpp relies on exception handling ("catch(bad_lexical_cast&")  for deserializing "special" values, which causes unnecessary delays in the typical case of deserializing uninitialized datetime values.
