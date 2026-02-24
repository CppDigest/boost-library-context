# #128 - Default to 64 bit builds on 64 bit hosts. [Closed]

> Username: grafikrobot  
> Created at: 2016-05-19 20:50:38 UTC  
> Updated at: 2020-09-11 19:14:12 UTC  
> Closed at: 2020-09-11 19:14:12 UTC  
> Url: https://github.com/boostorg/build/issues/128  

It would be more convenient for users if b2 defaulted to address-model=64 when hosted on a 64 bit machine.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-05-19 21:12:58 UTC  
> Url: https://github.com/boostorg/build/issues/128#issuecomment-220453797  

Is there a way to set the default, maybe in the current user's **site-config.jam**?

---

## Comment 2

> Username: OlafvdSpek  
> Created at: 2017-04-10 12:54:20 UTC  
> Url: https://github.com/boostorg/build/issues/128#issuecomment-292940673  

Why would that be more convenient?   
On Windows x64 I only use the 32-bit libs..

---

## Comment 3

> Username: vprus  
> Created at: 2017-04-10 12:56:10 UTC  
> Url: https://github.com/boostorg/build/issues/128#issuecomment-292941110  

The only way to know for sure is to poll users; my personal guess is that  
most folks have 64-bit systems and build 64-bit code.  
  
On Mon, Apr 10, 2017 at 3:54 PM Olaf van der Spek <notifications@github.com>  
wrote:  
  
> Why would that be more convenient?  
> On Windows x64 I only use the 32-bit libs..  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
>  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/build/issues/128#issuecomment-292940673>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AANye19h4hQXtPy4l6btDef8Hz1_644dks5ruiZ8gaJpZM4Iip_I>  
> .  
>  
--   
Vladimir Prus  
http://vladimirprus.com

---

## Comment 4

> Username: OlafvdSpek  
> Created at: 2017-04-10 12:57:50 UTC  
> Updated at: 2017-04-10 12:58:13 UTC  
> Url: https://github.com/boostorg/build/issues/128#issuecomment-292941503  

I think it depends on the platform. GCC for example defaults to 64-bit code on 64-bit platforms (AFAIK).  
  
Have there been requests for this feature?
