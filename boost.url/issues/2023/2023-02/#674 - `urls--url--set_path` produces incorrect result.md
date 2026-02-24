# #674 - `urls::url::set_path` produces incorrect result. [Closed]

> Username: Ramirisu  
> Created at: 2023-02-01 17:33:47 UTC  
> Updated at: 2023-03-08 04:12:10 UTC  
> Closed at: 2023-03-08 04:12:10 UTC  
> Url: https://github.com/boostorg/url/issues/674  

Version: `1.81.0`, `develop f8022af5f02b26655004666727b25b591809ce74`  
  
#### ISSUE 1  
  
`urls::url::set_path` with `"/"`  
  
```cpp  
  urls::url url;  
  url.set_path("/");  
  CHECK_EQ(url.buffer(), "/");  
  // ERROR: CHECK_EQ( url.buffer(), "/" ) is NOT correct!  
  // values : CHECK_EQ( /./, / )  
  url.normalize();  
  CHECK_EQ(url.buffer(), "/");  
  // PASS!  
```  
  
#### ISSUE 2  
  
Accessing url components after `urls::url::normalize`.  
  
```cpp  
  urls::url url;  
  url.set_path("/");  
  CHECK_EQ(url.buffer(), "/");  
  // ERROR: CHECK_EQ( url.buffer(), "/" ) is NOT correct!  
  // values : CHECK_EQ( /./, / )  
  url.normalize();  
  CHECK_EQ(url.buffer(), "/");  
  // PASS!  
  
  CHECK_EQ(url.encoded_target(), "/");  
  // BOOST_ASSERT failed!  
```  
It seems that the `pi_->decoded_[id_path] ` returns incorrect length 3 (should be 1 ?)  
  
```cpp  
pct_string_view  
url_view_base::  
encoded_target() const noexcept  
{  
  auto n =  
            pi_->decoded_[id_path] +  // return 3; should be 1 ?  
            pi_->decoded_[id_query];  
  BOOST_ASSERT(  // trigger assertion failed   
```  
  
Edit: Add boost version and issue 2.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-02-01 17:39:40 UTC  
> Url: https://github.com/boostorg/url/issues/674#issuecomment-1412459380  

I thought this was fixed. Have you tried the tip of *develop* ?

---

## Comment 2

> Username: Ramirisu  
> Created at: 2023-02-01 18:02:58 UTC  
> Url: https://github.com/boostorg/url/issues/674#issuecomment-1412494414  

I tried to pull the `develop` branch and the issue persists.  
  
```  
-- Downloading https://api.github.com/repos/boostorg/url/git/refs/heads/develop -> boostorg-url-develop.version...  
-- Downloading https://github.com/boostorg/url/archive/f8022af5f02b26655004666727b25b591809ce74.tar.gz -> boostorg-url-f8022af5f02b26655004666727b25b591809ce74.tar.gz...  
-- Extracting source ~/vcpkg/downloads/boostorg-url-f8022af5f02b26655004666727b25b591809ce74.tar.gz  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-02-01 18:30:13 UTC  
> Url: https://github.com/boostorg/url/issues/674#issuecomment-1412529816  

Yes I can reproduce these issues, thanks for the concise report!
