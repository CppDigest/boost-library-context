# #518 - Unstable `lockfree boost::atomic_flag` capability on MacOS Mojave over rebuild [Open]

> Username: klirichek  
> Created at: 2019-12-18 04:36:49 UTC  
> Updated at: 2021-05-29 18:22:35 UTC  
> Url: https://github.com/boostorg/build/issues/518  

```  
./bootstrap.sh --prefix=/work/boost/build --with-libraries=context  
./b2  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
  
Building the Boost C++ Libraries.  
  
  
    - symlinks supported       : yes  
    - C++11 mutex              : no  
    - lockfree boost::atomic_flag : no  
...  
```  
then remove `./bin.v2` folder and try again with b2:  
  
```  
./b2  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
  
Building the Boost C++ Libraries.  
  
  
    - symlinks supported       : yes  
    - C++11 mutex              : no  
    - lockfree boost::atomic_flag : yes  
```  
  
Note that `lockfree boost::atomic_flag` became 'yes' on second pass.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 18:22:05 UTC  
> Url: https://github.com/boostorg/build/issues/518#issuecomment-850877330  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
