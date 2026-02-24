# #2172 - Undefined behavior in parser [Closed]

> Username: vinniefalco  
> Created at: 2021-02-25 18:46:06 UTC  
> Updated at: 2021-03-03 20:47:10 UTC  
> Closed at: 2021-03-03 20:47:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2172  

Currently the parser exhibits undefined behavior when data is provided but the parser is already "complete." This needs to be fixed. One option is simply to throw an exception. We should check to see if there are any other ways to produce undefined behavior.

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-03-03 13:44:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2172#issuecomment-789723734  

Would it be reasonable to ASSERT this precondition in a debug build and throw in a release build?

---

## Comment 2

> Username: madmongo1  
> Created at: 2021-03-03 13:45:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2172#issuecomment-789724534  

since we already have this code  
```  
        // If this assert goes off, it means you tried to re-use a  
        // parser after it was done reading a message. This is not  
        // allowed, you need to create a new parser for each message.  
        // The easiest way to do that is to store the parser in  
        // an optional object.  
  
        BOOST_ASSERT(! used_);  
        if(used_)  
        {  
            ec = error::stale_parser;  
            return;  
        }  
```

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-03-03 18:01:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2172#issuecomment-789939927  

After some lively discussion in slack, there is not yet complete consensus but there is a 2:1 majority in favour fo the above solution.  
  
I will make this change and re-engage with @genossefloyd to assess impact.  
  
I am reluctant to actuallyt remove the assertion as initially requested as this is clearly an irrecoverable logic error that should be caught during development/testing and never reach production.
