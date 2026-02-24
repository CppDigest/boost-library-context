# #191 asm: adjust XCOFF files for AIX assembler [Merged]

> Username: Helflym  
> Created at: 2021-11-03 09:53:27 UTC  
> Updated at: 2021-11-03 10:15:49 UTC  
> Merged at: 2021-11-03 10:12:54 UTC  
> Closed at: 2021-11-03 10:12:54 UTC  
> Url: https://github.com/boostorg/context/pull/191  

AIX assembler is a bit more strict than GNU assembler. Thus, adjust  
the XCOFF asm files to be able to accept both assembler.  
  
For PPC64 jump and make files, most of the work have already been  
made recently, only the functions' header needs to be updated.  
For PPC64 ontop and PPC32 files, the algorithms where also wrong.  
So the whole files have been reworked.  
  
The PPC32 stack layout is based on AIX documentation:  
https://www.ibm.com/docs/en/aix/7.2?topic=overview-runtime-process-stack  
For PPC64, as it seems to work fine and is already being used in php,  
I've kept the current layout based on PPC64 Linux version.  
  
Tested with boost/context, boost/fiber and boost/coroutine2.  
Note that the test_sscanf is still failing in ppc32 because of  
float precision. (3.13999 is returned instead of 3.14).

---

## Comment 1

> Username: olk  
> Created_at: 2021-11-03 10:12:58 UTC  
> Url: https://github.com/boostorg/context/pull/191#issuecomment-958816081  

ty

---
