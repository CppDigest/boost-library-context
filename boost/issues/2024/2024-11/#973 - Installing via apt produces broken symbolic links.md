# #973 - Installing via apt produces broken symbolic links [Closed]

> Username: kskr24  
> Created at: 2024-11-03 09:16:39 UTC  
> Updated at: 2024-11-08 19:45:53 UTC  
> Closed at: 2024-11-08 19:45:53 UTC  
> Url: https://github.com/boostorg/boost/issues/973  

When I install via `sudo apt-get install libboost-all-dev`; the libraries are getting installed in /usr/lib/x86_64-linux-gnu  
  
When I run `ls -al /usr/lib/x86_64-linux-gnu/libboost_locale.so` I get the following  
lrwxrwxrwx 1 root root 25 Mar 17  2022 /usr/lib/x86_64-linux-gnu/libboost_locale.so -> libboost_locale.so.1.74.0  
  
Other than that a few other links are broken as well  
![Image](https://github.com/user-attachments/assets/6fd5252f-e61e-45bf-8077-090275ea25bc)
