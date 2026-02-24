# #55 - Considering bii code [Closed]

> Username: HaMster21  
> Created at: 2015-04-23 19:28:58 UTC  
> Updated at: 2015-08-18 20:00:01 UTC  
> Closed at: 2015-08-18 19:48:31 UTC  
> Url: https://github.com/boostorg/hana/issues/55  

Now, I know that you are working for the formal review for the boost inclusion, but in the mean time I'd like to play around with hana, and I'd like it to be easy to integrate. I don't know if you are following up on this, but there are efforts to make [bii](https://www.biicode.com) a dependency management tool for C++ project and they are heading towards [open-sourcing](https://github.com/biicode/biicode) the whole thing.  
  
One upside would be that boost is already there and bii is pulling dependencies automagically :boat:  
  
So what do you thing about trying to set up a block for hana there to make it easier to play around with hana?

---

## Comment 1

> Username: ldionne  
> Created at: 2015-04-23 19:38:18 UTC  
> Url: https://github.com/boostorg/hana/issues/55#issuecomment-95695172  

I think that's a good idea, and if biicode becomes big then it's going to be a no-brainer. I'll try to look into it, but right now I have more urgent things to do so it might have to wait for a bit. Also, if you want to try Hana right now, it should just be a matter of cloning the repository; Hana has no hard dependency on Boost, so all you need is a working C++14 compiler.

---

## Comment 2

> Username: HaMster21  
> Created at: 2015-04-24 06:51:17 UTC  
> Url: https://github.com/boostorg/hana/issues/55#issuecomment-95823775  

I have a toy project that is already using hana, and I was getting along. bii just brings some :lollipop: candy with it.  
  
I'll likely have some time during the weekend, lets see if I can prepare a pull that sets you up for bii. We'll all be better off I you keep concentrating on hana itself I guess :+1:

---

## Comment 3

> Username: ldionne  
> Created at: 2015-04-24 12:38:18 UTC  
> Url: https://github.com/boostorg/hana/issues/55#issuecomment-95919604  

A pull request would be terrific!

---

## Comment 4

> Username: Manu343726  
> Created at: 2015-05-21 08:41:08 UTC  
> Updated at: 2015-05-21 08:41:40 UTC  
> Url: https://github.com/boostorg/hana/issues/55#issuecomment-104182619  

Looking very briefly at the root `CMakeLists.txt` seems like most of the manual targets setup could be avoided in biicode since bii scans your sources to create library/executable/test targets. See http://docs.biicode.com/c++/building/biicode_building.html

---

## Comment 5

> Username: ldionne  
> Created at: 2015-08-18 19:48:31 UTC  
> Url: https://github.com/boostorg/hana/issues/55#issuecomment-132330515  

I just read [this unfortunate new](http://blog.biicode.com/biicode-just-the-company-post-mortem/) about Biicode. Even though it is just about the company, it still seems like supporting biiicode integration has less value now. Closing this.

---

## Comment 6

> Username: Manu343726  
> Created at: 2015-08-18 19:51:27 UTC  
> Url: https://github.com/boostorg/hana/issues/55#issuecomment-132331366  

As with Niebler on range-v3, I would liked to advise you before, but I was  
not authorized to do so. Anyway,  
  
El mar, 18 de ago de 2015 21:48, Louis Dionne notifications@github.com  
escribió:  
  
> I just read this unfortunate new  
> http://blog.biicode.com/biicode-just-the-company-post-mortem/ about  
> Biicode. Even though it is just about the company, it still seems like  
> supporting biiicode integration has less value now. Closing this.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/ldionne/hana/issues/55#issuecomment-132330515.

---

## Comment 7

> Username: ldionne  
> Created at: 2015-08-18 19:53:52 UTC  
> Url: https://github.com/boostorg/hana/issues/55#issuecomment-132331865  

Thanks @Manu343726. I'm sorry about the new.

---

## Comment 8

> Username: Manu343726  
> Created at: 2015-08-18 19:56:32 UTC  
> Url: https://github.com/boostorg/hana/issues/55#issuecomment-132332412  

Sorry about the boilerplate, my phone screen gone crazy. What I was trying to say...: Self hosted FindHana.cmake that downloads the library is a good replacement I think.

---

## Comment 9

> Username: ldionne  
> Created at: 2015-08-18 20:00:01 UTC  
> Url: https://github.com/boostorg/hana/issues/55#issuecomment-132333117  

> Sorry about the boilerplate, my phone screen gone crazy.   
  
No problem.  
  
> What I was trying to say...: Self hosted FindHana.cmake that downloads the library is a good replacement I think.  
  
I do think that it makes the library decently easy to use. Also, since it's header only, the barrier is very low.
