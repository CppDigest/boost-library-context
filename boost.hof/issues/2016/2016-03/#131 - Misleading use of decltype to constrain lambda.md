# #131 - [QuickStart.Tuples] Misleading use of decltype to constrain lambda? [Closed]

> Username: ldionne  
> Created at: 2016-03-11 15:11:55 UTC  
> Updated at: 2016-03-23 18:23:54 UTC  
> Closed at: 2016-03-22 23:54:23 UTC  
> Url: https://github.com/boostorg/hof/issues/131  

In the quick start, section Tuples, there's this piece of code:  
  
``` c++  
FIT_STATIC_LAMBDA_FUNCTION(print) = conditional(  
    [](const auto& x) -> decltype(std::cout << x, void())  
    {  
        std::cout << x << std::endl;  
    },  
    [](const auto& range) -> decltype(std::cout << *adl::adl_begin(range), void())  
    {  
        for(const auto& x:range) std::cout << x << std::endl;  
    },  
    [](const auto& tuple) -> decltype(for_each_tuple(tuple, identity), void())  
    {  
        return for_each_tuple(tuple, [](const auto& x)  
        {  
            std::cout << x << std::endl;  
        });  
    }  
);  
```  
  
Why do you use `decltype(for_each_tuple(...))`? If I'm not mistaken, if no other branch of the conditional is taken and the argument is not a tuple, this will cause a hard error inside of `for_each_tuple`, regardless of the fact that `decltype(for_each_tuple(...))` was used to "constrain" the lambda. So basically my impression is that this appearance of `decltype(for_each_tuple(...))` is both useless and misleading. Did I miss something?

---

## Comment 1

> Username: pfultz2  
> Created at: 2016-03-11 16:48:19 UTC  
> Url: https://github.com/boostorg/hof/issues/131#issuecomment-195447173  

Good point, so `for_each_tuple` probably should be defined as:  
  
``` cpp  
FIT_STATIC_LAMBDA_FUNCTION(for_each_tuple) = [](const auto& sequence, auto f) FIT_RETURNS(unpack(by(f))(sequence));  
```  
  
Or I should just drop the trailing decltype on the last branch. I think dropping the trailing decltype is the better option for the quick start guide, as it keeps it simple, and doesn't need the identity dance.

---

## Comment 2

> Username: ldionne  
> Created at: 2016-03-11 19:57:18 UTC  
> Url: https://github.com/boostorg/hof/issues/131#issuecomment-195522201  

Whatever feels right, I just wanted to point the issue out.

---

## Comment 3

> Username: pfultz2  
> Created at: 2016-03-11 20:20:26 UTC  
> Url: https://github.com/boostorg/hof/issues/131#issuecomment-195530980  

Yea, thanks for the feedback, I hope you have time to leave a review.

---

## Comment 4

> Username: ldionne  
> Created at: 2016-03-11 20:28:07 UTC  
> Url: https://github.com/boostorg/hof/issues/131#issuecomment-195536751  

Yes, I'm doing this right now. Sorry for being so late.
