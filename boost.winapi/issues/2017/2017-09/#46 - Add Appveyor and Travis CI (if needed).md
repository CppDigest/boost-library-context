# #46 - Add Appveyor and Travis CI (if needed) [Closed]

> Username: jeking3  
> Created at: 2017-09-14 00:50:14 UTC  
> Updated at: 2017-09-21 23:24:40 UTC  
> Closed at: 2017-09-15 18:06:44 UTC  
> Url: https://github.com/boostorg/winapi/issues/46  

We need a suite of tests that exercise the large number of compile-time branches in the project and ensure compatibility with Visual Studio versions (2010 through 2017) and older MinGW 32-bit and newer MinGW-x64.

---

## Comment 1

> Username: jeking3  
> Created at: 2017-09-16 13:04:19 UTC  
> Url: https://github.com/boostorg/winapi/issues/46#issuecomment-329967295  

@Lastique Please set milestone to v1.66.0 for this issue.

---

## Comment 2

> Username: jeking3  
> Created at: 2017-09-19 19:51:54 UTC  
> Url: https://github.com/boostorg/winapi/issues/46#issuecomment-330654070  

@Lastique I just noticed that my company email was used as the commit author instead of my apache.org email.  It should be "jking@apache.org".  Is there any way to fix that in the history, or is it impossible now that it was merged?

---

## Comment 3

> Username: Lastique  
> Created at: 2017-09-19 20:37:52 UTC  
> Url: https://github.com/boostorg/winapi/issues/46#issuecomment-330666211  

It is possible to rewrite history on develop, but that will cause inconsistencies with the superproject (i.e. some commits in the superproject will be referring to a non-existent commit in Boost.WinAPI). Unless you really want to change that email (e.g. it might get you in trouble with your employer or something) I would prefer to avoid doing this. Please, let me know if you still want me to do this.

---

## Comment 4

> Username: jeking3  
> Created at: 2017-09-21 23:24:39 UTC  
> Url: https://github.com/boostorg/winapi/issues/46#issuecomment-331308442  

I figured it would be painful - we'll let it be.  
  
On Tue, Sep 19, 2017 at 4:37 PM, Andrey Semashev <notifications@github.com>  
wrote:  
  
> It is possible to rewrite history on develop, but that will cause  
> inconsistencies with the superproject (i.e. some commits in the  
> superproject will be referring to a non-existent commit in Boost.WinAPI).  
> Unless you really want to change that email (e.g. it might get you in  
> trouble with your employer or something) I would prefer to avoid doing  
> this. Please, let me know if you still want me to do this.  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/winapi/issues/46#issuecomment-330666211>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/ALOdbZ8aXFQ-qXy14vlB6xD3vor7Evhuks5skCYhgaJpZM4PW6D9>  
> .  
>
