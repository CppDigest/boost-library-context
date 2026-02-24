# #216 - [1.79] compilation error in  boost::container::list with c++0x or higher [Closed]

> Username: youngwolf-project  
> Created at: 2022-05-07 07:23:27 UTC  
> Updated at: 2022-06-25 18:32:18 UTC  
> Closed at: 2022-06-25 18:32:18 UTC  
> Url: https://github.com/boostorg/container/issues/216  

example code:  
  
boost::container::list<std::pair<const char*, size_t> > l;  
l.emplace_back(std::make_pair<const char*, size_t>(nullptr, 0));

---

## Comment 1

> Username: igaztanaga  
> Created at: 2022-06-25 18:32:18 UTC  
> Url: https://github.com/boostorg/container/issues/216#issuecomment-1166340263  

Thanks for the report, fixed recently when solving #222.
