# #200 - Trojan warning [Closed]

> Username: andreaskoelsch  
> Created at: 2017-05-24 14:41:56 UTC  
> Updated at: 2017-05-25 01:53:00 UTC  
> Closed at: 2017-05-25 01:53:00 UTC  
> Url: https://github.com/boostorg/build/issues/200  

Hi,  
I just wanted to build Boost with  
```  
bootstrap  
.\b2  
```  
But when I tried to run b2, Kaspersky interrupts and deletes the file. The same happens, if I try to install Boost.Build.  
  
Is there any way to make this file non-infected?

---

## Comment 1

> Username: grafikrobot  
> Created at: 2017-05-24 14:59:30 UTC  
> Url: https://github.com/boostorg/build/issues/200#issuecomment-303750971  

This is not the most appropriate venue for this discussion. You should really try posting to the Boost user email list. But some quick questions.. Did you verify that the Boost archive you downloaded is valid? There are hashes and public-keys you can use to verify. What version of Boost are you trying to build? Have you checked your anti-virus options to see why it thinks the b2 executable is problematic? Have you contacted your AV support channel for help?

---

## Comment 2

> Username: vprus  
> Created at: 2017-05-24 16:46:06 UTC  
> Url: https://github.com/boostorg/build/issues/200#issuecomment-303782970  

I would recommend not to use Kaspersky. Windows 10 has both functional  
updates and built-in protection, while most antivirus software gets crazy  
on development boxes.  
  
On Wed, May 24, 2017, 17:59 Rene Rivera <notifications@github.com> wrote:  
  
> This is not the most appropriate venue for this discussion. You should  
> really try posting to the Boost user email list. But some quick questions..  
> Did you verify that the Boost archive you downloaded is valid? There are  
> hashes and public-keys you can use to verify. What version of Boost are you  
> trying to build? Have you checked your anti-virus options to see why it  
> thinks the b2 executable is problematic? Have you contacted your AV support  
> channel for help?  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/build/issues/200#issuecomment-303750971>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AANye8Roo80zCe2BrJX4F6cmpzFcpN8tks5r9EXTgaJpZM4NlNfE>  
> .  
>  
--   
Vladimir Prus  
http://vladimirprus.com
