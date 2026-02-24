# #43 - [Feature Request] Need containers of guaranteed actual `std::weak_ptr` [Open]

> Username: denzor200  
> Created at: 2025-01-10 23:15:27 UTC  
> Updated at: 2025-01-10 23:19:52 UTC  
> Url: https://github.com/boostorg/ptr_container/issues/43  

Let's consider a motivating example of the code that is simple semantically but it's data flow is ugly:  
```  
void move_all_to_top(std::list<std::weak_ptr<Cookie>>& child_cookies)  
{  
    for (auto it = child_cookies.begin(); it != child_cookies.end(); ) {  
        boost::shared_ptr<Cookie> cookie = it->second.lock();  
  
        if (cookie) {  
            cookie->y = 100;  
            ++it;  
        } else {  
            child_cookies.erase(it++);  
        }  
    }  
}  
```  
  
Having looked at this, I wish I had a container from which expired elements are automatically removed each time it's being accessed. Does anybody agree with me?  
  
Imagine we have `boost::weak_ptr_list` and then it becomes possible to reimplement the given example in the simpliest way:  
```  
void move_all_to_top(boost::weak_ptr_list<Cookie>& child_cookies)  
{  
    for (Cookie& cookie : child_cookies) {  
        cookie.y = 100;  
    }  
}  
```  
So, as you could see now, the semantic of that code was actually simple. And more over it becomes possible to use STL algorithms with container of weak pointers:  
```  
Cookie get_first_top(const boost::weak_ptr_list<Cookie>& child_cookies)  
{  
   const auto it = std::ranges::find_if(child_cookies, [](const Cookie& cookie) { return cookie.y > 0; });  
   if (it == child_cookies.end()) {  
      throw std::runtime_error("not found");  
   }  
   return *it;  
}  
```  
  
  
My proposal is to support containers of weak pointers on the Boost Pointer Container library side. Not only list - we also want to store weak pointers in associative containers and I suppose containers like `boost::weak_ptr_set` and `boost::weak_ptr_map` would be nice to have.

---

## Comment 1

> Username: denzor200  
> Created at: 2025-01-10 23:19:51 UTC  
> Url: https://github.com/boostorg/ptr_container/issues/43#issuecomment-2584775846  

I would also be thanksful for any information about a ready to use solution for a weak pointer container with automatic removal of expired elements(haven't found it in the Boost and other popular C++ libraries).
