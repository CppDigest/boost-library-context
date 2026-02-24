# #1088 - Minor change to example comment [Closed]

> Username: carolinebeltran  
> Created at: 2018-04-03 18:18:34 UTC  
> Updated at: 2018-04-04 01:18:46 UTC  
> Closed at: 2018-04-04 01:18:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1088  

This is so insignificant but I thought that I'd mention it anyway.  I believe that the comment should read 'relative', not 'absolute'.  
  
```  
// Request path must be absolute and not contain "..".  
if( req.target().empty() ||  
    req.target()[0] != '/' ||  
```  
The last line of code validates a 'relative' path and fails when an 'absolute' path is received.  
  
Relative path:  
/whatever.html  
  
Absolute path:  
http://www.example.com/whatever.html

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-04-03 21:41:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1088#issuecomment-378408609  

The path refers to the filesystem path not the request target. Relative paths are those which either depend on the current directory or have ".." in one or more places. Such paths could be used to escape the configured html root directory and access files to which clients should not have permission.

---

## Comment 2

> Username: carolinebeltran  
> Created at: 2018-04-04 01:18:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1088#issuecomment-378448920  

Thank you for the clarification!
