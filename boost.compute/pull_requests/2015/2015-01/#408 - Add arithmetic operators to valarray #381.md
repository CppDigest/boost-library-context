# #408 Add arithmetic operators to valarray #381 [Closed]

> Username: archangel4  
> Created at: 2015-01-07 11:46:55 UTC  
> Updated at: 2017-03-24 17:09:32 UTC  
> Closed at: 2017-03-24 17:09:32 UTC  
> Url: https://github.com/boostorg/compute/pull/408  

overloaded + operator , let me know if this works , ill overload - operator too .  
also please give me some feedback , this is my first pull request ever and first time contributing to an open source project .  
thanks

---

## Comment 1

> Username: kylelutz  
> Created_at: 2015-01-08 03:54:31 UTC  
> Url: https://github.com/boostorg/compute/pull/408#issuecomment-69132642  

So this code doesn't compile, see my comment on issue #381 for some example code.  
  
Also, take a look at the [Developer's Guide](https://github.com/kylelutz/compute/wiki/Developer%27s-Guide) and [CONTRIBUTING.md](https://github.com/kylelutz/compute/blob/master/CONTRIBUTING.md) file for some instructions on developing and submitting your patch. Thanks!

---

## Comment 2

> Username: archangel4  
> Created_at: 2015-01-08 11:53:02 UTC  
> Url: https://github.com/boostorg/compute/pull/408#issuecomment-69169868  

I can't seem to get the code to compile , help.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2015-01-08 16:36:58 UTC  
> Url: https://github.com/boostorg/compute/pull/408#issuecomment-69206401  

Did you try the code I provided in issue #381? That code should compile fine. What errors are you getting?

---

## Comment 4

> Username: archangel4  
> Created_at: 2015-01-08 16:38:46 UTC  
> Url: https://github.com/boostorg/compute/pull/408#issuecomment-69206730  

Yes I used that exact same code , the Travis ci build fails every time.  
On 08-Jan-2015 10:07 pm, "Kyle Lutz" notifications@github.com wrote:  
  
> Did you try the code I provided in issue #381  
> https://github.com/kylelutz/compute/issues/381? That code should  
> compile fine. What errors are you getting?  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/kylelutz/compute/pull/408#issuecomment-69206401.

---

## Comment 5

> Username: kylelutz  
> Created_at: 2015-01-08 16:43:47 UTC  
> Url: https://github.com/boostorg/compute/pull/408#issuecomment-69207625  

Oops, I forgot to add the result range for `transform()`, try this:  
  
```  
valarray<T> operator+(const valarray<T> &other) const  
{  
    const context &context = m_buffer.get_context();  
    command_queue queue(context, context.get_device());  
    valarray<T> result(size(), context);  
    transform(begin(), end(), other.begin(), result.begin(), plus<T>(), queue);  
    queue.finish();  
    return result;  
}  
```

---

## Comment 6

> Username: roshanrags  
> Created_at: 2015-01-25 13:47:11 UTC  
> Url: https://github.com/boostorg/compute/pull/408#discussion_r23504285  

Extra '+' symbol seems to be the problem... Same thing in a few more lines that follow...

---
