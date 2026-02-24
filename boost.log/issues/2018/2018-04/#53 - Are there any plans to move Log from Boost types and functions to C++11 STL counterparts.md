# #53 - Are there any plans to move Log from Boost types and functions to C++11 STL counterparts? [Closed]

> Username: quixoticaxis  
> Created at: 2018-04-17 18:30:00 UTC  
> Updated at: 2018-04-17 19:41:56 UTC  
> Closed at: 2018-04-17 19:34:22 UTC  
> Url: https://github.com/boostorg/log/issues/53  

For example, from `boost::shared_ptr` to `std::shared_ptr`. If not, are there any plans to generalize utility types and functions as template parameters, which would enable us to use both Boost and STL variants?

---

## Comment 1

> Username: Lastique  
> Created at: 2018-04-17 19:34:22 UTC  
> Url: https://github.com/boostorg/log/issues/53#issuecomment-382114639  

There are no concrete plans and I wouldn't want Boost.Log to have a duplicated API for Boost and the standard library components. I think Boost.Log 2.x will keep its compatibility with C++03 and thus the Boost types.  
  
I do, however, have a few ideas for a hypothetical Boost.Log 3.x, which would among other things switch to a more modern C++ version (most likely, C++17 or later). Unfortunately, this is nothing more than ideas and I don't know when I will be able to start working on it more seriously.

---

## Comment 2

> Username: quixoticaxis  
> Created at: 2018-04-17 19:41:55 UTC  
> Url: https://github.com/boostorg/log/issues/53#issuecomment-382116669  

@Lastique thank you for your response.
