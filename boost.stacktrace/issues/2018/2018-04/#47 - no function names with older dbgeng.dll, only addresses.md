# #47 - no function names with older dbgeng.dll, only addresses [Closed]

> Username: zlojvavan  
> Created at: 2018-04-06 07:47:09 UTC  
> Updated at: 2020-01-24 19:21:38 UTC  
> Closed at: 2020-01-24 19:21:38 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/47  

not the bugreport, rather just for the record. I couldn't make it work on my win 7 and tried several dbgeng.dll versions to no avail until I installed fresh win ddk which did the trick for me. side effect is extra dependencies on DbgModel.dll and dbghelp.dll I've had to put into target directory to let application start. guess this could be reflected in docs as windows specific details

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-04-07 06:28:07 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/47#issuecomment-379436238  

What version of Visual Studio you were using?

---

## Comment 2

> Username: zlojvavan  
> Created at: 2018-04-09 06:39:13 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/47#issuecomment-379649817  

Microsoft Visual Studio Community 2017   
Version 15.6.5  
VisualStudio.15.Release/15.6.5+27428.2027  
Microsoft .NET Framework  
Version 4.7.02558  
  
Installed Version: Community  
  
Visual C++ 2017   00369-60000-00001-AA507  
Microsoft Visual C++ 2017

---

## Comment 3

> Username: zlojvavan  
> Created at: 2018-04-19 06:20:12 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/47#issuecomment-382623499  

deployed the very same app on windows server 2012 r2, installed win ddk there and put those 3 dlls and .pdb next to app but only see raw addresses in the stacktrace

---

## Comment 4

> Username: apolukhin  
> Created at: 2020-01-23 21:01:33 UTC  
> Updated at: 2020-01-23 21:02:04 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/47#issuecomment-577875162  

@zlojvavan please take a look at the docs in 9b5bc54   
Does it solve your problem?

---

## Comment 5

> Username: zlojvavan  
> Created at: 2020-01-24 06:46:53 UTC  
> Updated at: 2020-01-24 06:48:52 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/47#issuecomment-578012258  

> @zlojvavan please take a look at the docs in [9b5bc54](https://github.com/boostorg/stacktrace/commit/9b5bc54fe3050762bb617d62bb73d842be1e165b)  
> Does it solve your problem?  
  
I resolved the problem back in last february after applying emptyVoid [workaround](https://github.com/boostorg/stacktrace/issues/55#issuecomment-467053114) (/PDBALTPATH:%_PDB% )

---

## Comment 6

> Username: apolukhin  
> Created at: 2020-01-24 19:21:38 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/47#issuecomment-578265067  

Thanks for the update!
