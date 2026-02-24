# #80 - Missing symbols when build with vcpkg [Closed]

> Username: yyzybb537  
> Created at: 2018-05-01 05:53:11 UTC  
> Updated at: 2018-05-18 07:10:26 UTC  
> Closed at: 2018-05-18 07:10:26 UTC  
> Url: https://github.com/boostorg/context/issues/80  

The libboost_context.a missing symbols:  
  make_fcontext  
  jump_fcontext  
  ontop_fcontext  
  
when boost.context built by vcpkg.  
I think your build script what you committed, lose the `asm` files in vcpkg.

---

## Comment 1

> Username: olk  
> Created at: 2018-05-01 07:32:08 UTC  
> Updated at: 2018-05-01 07:33:03 UTC  
> Url: https://github.com/boostorg/context/issues/80#issuecomment-385616618  

Wether I build boost.context with link=shared or link=static, objdump -t shows the symbols of the fcontext-functions contained in the symbol table (static and shared lib).  
I don't know and use vcpkg. Is vcpkg officially supported by boost?

---

## Comment 2

> Username: yyzybb537  
> Created at: 2018-05-05 09:06:42 UTC  
> Url: https://github.com/boostorg/context/issues/80#issuecomment-386791560  

Yes, vcpkg can install boost in linux now, because boost support for vcpkg!  
I think it will be popular package manager.

---

## Comment 3

> Username: yyzybb537  
> Created at: 2018-05-05 09:13:27 UTC  
> Url: https://github.com/boostorg/context/issues/80#issuecomment-386791907  

The bug because when build boost-context with vcpkg. ASM files was not built, can you fix it?

---

## Comment 4

> Username: olk  
> Created at: 2018-05-17 06:50:34 UTC  
> Url: https://github.com/boostorg/context/issues/80#issuecomment-389763269  

ASM files are compiled if you use b2 ... as I told you I don't use Windows and I'm unfamiliar with vcpkg.  
So I've no idea why yopu get this problem ... I guess it is related how you build boost.context

---

## Comment 5

> Username: ras0219-msft  
> Created at: 2018-05-18 06:25:05 UTC  
> Updated at: 2018-05-18 06:28:10 UTC  
> Url: https://github.com/boostorg/context/issues/80#issuecomment-390107209  

This should be fixed in the latest vcpkg master. We weren't passing the correct architecture flag to b2, which caused it to be unable to select the correct asm files.  
  
@yyzybb537 I've asked for some more clarifying information in the vcpkg thread if your issue is still unsolved.  
  
@olk I think this issue can be closed for now; we will come back if we confirm it's not a vcpkg problem :)

---

## Comment 6

> Username: olk  
> Created at: 2018-05-18 07:10:26 UTC  
> Url: https://github.com/boostorg/context/issues/80#issuecomment-390116332  

OK, tx
