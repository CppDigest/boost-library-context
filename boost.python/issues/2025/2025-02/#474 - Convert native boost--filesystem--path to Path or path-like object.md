# #474 - Convert native boost::filesystem::path to Path or path-like object [Open]

> Username: o01eg  
> Created at: 2025-02-16 19:11:38 UTC  
> Updated at: 2025-02-16 19:11:38 UTC  
> Url: https://github.com/boostorg/python/issues/474  

It would be helpful for dealing with path on windows or unix that aren't correctly represented as valid string. Instead of trying to convert them to string use OS-dependent conversion to Path ot path-like object.
