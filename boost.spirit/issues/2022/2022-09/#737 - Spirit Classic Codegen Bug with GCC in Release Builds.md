# #737 - Spirit Classic Codegen Bug with GCC in Release Builds [Open]

> Username: cmazakas  
> Created at: 2022-09-23 18:30:58 UTC  
> Updated at: 2025-05-09 23:10:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/737  

I've been working with @pdimov on some quickbook CI failures and I believe the cause of the issue is a codegen bug in gcc that seems to manifest in Spirit Classic. Namely, section IDs aren't being correctly produced when the qbk file contains:  
```  
[include:foo include-inc.quickbook]  
```  
  
More can be seen here: https://github.com/boostorg/quickbook/actions/runs/3046704503  
  
This seems to be because of a codegen bug caused by certain versions of gcc (seemingly 9 through 12.1) with `-O2` and higher.  
  
I can replicate the issue here: https://godbolt.org/z/oa5b6j6Pd  
  
Under `-O1` and lower, the output is:  
```bash  
Current iterator range: foo  
```  
but with `-O2` and higher,  
```bash  
Current iterator range: :foo  
```  
This is incorrect and is what is causing quickbook to generate incorrect output, which causes the test to fail.  
  
I know that Classic is likely not under maintenance anymore but because quickbook is such an integral part of Boost and its toolchain, this might be worth fixing.  
  
Let me know if any more information is required! The code in the Compiler Explorer example is a close copy-paste of the code used by quickbook today but can probably be simplified.

---

## Comment 1

> Username: Kojoley  
> Created at: 2022-09-23 23:34:12 UTC  
> Url: https://github.com/boostorg/spirit/issues/737#issuecomment-1256796668  

Thanks for a reproducer, but I don't think I can offer any help beyond telling that it seems to be fixed in GCC 12.2, what you most likely already know? I poked the reproducer in CE and affected GCC versions are 9.2-12.1, and it is also vanishes when sanitization is enabled. I can report to GCC on your behalf that they had and issue which might be fixed by accident.

---

## Comment 2

> Username: Lastique  
> Created at: 2022-09-24 01:25:52 UTC  
> Updated at: 2022-09-24 01:27:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/737#issuecomment-1256827442  

Is there a workaround possible on Boost.Spirit side? Or QuickBook?

---

## Comment 3

> Username: Kojoley  
> Created at: 2022-09-24 01:43:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/737#issuecomment-1256830393  

Massaging the parser helps avoid the bug for reproducer, so might be done something to Quickbook too https://godbolt.org/z/oGeqrP937
