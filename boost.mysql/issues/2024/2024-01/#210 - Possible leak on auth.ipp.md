# #210 - Possible leak on auth.ipp [Closed]

> Username: Zen0x7  
> Created at: 2024-01-18 17:26:47 UTC  
> Updated at: 2024-03-06 11:52:57 UTC  
> Closed at: 2024-03-06 11:52:57 UTC  
> Url: https://github.com/boostorg/mysql/issues/210  

Hi package maintainers;  
  
By using valgrind, for some debugging purposes; I was able to discover something interesting.  
  
Valgrind reports me a couple of warnings of "**Leak but still reachable**":  
  
![image](https://github.com/boostorg/mysql/assets/8731267/68351e74-ada9-4ca1-a88b-9aaa7793a068)  
  
A lots are related to:  
  
![image](https://github.com/boostorg/mysql/assets/8731267/1ce95d4d-8594-42a5-9ca8-a8fa1ce81daf)  
  
Following  
  
![image](https://github.com/boostorg/mysql/assets/8731267/f326be95-2aab-4f68-8643-2b3968d984e4)  
  
I'm using  
  
![image](https://github.com/boostorg/mysql/assets/8731267/9c7ae33a-89b7-4b91-902e-8efc6a583aec)

---

## Comment 1

> Username: anarthal  
> Created at: 2024-01-18 18:55:28 UTC  
> Url: https://github.com/boostorg/mysql/issues/210#issuecomment-1899037942  

Hi @SpiritSaint,  
  
Thanks for the report. Could you please attach the full valgrind output for your run?  
  
Please also include:  
* Compiler version  
* Operating system  
* Compiler flags (debug or release mode?)  
  
Thanks,  
Ruben.

---

## Comment 2

> Username: anarthal  
> Created at: 2024-03-06 11:52:57 UTC  
> Url: https://github.com/boostorg/mysql/issues/210#issuecomment-1980703636  

I've been trying to reproduce this with the info I have and I haven't been able to. Valgrind reports everything clean. This may have been caused by an old openssl version allocating but not freeing some global state required for hashing. I can't confirm without version numbers.
