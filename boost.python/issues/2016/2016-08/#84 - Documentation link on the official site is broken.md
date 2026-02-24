# #84 - Documentation link on the official site is broken [Closed]

> Username: vasily-v-ryabov  
> Created at: 2016-08-23 09:03:31 UTC  
> Updated at: 2017-07-20 12:20:54 UTC  
> Closed at: 2017-07-20 12:20:54 UTC  
> Url: https://github.com/boostorg/python/issues/84  

On [Getting Started Guide for Windows](http://www.boost.org/doc/libs/1_61_0/more/getting_started/windows.html) go to paragraph 3 and follow the link to [Boost.Python](http://www.boost.org/doc/libs/1_61_0/libs/python/doc/building.html) that shows the following message:  
  
```  
404 Not Found  
  
File "/home/www/shared/archives/live/boost_1_61_0/libs/python/doc/building.html" not found.  
  
Unable to find file.  
```

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2016-08-23 13:08:26 UTC  
> Url: https://github.com/boostorg/python/issues/84#issuecomment-241724252  

The page containing the wrong link (http://www.boost.org/doc/libs/1_61_0/more/getting_started/windows.html) doesn't belong to Boost.Python. I'm not sure what module owns that, probably https://github.com/boostorg/website.
