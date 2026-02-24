# #460 - Service not found when compiling with -fsanitize=address [Closed]

> Username: sagunkho  
> Created at: 2025-03-22 06:23:04 UTC  
> Updated at: 2025-03-25 14:30:21 UTC  
> Closed at: 2025-03-25 14:30:20 UTC  
> Url: https://github.com/boostorg/mysql/issues/460  

I have an application that I'm compiling with option `-fsanitize=address`. I have mysql running on local host. The application throws "Service not found" error when attempting to connect. Using the latest version of boost i.e. 1.87.0.  
This error doesn't occur on a build without the `-fsanitize=address`  
  
**With `-fsanitize=address` :**  
![Image](https://github.com/user-attachments/assets/e618756a-6457-4a6d-aa7e-0b8611a86c7a)  
  
**Without `-fsanitize=address`:**  
![Image](https://github.com/user-attachments/assets/223d53cb-d92c-4d93-bab2-1658e2bd1852)  
  
Code responsible for this can be found here -   
http://lab.projecthorizon.xyz/horizon/horizon/-/blob/master/src/Server/Common/Server.cpp?ref_type=heads#L275

---

## Comment 1

> Username: anarthal  
> Created at: 2025-03-22 12:06:39 UTC  
> Url: https://github.com/boostorg/mysql/issues/460#issuecomment-2745239344  

Hi,  
  
I can see that you're using `tcp_ssl_connection`. The error you're mentioning is thrown by `asio::tcp::resolver::resolve`, which isn't called by tcp_ssl_connection at all. I don't think that error can be physically be thrown by the function you're pointing at, since it performs no name resolution.  
  
I'm afraid I won't be able to help much more without a minimum reproducible example. This should include the minimum amount of code to be able to reproduce the issue, with no external code or dependencies.  
  
Thanks,  
Ruben.

---

## Comment 2

> Username: sagunkho  
> Created at: 2025-03-22 13:25:31 UTC  
> Url: https://github.com/boostorg/mysql/issues/460#issuecomment-2745266885  

@anarthal I've used GDB to identify the cause of the issue, the line is [275 on this page](http://lab.projecthorizon.xyz/horizon/horizon/-/blob/master/src/Server/Common/Server.cpp?ref_type=heads#L275)  
  
![Image](https://github.com/user-attachments/assets/4b678fba-52af-4381-98b0-809ff7e00e7e)

---

## Comment 3

> Username: anarthal  
> Created at: 2025-03-25 14:30:19 UTC  
> Url: https://github.com/boostorg/mysql/issues/460#issuecomment-2751461652  

This seems to confirm that the problem you're seeing is not related to Boost.MySQL, so if you're okay with it, I will be closing this issue. If I can help with anything else, please let me know.  
  
Thanks,  
Ruben.
