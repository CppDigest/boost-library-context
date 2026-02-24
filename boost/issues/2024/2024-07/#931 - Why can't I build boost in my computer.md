# #931 - Why can't I build boost in my computer? [Open]

> Username: ZhikangLai  
> Created at: 2024-07-26 05:28:09 UTC  
> Updated at: 2025-08-31 04:48:35 UTC  
> Url: https://github.com/boostorg/boost/issues/931  

![figure1](https://github.com/user-attachments/assets/2cbf66c9-c655-4e68-97dd-d6ca507ac477)  
![figure2](https://github.com/user-attachments/assets/d6a5b34b-0f39-48a0-8449-141b6afff5e9)  
![figure3](https://github.com/user-attachments/assets/2aaa5a9d-c7c7-40b6-aed2-5d95258cf47f)  
![figure4](https://github.com/user-attachments/assets/e15c4c57-7b10-45fe-8898-9a1d1d21e96a)  
![figure5](https://github.com/user-attachments/assets/aca5ee62-c8dc-4cf8-8176-b25c097ca266)  
![figure6](https://github.com/user-attachments/assets/4365ecc4-9261-48dd-a74b-06784a55ff90)  
![figure7](https://github.com/user-attachments/assets/ad65c262-d5a8-431a-8c8d-cf0f15cfabd1)  
![figure8](https://github.com/user-attachments/assets/a33b1238-dd46-426f-9477-a5266b353270)  
I can't not see any .lib in lib folder, Why?  
![figure9](https://github.com/user-attachments/assets/a3d9d41a-c8ed-487b-a995-856d360e86a3)

---

## Comment 1

> Username: m1mist  
> Created at: 2024-07-26 06:27:44 UTC  
> Url: https://github.com/boostorg/boost/issues/931#issuecomment-2252063418  

我换成1.83版本就编译过了

---

## Comment 2

> Username: ZhikangLai  
> Created at: 2024-07-26 06:30:53 UTC  
> Url: https://github.com/boostorg/boost/issues/931#issuecomment-2252067017  

[build_output.txt](https://github.com/user-attachments/files/16387401/build_output.txt)  
This file recored the whole message I built boost 1.85 version.

---

## Comment 3

> Username: ZhikangLai  
> Created at: 2024-07-26 14:19:17 UTC  
> Updated at: 2024-07-26 14:20:34 UTC  
> Url: https://github.com/boostorg/boost/issues/931#issuecomment-2252875001  

I have already fixed this issue.  Take the attach file to replace ".\boost_1_85_0\tools\build\src\tools\msvc.jam".  
[msvc.zip](https://github.com/user-attachments/files/16393003/msvc.zip)  
  
Or you can add the code as the figure show:  
![Snipaste_2024-07-26_22-15-26](https://github.com/user-attachments/assets/58371459-99a8-4065-ba8e-f2789791e0e1)

---

## Comment 4

> Username: chilkaditya  
> Created at: 2024-08-11 11:33:34 UTC  
> Url: https://github.com/boostorg/boost/issues/931#issuecomment-2282727638  

hi, @ZhikangLai. Can you please give me the entire steps to build boost  and can use it in my local machine ?  
My system configuration are as follows:  
1. Windows 11 OS  
2. VS code and mingw installed.  
3. Also Qt creator is installed.

---

## Comment 5

> Username: ZhikangLai  
> Created at: 2024-08-12 03:37:23 UTC  
> Url: https://github.com/boostorg/boost/issues/931#issuecomment-2283059744  

> hi, @ZhikangLai. Can you please give me the entire steps to build boost and can use it in my local machine ? My system configuration are as follows:  
>   
> 1. Windows 11 OS  
> 2. VS code and mingw installed.  
> 3. Also Qt creator is installed.  
  
Hi, Mr. @chilkaditya , Which step are you stuck on? Could you share more details about your compiler？If you're using GCC, you might not encounter the same issue I did. However, if you're using MSVC 14.3, it's likely you're facing a similar problem.

---

## Comment 6

> Username: chilkaditya  
> Created at: 2024-08-12 11:29:36 UTC  
> Url: https://github.com/boostorg/boost/issues/931#issuecomment-2283720307  

@ZhikangLai  sure,  
I do not have Microsoft visual studio IDE. I have vscode editor and mingw compiler downloaded separately. After downloading boost 1.85.0, using command prompt i run bootstrap.bat and then ./b2 command, then one bin.v2 folder generated but inside this folder there was libs folder. I  attached image below.  
  
<img width="567" alt="image" src="https://github.com/user-attachments/assets/1dd1b341-c949-4a6e-ba77-006071712dc1">  
<img width="553" alt="image" src="https://github.com/user-attachments/assets/0a8b31c8-71e7-43ed-b3ef-2955ca8748ec">  
<img width="562" alt="image" src="https://github.com/user-attachments/assets/4dd3d16a-34df-4c00-908e-d4b57ba28e7c">  
inside libs folder all folders is empty and I also cant use boost from vs code editor. I add bin.v2 path to environment variable. I am stuck here.

---

## Comment 7

> Username: ZhikangLai  
> Created at: 2024-08-13 04:01:10 UTC  
> Url: https://github.com/boostorg/boost/issues/931#issuecomment-2285296591  

> @ZhikangLai sure, I do not have Microsoft visual studio IDE. I have vscode editor and mingw compiler downloaded separately. After downloading boost 1.85.0, using command prompt i run bootstrap.bat and then ./b2 command, then one bin.v2 folder generated but inside this folder there was libs folder. I attached image below.  
>   
> <img alt="image" width="567" src="https://private-user-images.githubusercontent.com/77826191/357025983-1dd1b341-c949-4a6e-ba77-006071712dc1.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MjM1MjE1NjcsIm5iZiI6MTcyMzUyMTI2NywicGF0aCI6Ii83NzgyNjE5MS8zNTcwMjU5ODMtMWRkMWIzNDEtYzk0OS00YTZlLWJhNzctMDA2MDcxNzEyZGMxLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNDA4MTMlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjQwODEzVDAzNTQyN1omWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTY1ZDRmNTliZjk2NDY4YjZmNmEwMjZjZTc2ZTg3NTExOTM3YWZiYzdiNGI2ODA3ZjVhY2U4M2JiMGY0YWViMmQmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JmFjdG9yX2lkPTAma2V5X2lkPTAmcmVwb19pZD0wIn0.xZfNfIBmbhPwL5MLSN3vwHBaoOsbXlnofxkEETACjnA"> <img alt="image" width="553" src="https://private-user-images.githubusercontent.com/77826191/357026404-0a8b31c8-71e7-43ed-b3ef-2955ca8748ec.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MjM1MjE1NjcsIm5iZiI6MTcyMzUyMTI2NywicGF0aCI6Ii83NzgyNjE5MS8zNTcwMjY0MDQtMGE4YjMxYzgtNzFlNy00M2VkLWIzZWYtMjk1NWNhODc0OGVjLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNDA4MTMlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjQwODEzVDAzNTQyN1omWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPWEyYTE4NDM4OTQ1OTBlOTFmNzhiMzIxYjM1NDVjYjg1MDE4ODYwYzE3ODkwZTEzZmZhMmU0MjZlNDI0Mzk2MDgmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JmFjdG9yX2lkPTAma2V5X2lkPTAmcmVwb19pZD0wIn0.VYA80p1H0qXmJxz4FMI9HW-eRcee2vJ96xwtLElTY7w"> <img alt="image" width="562" src="https://private-user-images.githubusercontent.com/77826191/357026634-4dd3d16a-34df-4c00-908e-d4b57ba28e7c.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MjM1MjE1NjcsIm5iZiI6MTcyMzUyMTI2NywicGF0aCI6Ii83NzgyNjE5MS8zNTcwMjY2MzQtNGRkM2QxNmEtMzRkZi00YzAwLTkwOGUtZDRiNTdiYTI4ZTdjLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNDA4MTMlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjQwODEzVDAzNTQyN1omWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTYzODhhOGIyN2UyMGY5ZDUyZGMxODUzMDFhZDI4MDFmMjAyOTdlMDdiYjdlNDcyMjlkN2RjN2QxNTBlNmMyNDYmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JmFjdG9yX2lkPTAma2V5X2lkPTAmcmVwb19pZD0wIn0.vh1wJ-uQuPy5cs2E9pwvteeeEuilchA1F0hfFfI6SZ0"> inside libs folder all folders is empty and I also cant use boost from vs code editor. I add bin.v2 path to environment variable. I am stuck here.  
  
when you run "b2", have you try this command:  
b2 toolset=gcc  
?

---

## Comment 8

> Username: chris155-hash  
> Created at: 2025-07-06 07:34:05 UTC  
> Url: https://github.com/boostorg/boost/issues/931#issuecomment-3041104076  

非常感谢，一模一样的问题，这个问题搞了我一天，终于解决了。

---

## Comment 9

> Username: nigels-com  
> Created at: 2025-08-31 04:48:35 UTC  
> Url: https://github.com/boostorg/boost/issues/931#issuecomment-3239758733  

Is there anything further needed for this?  
Can this issue be closed?
