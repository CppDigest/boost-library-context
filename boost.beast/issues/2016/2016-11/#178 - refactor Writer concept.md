# #178 - refactor Writer concept [Closed]

> Username: vinniefalco  
> Created at: 2016-11-09 18:12:47 UTC  
> Updated at: 2017-06-08 05:27:20 UTC  
> Closed at: 2017-06-08 05:27:20 UTC  
> Url: https://github.com/boostorg/beast/issues/178  

The **Writer** concept has some issues with it. One is that in order for `prepare` to insert the content length, it has to construct the Writer twice. Another is the complexity of the **write_function** and **resume_context**. I am considering these changes to the **Writer** concept:  
  
* Beast will only serialize message bodies whose size is known in advance  
* Remove nested type `Body::writer`  
* `chunk_encode` is now a public interface so callers can perform chunk encoding as needed  
* The **Body** concept will have this requirement for writing:  
```  
    struct Body  
    {  
        ...  
        template<class WriteFunction>  
        static  
        void  
        write(WriteFunction const& wf, value_type const& body);  
    };  
```  
  
To replicate the missing functionality of `writer`, callers can simply write the http header themselves by calling `write` with the `message_headers` then manually sending the body.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-08 05:27:20 UTC  
> Url: https://github.com/boostorg/beast/issues/178#issuecomment-307002203  

BodyWriter (was Writer) has been completely revamped, it is simplified and this issue is no longer applicable.
