# #584 - message refinement [Closed]

> Username: vinniefalco  
> Created at: 2017-07-04 01:10:49 UTC  
> Updated at: 2017-08-06 22:40:30 UTC  
> Closed at: 2017-08-06 22:40:30 UTC  
> Url: https://github.com/boostorg/beast/issues/584  

Quality of life improvements for message should allow getting and setting keep-alive, getting and setting Content-Length without requiring `basic_fields`. This will expand the **Fields** concept but the result makes the container more usable.  
  
`response_header` could have a member function `prepare_response` that takes a request and fills in the version and Connection fields based on the request:  
  
* The response version is set to the request version  
  
* The Connection field value is adjusted to reflect keep-alive or close as required by the semantics of the request's Connection field and http-version, plus an optional argument to `prepare_response` indicating if the caller wants keep-alive.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-09 14:45:04 UTC  
> Url: https://github.com/boostorg/beast/issues/584#issuecomment-313924186  

Added `message::keep_alive`

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-08-06 22:40:30 UTC  
> Url: https://github.com/boostorg/beast/issues/584#issuecomment-320537586  

This is done
