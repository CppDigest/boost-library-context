# #693 - Upgrade with Content-Length should use a custom error [Closed]

> Username: vinniefalco  
> Created at: 2017-07-28 14:56:13 UTC  
> Updated at: 2017-07-29 12:36:09 UTC  
> Closed at: 2017-07-29 12:36:09 UTC  
> Url: https://github.com/boostorg/beast/issues/693  

When the parser receives an Upgrade message containing Content-Length, the error `parse_error::bad_upgrade` should be returned.  
  
The check for a body that is too large should be made after the check for the Upgrade, and after the entire header is received.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-29 12:36:09 UTC  
> Url: https://github.com/boostorg/beast/issues/693#issuecomment-318828364  

So, we are going in the opposite direction and ignoring Content-Length for 101 responses. Nothing to do here.
