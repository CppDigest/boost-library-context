# #164 Remove --valid flag from xmllint [Merged]

> Username: danieljames  
> Created at: 2017-10-03 12:14:21 UTC  
> Updated at: 2017-10-09 10:29:00 UTC  
> Merged at: 2017-10-09 10:28:53 UTC  
> Closed at: 2017-10-09 10:28:53 UTC  
> Url: https://github.com/boostorg/boost/pull/164  

The existing call to xmllint returns an error status, the error message is obscured by the output, but it writes to stderr:  
  
    explicit-failures-markup.xml:3: validity error : Validation failed: no DTD found !  
     xsi:noNamespaceSchemaLocation="explicit-failures.xsd">  
  
According to the xmllint man page the `--valid` flag is used for validating using a DTD, and we don't have a DTD. The `--schema` flag is sufficient for validation, so I think the solution is just to remove the `--valid` flag.  
  
I'll accept this myself if no one objects, but I wanted to give people a chance to comment.

---
