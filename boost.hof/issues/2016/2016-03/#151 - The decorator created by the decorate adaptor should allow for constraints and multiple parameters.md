# #151 - The decorator created by the decorate adaptor should allow for constraints and multiple parameters [Open]

> Username: pfultz2  
> Created at: 2016-03-15 20:17:16 UTC  
> Updated at: 2016-03-17 07:36:04 UTC  
> Url: https://github.com/boostorg/hof/issues/151  

One way to do this is to have a second function passed to `decorate`, which will construct or pack the data. It can also be used to constrain the decorator.

---

## Comment 1

> Username: viboes  
> Created at: 2016-03-15 20:58:48 UTC  
> Url: https://github.com/boostorg/hof/issues/151#issuecomment-197016791  

Can you show an example of the need?

---

## Comment 2

> Username: pfultz2  
> Created at: 2016-03-15 21:38:13 UTC  
> Url: https://github.com/boostorg/hof/issues/151#issuecomment-197032240  

For example, the `capture` decorator can't be implemented using `fit::decorate`. However, adding the second parameter to `decorate`, `capture` could be implements like this:  
  
``` cpp  
auto capture = decorate([](auto&& p, auto&& f, auto&&... xs)  
{  
    return pack_join(p, pack(xs...))(f);  
}, pack);  
```  
  
And this could be useful when the user defines their own decorators, as well.  
  
Another example is the log example. It requires a string parameter, however, if you pass it something that is not convertible to string, the error won't happen until the function is invoked:  
  
``` cpp  
std::vector<char> v;  
auto logged_sum = log(v)(sum); // v is not valid, but there is no error here  
auto x = logged_sum(1, 2); // Compile error here, even though the error should be in the first line  
```  
  
Using the second parameter, we can force it to always be a string:  
  
``` cpp  
auto log = decorate(log_f(), construct<std::string>());  
auto logged_sum = log(v)(sum); // Compile error here because v is not convertible to string  
```

---

## Comment 3

> Username: viboes  
> Created at: 2016-03-15 22:40:14 UTC  
> Url: https://github.com/boostorg/hof/issues/151#issuecomment-197055775  

I don't believe this is a good use case. It seems more than the decorate function either doesn't do enough or you want it to do too much things.  
  
BTW, why we don't catch the error in line?  
  
```  
auto logged_sum = log(v)(sum); // v is not valid, but there is no error here  
```

---

## Comment 4

> Username: pfultz2  
> Created at: 2016-03-15 22:47:31 UTC  
> Url: https://github.com/boostorg/hof/issues/151#issuecomment-197057470  

> I don't believe this is a good use case. It seems more than the decorate function either doesn't do enough or you want it to do too much things.  
  
I don't see it as too much, and it is a simple addition to `fit::decorate` to make it much more capable.  
  
> BTW, why we don't catch the error in line?  
  
How could we? We can't know anything until we invoke the function.
