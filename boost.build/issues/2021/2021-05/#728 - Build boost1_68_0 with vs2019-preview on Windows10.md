# #728 - Build boost1_68_0 with vs2019-preview on Windows10 [Closed]

> Username: liam31012  
> Created at: 2021-05-27 13:29:03 UTC  
> Updated at: 2021-05-28 08:52:45 UTC  
> Closed at: 2021-05-28 08:52:45 UTC  
> Url: https://github.com/boostorg/build/issues/728  

The logs say `can not find the path,'.\build.bat' is not a command.....`  
![cmd](https://user-images.githubusercontent.com/30792256/119833631-d8691480-bf31-11eb-8dac-c4abbc9597f6.png)  
But I found there was a file named 'build.bat` in the folder.   
![bat](https://user-images.githubusercontent.com/30792256/119834266-61804b80-bf32-11eb-9ade-839761614d16.png)  
How can I fix this ?

---

## Comment 1

> Username: liam31012  
> Created at: 2021-05-27 14:16:27 UTC  
> Url: https://github.com/boostorg/build/issues/728#issuecomment-849672407  

I fix it! I got the latest version 1_76_0.  
Comment out `.\build.bat` ,  just `build.bat`  
Here is the solution:   
![ccc](https://user-images.githubusercontent.com/30792256/119841946-d3f42a00-bf38-11eb-997b-cfb20f363aad.png)
