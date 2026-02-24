# #121 - Serialization of boost::optional should use load_construct_data [Open]

> Username: markstijnman  
> Created at: 2018-09-28 13:55:53 UTC  
> Updated at: 2018-09-30 01:08:25 UTC  
> Url: https://github.com/boostorg/serialization/issues/121  

It currently isn't possible to serialize a boost::optional&lt;T&gt; where T doesn't have a default constructor. This used to work, but was changed in 632df7aab5e1fb3458ea53be2704c83462b2caa9. However, this is an important use case. While the workaround is easy, there is usually a good reason not to allow a default constructor, for example there isn't a reasonable default value. In other cases, it may not even be possible to add a default constructor, for example when adding serialization to 3rd party classes.   
  
I can't think for a good reason for this limitation, as the serialization framework defines the load_construct_data hook specifically for this use case. Since boost::optional can be considered a container with either 0 or 1 items, I would therefore expect it to also be serialized in a similar way to the STL container, and call load_construct_data (using use stack_construct&lt;T&gt; I assume).

---

## Comment 1

> Username: robertramey  
> Created at: 2018-09-30 01:08:25 UTC  
> Url: https://github.com/boostorg/serialization/issues/121#issuecomment-425685656  

load_construct_data was really designed to be able to load pointers.  
  
Honestly I don't remember why I suppressed this.  I'm guessing that it wasn't reliable.  In addressing bug reports, I probably concluded that  it couldn't be fixed to be guaranteed correct.  
  
If you want to fork, alter the code and test and submit a PR, I'll take a look at it
