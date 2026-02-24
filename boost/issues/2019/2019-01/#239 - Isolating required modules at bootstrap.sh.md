# #239 - Isolating required modules at bootstrap.sh [Closed]

> Username: Dravion  
> Created at: 2019-01-03 09:37:49 UTC  
> Updated at: 2019-12-17 19:32:10 UTC  
> Closed at: 2019-12-17 19:32:10 UTC  
> Url: https://github.com/boostorg/boost/issues/239  

Hi,  
I use BOOST, thanks for this great peace of Software!  
  
But i think the Build Documentation should cover Minimal builds as well. Its a  pain to built each and everything  for any Setup (Debug/Release, diffrent archl diffrent OS, diffrent Compiler ect) and it  
would be cool if there is an tutorial which covers the step necessary to build user specific boost   
instead of the whole thing. Such a Guide should also cover howto extend modules, which where  
not build at the initial build so its possible to rerun the build process of an existing allready  
builded BOOST build and only add specific modules and nothing else.

---

## Comment 1

> Username: Dravion  
> Created at: 2019-01-03 11:40:48 UTC  
> Updated at: 2019-01-03 11:46:41 UTC  
> Url: https://github.com/boostorg/boost/issues/239#issuecomment-451120428  

If found a way on Stackoverflow https://stackoverflow.com/questions/10022180/how-to-build-boost-required-modules-only  
  
For Visual Studio 2017 i use this for selective builds  
#Win32   
b2 toolset=msvc-14.1 --build-dir=build --stagedir=out32 --with-filesystem --with-regex --with-thread  
#Win64  
b2 toolset=msvc-14.1 --build-dir=build address-model=64 --stagedir=out64 --with-filesystem --with-regex --with-thread  
  
I took about 4 Minutes instead of 20 Minutes to build for the Wib32 target.  
  
#The Result is:   
  
dir boost/1_69_0/out32/lib  
  
01/03/2019  12:37 PM         3,129,096 libboost_chrono-vc141-mt-gd-x32-1_69.lib  
01/03/2019  12:38 PM         3,347,584 libboost_chrono-vc141-mt-gd-x64-1_69.lib  
01/03/2019  12:37 PM           454,676 libboost_chrono-vc141-mt-x32-1_69.lib  
01/03/2019  12:39 PM           604,132 libboost_chrono-vc141-mt-x64-1_69.lib  
01/03/2019  12:36 PM         8,485,550 libboost_filesystem-vc141-mt-gd-x32-1_69.lib  
01/03/2019  12:38 PM         9,094,880 libboost_filesystem-vc141-mt-gd-x64-1_69.lib  
01/03/2019  12:37 PM         1,014,254 libboost_filesystem-vc141-mt-x32-1_69.lib  
01/03/2019  12:38 PM         1,321,334 libboost_filesystem-vc141-mt-x64-1_69.lib  
01/03/2019  12:36 PM        40,095,868 libboost_regex-vc141-mt-gd-x32-1_69.lib  
01/03/2019  12:38 PM        50,893,266 libboost_regex-vc141-mt-gd-x64-1_69.lib  
01/03/2019  12:37 PM        13,406,756 libboost_regex-vc141-mt-x32-1_69.lib  
01/03/2019  12:39 PM        17,067,488 libboost_regex-vc141-mt-x64-1_69.lib  
01/03/2019  12:36 PM            34,520 libboost_system-vc141-mt-gd-x32-1_69.lib  
01/03/2019  12:38 PM            34,590 libboost_system-vc141-mt-gd-x64-1_69.lib  
01/03/2019  12:37 PM             1,504 libboost_system-vc141-mt-x32-1_69.lib  
01/03/2019  12:38 PM             1,430 libboost_system-vc141-mt-x64-1_69.lib  
01/03/2019  12:37 PM         4,939,792 libboost_thread-vc141-mt-gd-x32-1_69.lib  
01/03/2019  12:38 PM         5,425,216 libboost_thread-vc141-mt-gd-x64-1_69.lib  
01/03/2019  12:38 PM           930,890 libboost_thread-vc141-mt-x32-1_69.lib  
01/03/2019  12:39 PM         1,166,822 libboost_thread-vc141-mt-x64-1_69.lib  
  
Roughly about 154 MBytes instead of dozens of GBytes.  
  
I think its worth adding it to the build guide.

---

## Comment 2

> Username: tiendq  
> Created at: 2019-01-17 02:40:41 UTC  
> Url: https://github.com/boostorg/boost/issues/239#issuecomment-455021387  

Building selective libs are already instructed [here](https://github.com/boostorg/boost/wiki/Getting-Started%3A-Cheat-Sheet#list-of-steps).
