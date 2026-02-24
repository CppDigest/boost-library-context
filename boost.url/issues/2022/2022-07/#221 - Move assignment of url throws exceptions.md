# #221 - Move assignment of url throws exceptions [Closed]

> Username: akrzemi1  
> Created at: 2022-07-06 15:08:20 UTC  
> Updated at: 2022-08-05 01:15:06 UTC  
> Closed at: 2022-08-05 01:15:06 UTC  
> Url: https://github.com/boostorg/url/issues/221  

The docs for `url`'s move assignment say that it throws nothing. ([See here](https://master.url.cpp.al/url/ref/boost__urls__url/operator_eq_/overload1.html).)  
  
Yet, the move assignment throws an exception in this function:  
  
```c++  
void fill(urls::url& url)  
{  
  urls::url new_url = urls::parse_uri("https://www.gmail.com/my/path").value(); // ok   
  url = std::move(new_url); // throws!  
}  
```  
  
The exception is of the type `invalid_argument` and is caused by another fishy inheritance in the library: `static_url` derives from `url` but does not adhere to `url`'s contract. The calling function looks like this:  
  
```c++  
int main()  
{     
    urls::static_url<10> url;   
    fill(url);  
}  
```  
  
If the goal was to reuse some implementation from `url` in `static_url` then I suggest to either use composition or to introduce another class `url_base` that derives from `url_view` and is used as a base class both for `url` and `static_url`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-07-06 17:12:06 UTC  
> Url: https://github.com/boostorg/url/issues/221#issuecomment-1176477418  

Unlike the other report, this is an actual violation :) Yes we need `url_base` and the refactoring that follows.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-08-03 22:33:17 UTC  
> Url: https://github.com/boostorg/url/issues/221#issuecomment-1204550586  

> fishy inheritance  
  
LOL...
