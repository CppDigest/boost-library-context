# #579 - Add Security Considerations section (HTTP) [Open]

> Username: vinniefalco  
> Created at: 2017-07-03 19:58:56 UTC  
> Updated at: 2018-12-21 15:08:50 UTC  
> Url: https://github.com/boostorg/beast/issues/579  

This section should discuss the security considerations for HTTP users.  
  
* Slow loris mitigation  
* Header smuggling mitigation  
  
>Also stuff like buffer overruns or integer overflows are common they are relatively simple to check using unit tests. The bigger problem is the stuff you do not check or assume already validated. Also Beast is too low level library to worry about CSRF or XSS. It still provides virtually no validation of what is going on automatically. No timeouts, no content-length till few days ago. It may give some sort of false security to user that may not be aware of wide range of potential issues around.  
  
and  
  
> This kind of design should provide a huge warning notice to all end users - all we do is parse protocol - the rest is up to you (which is 99% of all security stuff related to WWW)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-04 03:42:08 UTC  
> Url: https://github.com/boostorg/beast/issues/579#issuecomment-312773215  

Could name it "Validation and Security", and also explain that the library does not do any validation of inputs. So if the caller sets a bogus request-target, the resulting serialized HTTP message will be malformed.
