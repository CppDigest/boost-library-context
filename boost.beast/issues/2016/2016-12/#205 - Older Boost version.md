# #205 - Older Boost version [Closed]

> Username: AGaliuzov  
> Created at: 2016-12-12 07:29:25 UTC  
> Updated at: 2017-01-04 19:06:50 UTC  
> Closed at: 2017-01-04 19:06:50 UTC  
> Url: https://github.com/boostorg/beast/issues/205  

Hello @vinniefalco, we are using Boost 1.56 and in documentations here I see restrictions to 1.58 or higher.  
  
Are there any known issues with 1.56? In case It will work fine may I send the pull request?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-12-16 16:21:19 UTC  
> Url: https://github.com/boostorg/beast/issues/205#issuecomment-267631730  

I usually run all the automated tests with a more recent Boost version. I am building Beast to become a Boost library (formal review early next year). When that happens, Beast won't be available with earlier versions of Boost. You can try building Beast with an older Boost, but it might not work. I can't remember what the problem was. I wouldn't mind looking over your proposed changes to get Beast compiling in Boost. If they are very minor I would consider it but there's no guarantee.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-01-04 19:06:50 UTC  
> Url: https://github.com/boostorg/beast/issues/205#issuecomment-270457513  

I'll close this for now, feel free to re-open it if there is new information.
