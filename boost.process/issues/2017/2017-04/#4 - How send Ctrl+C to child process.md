# #4 - How send Ctrl+C to child process? [Closed]

> Username: KindDragon  
> Created at: 2017-04-13 19:16:21 UTC  
> Updated at: 2017-04-14 11:24:23 UTC  
> Closed at: 2017-04-13 22:16:12 UTC  
> Url: https://github.com/boostorg/process/issues/4  

I want to terminate my child `git` process

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2017-04-13 22:16:12 UTC  
> Url: https://github.com/boostorg/process/issues/4#issuecomment-294035851  

Terminate can be done with `child::terminate`, but Strg+C is not supported, because there is no portable way to do so.

---

## Comment 2

> Username: KindDragon  
> Created at: 2017-04-13 22:21:01 UTC  
> Url: https://github.com/boostorg/process/issues/4#issuecomment-294036726  

What you mean by "no portable way"? Your library have a lot of custom code for Windows and Posix

---

## Comment 3

> Username: KindDragon  
> Created at: 2017-04-13 22:22:33 UTC  
> Url: https://github.com/boostorg/process/issues/4#issuecomment-294036970  

> Terminate can be done with child::terminate  
  
It will terminate only one process, not a whole child process tree

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2017-04-14 08:51:58 UTC  
> Url: https://github.com/boostorg/process/issues/4#issuecomment-294119038  

Strg+C is an interrupt, and there is no unified way to do that on windows. Thus it wouldn't be portable, because it would not work the same on both platforms.   
If you want to terminate the whole process tree you can use `group`.

---

## Comment 5

> Username: KindDragon  
> Created at: 2017-04-14 11:17:19 UTC  
> Updated at: 2017-04-14 11:17:48 UTC  
> Url: https://github.com/boostorg/process/issues/4#issuecomment-294137853  

> Strg+C is an interrupt, and there is no unified way to do that on windows.  
  
On Windows you have [`GenerateConsoleCtrlEvent`](https://msdn.microsoft.com/ru-ru/library/windows/desktop/ms683155%28v=vs.85%29.aspx?f=255&MSPPError=-2147217396) http://stackoverflow.com/a/12899190/61505

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2017-04-14 11:19:39 UTC  
> Url: https://github.com/boostorg/process/issues/4#issuecomment-294138082  

I know, but that does only works for console processes, not for those with a window. That's what I mean by not portable.

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2017-04-14 11:20:41 UTC  
> Url: https://github.com/boostorg/process/issues/4#issuecomment-294138181  

You can of course still use that, when you obtain child handle through `child::native_handle`.

---

## Comment 8

> Username: KindDragon  
> Created at: 2017-04-14 11:22:34 UTC  
> Url: https://github.com/boostorg/process/issues/4#issuecomment-294138390  

Ok, maybe sometime in the long distant future you will be supporting Ctrl+C 😄

---

## Comment 9

> Username: klemens-morgenstern  
> Created at: 2017-04-14 11:24:23 UTC  
> Url: https://github.com/boostorg/process/issues/4#issuecomment-294138600  

Probably never, I only put in features that work similar on both platforms. An `interrupt` does not qualify, but if you have a design idea or maybe even a implementation, I'll definitely take a look.
