# #6 Allow construction from ranges of intervals [Closed]

> Username: muggenhor  
> Created at: 2016-09-06 13:50:12 UTC  
> Updated at: 2021-04-11 19:27:19 UTC  
> Closed at: 2021-04-11 19:27:19 UTC  
> Url: https://github.com/boostorg/icl/pull/6  

This allows constructing interval_map and interval_set as const  
variables without having to resort to constructor functions. E.g.  
boost::assign::list_of can now be used to fill them.

---

## Comment 1

> Username: jofaber  
> Created_at: 2021-03-08 20:27:03 UTC  
> Url: https://github.com/boostorg/icl/pull/6#issuecomment-793052517  

There are already methods to populate icl containers via std::copy or std::transform using `icl::inserter` and `icl::adder` see examples https://www.boost.org/doc/libs/1_75_0/libs/icl/doc/html/boost_icl/examples/std_copy.html and https://www.boost.org/doc/libs/1_75_0/libs/icl/doc/html/boost_icl/examples/std_transform.html  
  
I'd like to keep the containers' interface minimal and rather combine the available functions with algorithms.

---

## Comment 2

> Username: muggenhor  
> Created_at: 2021-03-09 11:27:47 UTC  
> Url: https://github.com/boostorg/icl/pull/6#issuecomment-793748784  

Unfortunately I don't recall what the use case for this was anymore.  
  
I was definitely aware of `std::copy` and `std::transform` at the time and am thus sure they didn't suit my purpose. I don't recall anything else beyond that. But I suspect that it was more convenient to match the iterator construction interfaces of standard containers.  
  
Feel free to close this.

---
