# #438 - Case sensitivity when comparing field values [Closed]

> Username: cmannett85  
> Created at: 2017-06-08 09:05:11 UTC  
> Updated at: 2017-06-08 14:55:33 UTC  
> Closed at: 2017-06-08 14:55:33 UTC  
> Url: https://github.com/boostorg/beast/issues/438  

### Version of Beast  
commit a2c1117fc0c8125f88d5cfcf14e91f9963441e45  
Author: Vinnie Falco <vinnie.falco@gmail.com>  
Date:   Wed Jun 7 05:15:21 2017 -0700  
  
    Set version to 50  
  
### Issue  
In v50, `message::is_keep_alive` was removed when `header` became derived from `fields`.  This is understandable as it keeps the API cleaner - you have one set of generic methods to access all fields.  
  
So now a keep-alive check might look like this  
```  
auto it = msg_.find(beast::http::to_string(beast::http::field::connection));  
if (it->value() != beast::http::to_string(beast::http::field::keep_alive)) {  
    return;  
}  
```  
But it has got me thinking about case-sensitivity issues.  Regarding this example, the "Connection" field value is case insensitive, so the `it->value()` is "Keep-Alive" but it could be comparing against "keep-alive", in which case the conditional would evaluate incorrectly.  
  
What do the Beast contributors recommend to solve this?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-08 12:30:30 UTC  
> Url: https://github.com/boostorg/beast/issues/438#issuecomment-307088715  

_"Never underestimate the ability of users to abuse your interface in ways you hadn't thought of."_  
  
`beast::http::field::keep_alive` represents a field **name** not a value . There are no provisions for enum constants for the values (since they can be arbitrary strings).  
  
Note that the value for Connection is actually a comma separated token list. This is the BNF from rfc7230:  
```  
Connection = *( "," OWS ) connection-option *( OWS "," [ OWS connection-option ] )  
```  
  
What you want is to write something like this:  
```  
using namespace beast::http;  
if(! token_list{msg_[field::connection].contains("keep-alive"))  
  return;  
```  
  
See:  
http://vinniefalco.github.io/beast/beast/ref/http__token_list.html  
  
The reason I removed `is_keep_alive` is to reduce the requirements of the **Fields** concept. Some implementations of fields might not have the array operator or a `find` function.

---

## Comment 2

> Username: cmannett85  
> Created at: 2017-06-08 14:55:33 UTC  
> Url: https://github.com/boostorg/beast/issues/438#issuecomment-307128955  

Thanks!
