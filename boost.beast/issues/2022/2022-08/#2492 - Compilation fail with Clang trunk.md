# #2492 - Compilation fail with Clang trunk [Closed]

> Username: vient  
> Created at: 2022-08-02 13:28:29 UTC  
> Updated at: 2022-08-09 18:23:46 UTC  
> Closed at: 2022-08-09 18:23:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2492  

### Version of Beast  
  
The one included in Boost 1.79.0  
  
### Steps necessary to reproduce the problem  
  
Minified version https://godbolt.org/z/WjbGacE7o  
  
I can't understand on which side the problem is so I'll report it to LLVM too, maybe it's a bug in their trunk since latest release works fine. I'll report back if that's the case.

---

## Comment 1

> Username: segeter  
> Created at: 2022-08-03 06:37:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2492#issuecomment-1203544290  

I had the same problem today:  
![image](https://user-images.githubusercontent.com/52019351/182540527-75338243-53b3-4300-9db5-2647ba62345d.png)  
![image](https://user-images.githubusercontent.com/52019351/182540670-f34eef30-3f8d-4b4e-a281-1a0644a56c1e.png)

---

## Comment 2

> Username: vient  
> Created at: 2022-08-03 17:48:41 UTC  
> Updated at: 2022-08-03 17:48:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2492#issuecomment-1204285394  

I tried to make smaller reproducer and noted that the problem goes away if you add an instantiation of the type which is said to be incomplete, or copy this static_assert into your code.  
      
So like this  
```cpp  
using Type = boost::beast::detail::buffers_ref<boost::beast::buffers_cat_view<boost::asio::const_buffer, boost::asio::const_buffer, boost::asio::const_buffer, boost::beast::http::basic_fields<std::allocator<char>>::writer::field_range, boost::beast::http::chunk_crlf>>;  
boost::asio::is_const_buffer_sequence<Type> unused;  
```  
or like this  
```cpp  
static_assert(boost::beast::net::is_const_buffer_sequence<Type>::value);  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-08-03 18:26:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2492#issuecomment-1204326223  

That is VERY helpful !

---

## Comment 4

> Username: vient  
> Created at: 2022-08-09 13:46:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2492#issuecomment-1209405115  

Looks like the problem is in Clang, seems to be fixed in main https://github.com/llvm/llvm-project/issues/56878.

---

## Comment 5

> Username: vient  
> Created at: 2022-08-09 18:23:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2492#issuecomment-1209723117  

Closing this one since the root cause is Clang bug.
