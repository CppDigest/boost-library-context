# #130 Add option to wait for keypress before application terminates [Closed]

> Username: ShadowIce  
> Created at: 2018-02-13 14:16:35 UTC  
> Updated at: 2018-08-29 08:49:45 UTC  
> Closed at: 2018-08-29 07:36:55 UTC  
> Url: https://github.com/boostorg/test/pull/130  

I've added a command line parameter to pause execution until a key is pressed. This should be very useful if you run tests directly in Visual Studio with attached debugger because Visual Studio just closes the console window after the application terminates.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2018-08-29 07:48:22 UTC  
> Url: https://github.com/boostorg/test/pull/130#issuecomment-416858738  

Hi,  
  
Why are you closing this?

---

## Comment 2

> Username: ShadowIce  
> Created_at: 2018-08-29 08:49:45 UTC  
> Url: https://github.com/boostorg/test/pull/130#issuecomment-416876168  

Because Microsoft added a feature to keep the console window open after the process terminates, so from my point of view this is not necessary any more (https://visualstudio.uservoice.com/forums/121579-visual-studio-ide/suggestions/31124656-toggle-console-window-to-stay-open-on-off-settin).  
  
But if you or someone else still needs this feature I can reopen the PR again.

---
