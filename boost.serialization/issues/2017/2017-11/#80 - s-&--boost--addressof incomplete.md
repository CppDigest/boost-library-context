# #80 - s/&*/boost::addressof incomplete [Closed]

> Username: joaquintides  
> Created at: 2017-11-23 20:51:04 UTC  
> Updated at: 2017-11-29 17:29:44 UTC  
> Closed at: 2017-11-29 17:29:44 UTC  
> Url: https://github.com/boostorg/serialization/issues/80  

Hi Robert,  
  
I see you've replaced occurrences of raw `&*t` with `boost::addressof(t)`, presumably to cope with types whose `operator&` is overloaded. This replacement is still not done at  
  
https://github.com/boostorg/serialization/blob/8378c75f32b87c2ef8acf74b5ba6ec4dc2a78648/include/boost/serialization/collections_save_imp.hpp#L63  
  
(at least). Thank you,

---

## Comment 1

> Username: alfC  
> Created at: 2017-11-23 23:18:34 UTC  
> Url: https://github.com/boostorg/serialization/issues/80#issuecomment-346708373  

Yes, there at least three places that need that replacement. See here  
https://github.com/boostorg/serialization/issues/72  
  
On Nov 23, 2017 17:51, "joaquintides" <notifications@github.com> wrote:  
  
Hi Robert,  
  
I see you've replaced occurrences of raw &*t with boost::addressof(t),  
presumably to cope with types whose operator& is overloaded. This  
replacement is still not done at  
  
https://github.com/boostorg/serialization/blob/  
8378c75f32b87c2ef8acf74b5ba6ec4dc2a78648/include/boost/  
serialization/collections_save_imp.hpp#L63  
  
(at least). Thank you,  
  
—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub  
<https://github.com/boostorg/serialization/issues/80>, or mute the thread  
<https://github.com/notifications/unsubscribe-auth/ACMfYKKXoCeAWjzcE8zmrMXgM2Q7fRl1ks5s5dq5gaJpZM4QpLCk>  
.
