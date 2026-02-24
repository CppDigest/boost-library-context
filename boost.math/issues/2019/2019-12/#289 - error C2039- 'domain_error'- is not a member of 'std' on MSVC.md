# #289 - error C2039: 'domain_error': is not a member of 'std' on MSVC [Closed]

> Username: QuellaZhang  
> Created at: 2019-12-23 07:09:05 UTC  
> Updated at: 2019-12-23 14:31:46 UTC  
> Closed at: 2019-12-23 14:31:46 UTC  
> Url: https://github.com/boostorg/math/issues/289  

**Issue description:**  
Found Boost\libs\math build error C2039 C3861 in a future release of MSVC. Could you please take a look?  
  
**Reproduce steps:**  
  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x64 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\math\test  
  
**ErrorMessage:**  
catmull_rom_incl_test.cpp  
.\boost/math/interpolators/catmull_rom.hpp(97): error C3861: 'domain_error': identifier not found  
.\boost/math/interpolators/catmull_rom.hpp(101): error C2039: 'domain_error': is not a member of 'std'  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\VC\Tools\MSVC\14.16.27023\include\typeinfo(25): note: see declaration of 'std'  
.\boost/math/interpolators/catmull_rom.hpp(101): error C3861: 'domain_error': identifier not found  
.\boost/math/interpolators/catmull_rom.hpp(122): error C2039: 'domain_error': is not a member of 'std'

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-12-23 08:37:14 UTC  
> Url: https://github.com/boostorg/math/issues/289#issuecomment-568403141  

We need to be including <stdexcept> @NAThompson ?

---

## Comment 2

> Username: ckormanyos  
> Created at: 2019-12-23 08:56:06 UTC  
> Url: https://github.com/boostorg/math/issues/289#issuecomment-568407935  

Yes. Just a thought. Could it be a simple case that a standard header is not being included, or not being included in the needed order?

---

## Comment 3

> Username: NAThompson  
> Created at: 2019-12-23 14:31:42 UTC  
> Url: https://github.com/boostorg/math/issues/289#issuecomment-568490793  

Should be fixed [here](https://github.com/boostorg/math/commit/3b0bf08bba46bb302e216ae015396e1b3f2d580c), let me know if it's not.
