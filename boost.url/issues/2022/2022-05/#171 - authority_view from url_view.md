# #171 - authority_view from url_view [Closed]

> Username: vinniefalco  
> Created at: 2022-05-17 14:06:41 UTC  
> Updated at: 2022-05-19 00:17:04 UTC  
> Closed at: 2022-05-19 00:17:04 UTC  
> Url: https://github.com/boostorg/url/issues/171  

`url_view` needs a function (,might be an existing one) that returns the authority as `authority_view`.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-05-17 15:41:30 UTC  
> Url: https://github.com/boostorg/url/issues/171#issuecomment-1129026954  

`url_view` has only the member functions [encoded_authority](https://master.url.cpp.al/url/ref/boost__urls__url_view/encoded_authority.html) and [has_authority](https://master.url.cpp.al/url/ref/boost__urls__url_view/has_authority.html) at the moment. So should this just be a new member/convenience function such as:  
  
```cpp  
authority_view  
authority()  
{  
    return parse_authority( encoded_authority() ).value();  
}  
```  
  
?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-05-17 16:11:44 UTC  
> Updated at: 2022-05-17 16:16:54 UTC  
> Url: https://github.com/boostorg/url/issues/171#issuecomment-1129059971  

We don't want to parse again. So `authority_view` would need a private function and befriend `url_view` so that the url view can secretly construct the authority view and bypass parsing.  
  
And the function should be `const noexcept`
