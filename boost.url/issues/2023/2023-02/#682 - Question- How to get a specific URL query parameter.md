# #682 - Question: How to get a specific URL query parameter [Closed]

> Username: av4625  
> Created at: 2023-02-09 21:34:48 UTC  
> Updated at: 2023-02-10 21:28:19 UTC  
> Closed at: 2023-02-10 21:28:19 UTC  
> Url: https://github.com/boostorg/url/issues/682  

Hi,  
  
I wanted to get a specific query parameter and I used `find` to see if it existed and then wanted to return the `value`. But I noticed that `->` has been deleted from the iterator so I can't do `param->value`, but I can do `(*param).value`.  
  
Here is the sort of function that I want:  
```  
std::string request_impl::get_query_param(const std::string& key) const  
{  
    const boost::urls::url_view url(request_.target());  
  
    const auto param{url.params().find(key)};  
  
    if (param != url.params().end())  
    {  
        // -> is deleted  
        return (*param).value;  
    }  
  
    throw std::invalid_argument(key + " does not exist as a query parameter");  
}  
```  
  
Is what I have the "right" way to do it or is there a better way that I'm missing?  
  
What is the reason for deleting `->` but allowing `(*param).value`?  
  
Thanks!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-02-09 23:20:50 UTC  
> Updated at: 2023-02-09 23:21:40 UTC  
> Url: https://github.com/boostorg/url/issues/682#issuecomment-1424969644  

Every time you invoke `operator*` or `operator->` it applies percent-decoding to produce the value. The idiomatic usage is:  
```  
auto const qp( *it );  
  
// access qp.key and qp.value  
```  
  
This way the percent-decoding is only applied once, and you can access the key and value many times. Your way is okay too, because you are only applying the percent-decoding once. `operator->` is omitted as a gentle reminder of the cost of dereferencing.

---

## Comment 2

> Username: av4625  
> Created at: 2023-02-10 21:28:15 UTC  
> Url: https://github.com/boostorg/url/issues/682#issuecomment-1426366568  

Thanks for the reply!
