# #73 - fixed antora menu on mobile [Closed]

> Username: rbbeeston  
> Created at: 2025-01-07 21:15:14 UTC  
> Updated at: 2025-01-10 23:12:24 UTC  
> Closed at: 2025-01-10 23:12:24 UTC  
> Url: https://github.com/boostorg/boostlook/issues/73  

on mobile the toolbar menu on mobile is scrolling rather than fixed to the top. needs the following:  
.toolbar {  
...  
    width: 93%;  
    position: fixed;  
    top: 0;  
    padding-top: 10px;  
}  
  
![Image](https://github.com/user-attachments/assets/40258bea-0a71-4342-807f-ba43bebaaf1f)
