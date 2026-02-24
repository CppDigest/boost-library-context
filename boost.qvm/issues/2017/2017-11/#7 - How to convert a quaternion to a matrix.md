# #7 - How to convert a quaternion to a matrix? [Closed]

> Username: szsdk  
> Created at: 2017-11-20 05:23:14 UTC  
> Updated at: 2018-11-12 03:13:09 UTC  
> Closed at: 2018-11-12 03:13:09 UTC  
> Url: https://github.com/boostorg/qvm/issues/7  

I find there is a function converting matrix to quaternion, but it seems that there is no function doing the other direction.

---

## Comment 1

> Username: zajo  
> Created at: 2017-11-21 05:45:54 UTC  
> Url: https://github.com/boostorg/qvm/issues/7#issuecomment-345923564  

It would appear that this is an omission on my part, yes. I can't work on  
this right now but I will take a better look and add it when I get a chance.  
  
Thanks for reporting this.  
  
On Sun, Nov 19, 2017 at 7:23 PM, Shen Zhou <notifications@github.com> wrote:  
  
> I find there is a function converting matrix to quaternion, but it seems  
> that there is no function doing the other direction.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/qvm/issues/7>, or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AAlM2sbrII089epIq2IirpepnxZZ1KG0ks5s4QzCgaJpZM4QjwZ4>  
> .  
>

---

## Comment 2

> Username: zajo  
> Created at: 2017-11-25 23:30:50 UTC  
> Url: https://github.com/boostorg/qvm/issues/7#issuecomment-346973136  

On Sun, Nov 19, 2017 at 9:23 PM, Shen Zhou <notifications@github.com> wrote:  
  
> I find there is a function converting matrix to quaternion, but it seems  
> that there is no function doing the other direction.  
>  
Actually, this is merely a documentation bug as there is a convert_to  
overload that takes a quaternion and returns a matrix. Please include  
<boost/qvm/mat_operations.hpp>, instantiate the convert_to function  
template with a compatible matrix type and pass a quaternion type as its  
argument.  
  
Emil

---

## Comment 3

> Username: szsdk  
> Created at: 2017-11-26 11:27:56 UTC  
> Url: https://github.com/boostorg/qvm/issues/7#issuecomment-347001839  

Thanks.
