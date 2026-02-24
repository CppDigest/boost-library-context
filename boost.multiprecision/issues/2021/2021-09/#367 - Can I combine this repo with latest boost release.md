# #367 - Can I combine this repo with latest boost release [Closed]

> Username: afabri  
> Created at: 2021-09-08 20:14:34 UTC  
> Updated at: 2021-09-09 10:22:51 UTC  
> Closed at: 2021-09-09 10:22:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/367  

Can I just clone this repo and use it with an installed version of boost, or does it depend on recent code so that I must clone the whole boost?    I ask in particular with respect to the branch with the new `rational_adaptor`.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-09-08 20:41:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/367#issuecomment-915555005  

You should only need to clone this repro.  Occasionally this library and  
Boost.Math have linked changes, but I don't think there have been any since  
the last release.  
  
On Wed, 8 Sept 2021 at 21:14, Andreas Fabri ***@***.***>  
wrote:  
  
> Can I just clone this repo and use it with an installed version of boost,  
> or does it depend on recent code so that I must clone the whole boost? I  
> ask in particular with respect to the branch with the new rational_adaptor  
> .  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/multiprecision/issues/367>, or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABGHSOFI6YMZ24R75427CC3UA673NANCNFSM5DVTLOXA>  
> .  
> Triage notifications on the go with GitHub Mobile for iOS  
> <https://apps.apple.com/app/apple-store/id1477376905?ct=notification-email&mt=8&pt=524675>  
> or Android  
> <https://play.google.com/store/apps/details?id=com.github.android&referrer=utm_campaign%3Dnotification-email%26utm_medium%3Demail%26utm_source%3Dgithub>.  
>  
>
