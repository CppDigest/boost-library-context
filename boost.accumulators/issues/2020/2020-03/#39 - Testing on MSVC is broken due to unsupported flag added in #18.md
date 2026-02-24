# #39 - Testing on MSVC is broken due to unsupported flag added in #18 [Open]

> Username: Kojoley  
> Created at: 2020-03-02 01:37:37 UTC  
> Updated at: 2020-03-02 01:37:37 UTC  
> Url: https://github.com/boostorg/accumulators/issues/39  

https://www.boost.org/development/tests/develop/developer/output/teeks99-09-p-64onAMD64-boost-bin-v2-libs-accumulators-test-count-test-msvc-14-1-dbg-adrs-mdl-64-itrtr-off-lnk-sttc-thrd-mlt.html  
```  
    call "D:\teeks99-09\run\results\boost\bin.v2\standalone\msvc\msvc-14.1\adrs-mdl-64\archt-x86\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo @"D:\teeks99-09\run\results\boost\bin.v2\libs\accumulators\test\count.test\msvc-14.1\dbg\adrs-mdl-64\itrtr-off\lnk-sttc\thrd-mlt\count.obj.rsp"   
  
cl : Command line error D8021 : invalid numeric argument '/Wno-deprecated-declarations'  
  
```
