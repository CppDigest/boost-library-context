# #697 - json::serialize takes an extraordinary amount of time on clang / libc++ [Closed]

> Username: madmongo1  
> Created at: 2022-04-23 21:30:05 UTC  
> Updated at: 2022-05-15 08:25:43 UTC  
> Closed at: 2022-05-15 08:25:43 UTC  
> Url: https://github.com/boostorg/json/issues/697  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
When reporting a bug please include the following:  
  
### Version of Boost  
  
1.79  
  
### Steps necessary to reproduce the problem  
  
Compile the linked program https://github.com/madmongo1/json-bug-1  
  
with:  
clang 13  
`-stdlib=libc++`  
  
### All relevant compiler information  
  
clang 13  
`-stdlib=libc++`  
  
  
Run the executable in the same directory as the bundled log file `demo.log`.  
  
The program reads one line of text from the file, parses it to json and then serialises the json value back to a string.  
  
With GCC, the serialisation is instantaneous.  
  
With clang (in both debug and release configurations), the parsing is instantaneous, but the serialisation takes many minutes.  
  
It does make progress, but incredibly slowly. I have waited 5-10 minutes. In that time, the program was only able serialise approx 140k of text.

---

## Comment 1

> Username: sehe  
> Created at: 2022-04-23 21:46:29 UTC  
> Url: https://github.com/boostorg/json/issues/697#issuecomment-1107654200  

The problem is clearly linked to libc++, because I cannot reproduce it unless I add `-stdlib=libc++` (which isn't default on my system).

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-04-23 22:00:46 UTC  
> Url: https://github.com/boostorg/json/issues/697#issuecomment-1107655966  

What is the `std::string` growth policy of libc++ compared to clang?  
  
See: https://github.com/boostorg/json/blob/83c364afaf31b58cfe8b97ab159c406c508f47c9/include/boost/json/impl/serialize.ipp#L52

---

## Comment 3

> Username: madmongo1  
> Created at: 2022-04-23 22:02:22 UTC  
> Url: https://github.com/boostorg/json/issues/697#issuecomment-1107656172  

I use `-stdlib=libc++` with clang as it's seemingly the only way to get coroutine support.

---

## Comment 4

> Username: sehe  
> Created at: 2022-04-23 22:04:04 UTC  
> Url: https://github.com/boostorg/json/issues/697#issuecomment-1107656382  

Whatever it is, it's taking 96% of execution (even if I reserve the output string to 4'472'628 in serialize.ipp, so it's intermediate strings?).  
![image](https://user-images.githubusercontent.com/324097/164947406-6554d5c5-27bf-4131-87ef-87bec097db90.png)

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-04-23 22:07:22 UTC  
> Url: https://github.com/boostorg/json/issues/697#issuecomment-1107656762  

libc++ `reserve( capacity() + 1 )` grows capacity 16 bytes at a time

---

## Comment 6

> Username: sehe  
> Created at: 2022-04-23 22:15:55 UTC  
> Url: https://github.com/boostorg/json/issues/697#issuecomment-1107657811  

```  
parsing  
...serialising  
...serialised: 4472628 chars  
  
real    0m0,079s  
user    0m0,059s  
sys     0m0,020s  
  
```

---

## Comment 7

> Username: sehe  
> Created at: 2022-04-23 22:16:50 UTC  
> Url: https://github.com/boostorg/json/issues/697#issuecomment-1107657928  

And no leaks reported (valgrind)   
  
`==9036== All heap blocks were freed -- no leaks are possible  
`

---

## Comment 8

> Username: grisumbras  
> Created at: 2022-05-15 08:25:32 UTC  
> Url: https://github.com/boostorg/json/issues/697#issuecomment-1126885577  

Fixed in 5ca4fb363e39822ae1e3013ce6f2ce6106076fe7
