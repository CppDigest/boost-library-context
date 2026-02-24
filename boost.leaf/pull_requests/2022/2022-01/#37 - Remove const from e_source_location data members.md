# #37 Remove const from e_source_location data members [Merged]

> Username: kammce  
> Created at: 2022-01-02 03:18:18 UTC  
> Updated at: 2022-01-02 12:30:36 UTC  
> Merged at: 2022-01-02 07:25:11 UTC  
> Closed at: 2022-01-02 07:25:11 UTC  
> Url: https://github.com/boostorg/leaf/pull/37  

Doing so will allow copying of e_source_location objects. This is  
especially handy if a developer wants to make a fixed sized array  
of e_source_location objects and update the elements for purposes of  
creating a stack trace.

---
