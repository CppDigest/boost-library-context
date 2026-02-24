# #62 Improve initialization scalability [Closed]

> Username: jloy  
> Created at: 2016-03-12 00:50:51 UTC  
> Updated at: 2020-01-28 18:37:41 UTC  
> Closed at: 2020-01-28 18:37:41 UTC  
> Url: https://github.com/boostorg/python/pull/62  

I've run into a situation where the time spent initializing Boost Python inheritance structures is having a significant impact on application startup times.  The application in question winds up making thousands of registrations, across dozens of libraries. Profiling turned up a couple of hot spots: re-initializing the `smart_graph` structures multiple times and `num_edges` running in linear time for some versions of libstdc++.

---
