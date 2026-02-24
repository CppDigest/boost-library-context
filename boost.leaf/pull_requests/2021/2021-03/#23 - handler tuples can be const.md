# #23 handler tuples can be const [Merged]

> Username: ProfDiesel  
> Created at: 2021-03-25 16:48:52 UTC  
> Updated at: 2021-07-09 18:16:51 UTC  
> Merged at: 2021-03-26 05:00:47 UTC  
> Closed at: 2021-03-26 05:00:47 UTC  
> Url: https://github.com/boostorg/leaf/pull/23  

So you can   
```C++  
const auto handlers = std::tuple {[](const std::error_code &ec) { ... },  
                                  [](const boost::leaf::error_info &ei) { ... }};  
  
boost::leaf::try_handle_all([]() {...}, handlers);  
```

---

## Comment 1

> Username: zajo  
> Created_at: 2021-03-26 05:00:53 UTC  
> Url: https://github.com/boostorg/leaf/pull/23#issuecomment-807936093  

Thank you!

---
