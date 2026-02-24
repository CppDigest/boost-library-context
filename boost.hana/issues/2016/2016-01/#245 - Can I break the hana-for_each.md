# #245 - Can I break the hana:for_each? [Closed]

> Username: harrywong  
> Created at: 2016-01-26 12:50:37 UTC  
> Updated at: 2016-01-26 15:26:34 UTC  
> Closed at: 2016-01-26 13:48:00 UTC  
> Url: https://github.com/boostorg/hana/issues/245  

Hi, can I find any way to break out of hana:for_each? I tried goto but it's not valid, I think it's the lambda-things.  
Thanks!

---

## Comment 1

> Username: ldionne  
> Created at: 2016-01-26 13:47:51 UTC  
> Url: https://github.com/boostorg/hana/issues/245#issuecomment-175021817  

No, you can't break out of a `hana::for_each`, just like you can't break out of `std::for_each`. Both are higher order functions accepting the body of the loop, and there is no way I know of to break from that in C++.

---

## Comment 2

> Username: pfultz2  
> Created at: 2016-01-26 14:53:51 UTC  
> Url: https://github.com/boostorg/hana/issues/245#issuecomment-175057336  

Well, the lambda could return a value to signify whether it should continue or stop:  
  
``` cpp  
for_each(seq, [](auto x)  
{  
    if(x)  
    {  
        run(x);  
        return continue_;  
    }  
    else return break_;  
};  
```  
  
But it might be better to use `take_while` then `for_each`.

---

## Comment 3

> Username: ldionne  
> Created at: 2016-01-26 15:17:03 UTC  
> Url: https://github.com/boostorg/hana/issues/245#issuecomment-175070291  

The problem with this is that it would make the algorithm much less compile-time efficient. I think the only way to implement it while allowing one to break is to use a recursive function, and that's a no-no.  
  
I think the proper way to go would be to use `take_while` and then `for_each`, as you suggest. However, `take_while` is going to make a copy of the sequence. If we had a `take_while` sort of view, we could create a `take_while` view and then use `for_each` on it without additional runtime overhead.

---

## Comment 4

> Username: ldionne  
> Created at: 2016-01-26 15:17:43 UTC  
> Url: https://github.com/boostorg/hana/issues/245#issuecomment-175070827  

@pfultz2 Do you know of any way to implement your suggestion while still retaining the near optimal compile-times of a simple `for_each`?

---

## Comment 5

> Username: vendethiel  
> Created at: 2016-01-26 15:25:06 UTC  
> Url: https://github.com/boostorg/hana/issues/245#issuecomment-175075206  

``` c++  
bool continue = true;  
for_each(seq, [](auto x)  
{  
  if (!continue) return;  
  if (run(x)) continue = false;  
};  
```  
  
:P

---

## Comment 6

> Username: pfultz2  
> Created at: 2016-01-26 15:26:33 UTC  
> Url: https://github.com/boostorg/hana/issues/245#issuecomment-175076804  

> Do you know of any way to implement your suggestion while still retaining the near optimal compile-times of a simple for_each?  
  
I don't, which is why I suggested `take_while` as a more efficient route.
