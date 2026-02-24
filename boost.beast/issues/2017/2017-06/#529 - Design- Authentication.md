# #529 - Design: Authentication [Closed]

> Username: vinniefalco  
> Created at: 2017-06-22 20:35:33 UTC  
> Updated at: 2017-07-04 01:50:47 UTC  
> Closed at: 2017-07-04 01:50:47 UTC  
> Url: https://github.com/boostorg/beast/issues/529  

The question has been raised, what would an library that handles authentication look like? Here's a start  
```  
/** Returns `boost::indeterminate` if a response indicates that authentication is needed  
  
    @return `false` if the response does not indicate 401 Unauthorized. `true` if the  
    response indicates 401 Unauthorized and no WWW-Authentication field is present.  
    Otherwise, the function returns `boost::indeterminate`.  
*/  
template<class Body, class Fields>  
boost::tribool  
is_unauthorized(response<Body, Fields> const& res);  
```  
  
When the client calls this function and it returns `true` then this function can be used to fill out a structure with information:  
```  
/** Fill in a `challenge_info` structure from the authentication requirements of a response  
*/  
template<class Body, class Fields>  
std::expected(challenge_info, std::error_code)  
get_challenge(response<Body, Fields> const& res);  
```

---

## Comment 1

> Username: octopus-prime  
> Created at: 2017-06-22 21:28:45 UTC  
> Updated at: 2017-06-22 21:33:14 UTC  
> Url: https://github.com/boostorg/beast/issues/529#issuecomment-310507878  

Or something like this:  
```  
template<class Derived>  
class authentication_handler  
{  
public:  
  challenge_response  
  on_challenge(challenge_request req);  
}  
```  
called if status 401 and header field "WWW-Authentication: ..."  
  
So somebody could implement this interface, if he knows to handle gssapi c-hell :-)  
  
But who calls this interface? Who listens for the status 401 and header field "WWW-Authentication: ..."?  
And who inserts the challenge_response into the header field "Authorization: ..." and resends the request?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-22 23:03:59 UTC  
> Url: https://github.com/boostorg/beast/issues/529#issuecomment-310525976  

>But who calls this interface?  
  
Whoever is implementing a client!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-07-04 01:50:47 UTC  
> Url: https://github.com/boostorg/beast/issues/529#issuecomment-312761500  

This is outside of the scope of Beast for now, closing
