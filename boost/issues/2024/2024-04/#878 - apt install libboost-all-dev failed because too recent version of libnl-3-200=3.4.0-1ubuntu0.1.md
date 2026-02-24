# #878 - apt install libboost-all-dev failed because too recent version of libnl-3-200=3.4.0-1ubuntu0.1 [Open]

> Username: HarukiZhang  
> Created at: 2024-04-01 06:43:20 UTC  
> Updated at: 2024-04-01 06:47:18 UTC  
> Url: https://github.com/boostorg/boost/issues/878  

Hi, recently, I've tried to install libboost-all-dev using apt in ubuntu 20.04, but failed because there already a **libnl-3-200=3.4.0-1ubuntu0.1** focal update installed in my system, and libboost-all-dev traced up finally depending on **libnl-3-200=3.4.0-1** version (i.e., without a sub update by ubuntu).  
  
I am new in this field. Could anyone here please tell me what should I do to install libboost-all-dev without caussing network disfuntion to my system.   
  
Thanks!  
  
Besides, I have searched for ubuntu package and found message about libnl-3-200 update, but don't know the significance of the library to the system. The update log as follow:  
https://[changelogs.ubuntu.com/changelogs/pool/main/libn/libnl3/libnl3_3.4.0-1ubuntu0.1/changelog](https://changelogs.ubuntu.com/changelogs/pool/main/libn/libnl3/libnl3_3.4.0-1ubuntu0.1/changelog)
