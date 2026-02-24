# #491 - Error in runnung bjam [Open]

> Username: daviewan  
> Created at: 2019-10-09 11:20:52 UTC  
> Updated at: 2024-03-05 19:00:09 UTC  
> Url: https://github.com/boostorg/build/issues/491  

Running bjam i get the followingg error: ./bjam -j3  
  
  
Failed to run bash -c "g++  -dM -x c++ -E /dev/null -include boost/version.hpp 2>/dev/null |grep '#define BOOST_'"  
Boost does not seem to be installed or g++ is confused.

---

## Comment 1

> Username: Shireen35  
> Created at: 2021-05-02 05:53:52 UTC  
> Url: https://github.com/boostorg/build/issues/491#issuecomment-830755854  

Hey,  
Did you get the solution for this I am getting the same problem

---

## Comment 2

> Username: daviewan  
> Created at: 2021-05-02 09:59:21 UTC  
> Updated at: 2021-05-02 10:06:47 UTC  
> Url: https://github.com/boostorg/build/issues/491#issuecomment-830782147  

Try ./bjam --prefix=... install  
  
or you can also install g++  
  
sudo apt install build-essential  
  
or installing boost  
  
sudo apt update  
sudo apt install libboost-all-dev

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-05-29 16:22:14 UTC  
> Url: https://github.com/boostorg/build/issues/491#issuecomment-850859555  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.

---

## Comment 4

> Username: waisyousofi  
> Created at: 2024-03-05 19:00:08 UTC  
> Url: https://github.com/boostorg/build/issues/491#issuecomment-1979445145  

For my case doing SRILM step of this link helped:  
https://masonreznov.github.io/installing-moses-the-statistical-machine-translation-toolkit/
