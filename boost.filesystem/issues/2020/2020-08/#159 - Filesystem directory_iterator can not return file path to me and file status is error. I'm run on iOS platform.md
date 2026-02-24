# #159 - Filesystem directory_iterator can not return file path to me and file status is error. I'm run on iOS platform. [Closed]

> Username: onedou  
> Created at: 2020-08-27 02:07:24 UTC  
> Updated at: 2020-09-03 08:43:41 UTC  
> Closed at: 2020-09-03 08:43:41 UTC  
> Url: https://github.com/boostorg/filesystem/issues/159  

![image](https://user-images.githubusercontent.com/3422640/91375261-c3e5d780-e84c-11ea-9e3e-344bb84db477.png)

---

## Comment 1

> Username: onedou  
> Created at: 2020-08-27 02:10:07 UTC  
> Url: https://github.com/boostorg/filesystem/issues/159#issuecomment-681300036  

error in boost version 1.74.0 and 1.73.0  
1.69.0 work for me

---

## Comment 2

> Username: Lastique  
> Created at: 2020-08-27 08:50:20 UTC  
> Url: https://github.com/boostorg/filesystem/issues/159#issuecomment-681812130  

1. Please, don't post screenshots of your code, they are not helpful. Post code in plain text instead.  
2. Please, post a minimal compilable code sample that reproduces the problem.  
3. If you're getting a default-constructed `directory_entry` from `directory_iterator` then that is most probably a result of a runtime error. In this case you should inspect the `error_code` you're getting.

---

## Comment 3

> Username: onedou  
> Created at: 2020-08-27 09:37:03 UTC  
> Url: https://github.com/boostorg/filesystem/issues/159#issuecomment-681839629  

Ok, Thank you, I'll keep looking for the problem. If there's progress, I'll send a message to here

---

## Comment 4

> Username: onedou  
> Created at: 2020-08-30 15:52:12 UTC  
> Url: https://github.com/boostorg/filesystem/issues/159#issuecomment-683436878  

I build a empty project. but that seems like origin project.  
  
![image](https://user-images.githubusercontent.com/3422640/91663507-cdf12a00-eb1b-11ea-964d-13ff7e54fb6d.png)

---

## Comment 5

> Username: onedou  
> Created at: 2020-08-30 15:53:23 UTC  
> Updated at: 2020-08-30 15:54:34 UTC  
> Url: https://github.com/boostorg/filesystem/issues/159#issuecomment-683437001  

m_perms is no_perms. Should I set some options on xcode project settings?

---

## Comment 6

> Username: Lastique  
> Created at: 2020-08-30 18:15:05 UTC  
> Url: https://github.com/boostorg/filesystem/issues/159#issuecomment-683452478  

As I said before, I have no idea what is going on or what the problem might be without a compilable reproducer code sample.

---

## Comment 7

> Username: onedou  
> Created at: 2020-08-31 01:35:48 UTC  
> Updated at: 2020-08-31 01:43:18 UTC  
> Url: https://github.com/boostorg/filesystem/issues/159#issuecomment-683507375  

windows and mac os are normal.Error  just on iOS system

---

## Comment 8

> Username: onedou  
> Created at: 2020-08-31 01:40:29 UTC  
> Url: https://github.com/boostorg/filesystem/issues/159#issuecomment-683508329  

The source code is here. https://github.com/LiXizhi/NPLRuntime/tree/cp_old/NPLRuntime/Platform/iOS

---

## Comment 9

> Username: Lastique  
> Created at: 2020-08-31 08:38:28 UTC  
> Url: https://github.com/boostorg/filesystem/issues/159#issuecomment-683647601  

That is not a minimal repro, I cannot test it.  
  
Did you debug? What does `readdir` return? What is the directory structure you're iterating over?

---

## Comment 10

> Username: onedou  
> Created at: 2020-08-31 10:52:28 UTC  
> Url: https://github.com/boostorg/filesystem/issues/159#issuecomment-683707196  

/Users/xxxxx/Library/Developer/CoreSimulator/Devices/xxx-xxx-xxxx/data/Containers/Data/Application/xxxxxx-xxxxx-xxxxxx/Documents

---

## Comment 11

> Username: onedou  
> Created at: 2020-08-31 11:00:49 UTC  
> Updated at: 2020-08-31 11:01:35 UTC  
> Url: https://github.com/boostorg/filesystem/issues/159#issuecomment-683710353  

1. That can create folders and files.  
2. I run my simple project on iOS simulator. The `m_value` is 256 and the `m_perms` is owner_read.  
  
My simple project is here.  
http://service.schoolchat.org:10180/big/ios-app

---

## Comment 12

> Username: Lastique  
> Created at: 2020-08-31 14:13:18 UTC  
> Url: https://github.com/boostorg/filesystem/issues/159#issuecomment-683804677  

> My simple project is here.  
  
I cannot run that. A minimal repro is a single .cpp file, preferably with a single function `main()` that reproduces the problem.  
  
I also don't have an iOS environment, I can only test on Linux.  
  
> What does `readdir` return? What is the directory structure you're iterating over?

---

## Comment 13

> Username: onedou  
> Created at: 2020-09-01 00:59:38 UTC  
> Url: https://github.com/boostorg/filesystem/issues/159#issuecomment-684127939  

OK,Thanks a lot.I think that maybe iOS SDK problem, but boost 1.69.0 is correct.

---

## Comment 14

> Username: Lastique  
> Created at: 2020-09-02 12:41:07 UTC  
> Url: https://github.com/boostorg/filesystem/issues/159#issuecomment-685707959  

Also check if you are affected by https://github.com/boostorg/filesystem/issues/147.

---

## Comment 15

> Username: onedou  
> Created at: 2020-09-03 05:34:23 UTC  
> Url: https://github.com/boostorg/filesystem/issues/159#issuecomment-686264119  

> Also check if you are affected by #147.  
  
Thank you. That work for me now!
