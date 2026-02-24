# #93 - Add support for unicode file path on windows in boost::interprocess::sync::file_lock [Closed]

> Username: mdoppler  
> Created at: 2019-07-02 07:22:05 UTC  
> Updated at: 2021-01-30 23:27:42 UTC  
> Closed at: 2021-01-30 23:27:42 UTC  
> Url: https://github.com/boostorg/interprocess/issues/93  

boost::interprocess::sync::file_lock currently does not support unicode paths on windows.   
  
This patch adds support for them on windows:  
  
[boost_1_70_0-windows-unicode-filelock.zip](https://github.com/boostorg/interprocess/files/3348780/boost_1_70_0-windows-unicode-filelock.zip)
