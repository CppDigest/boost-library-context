# #248 - Current path changes to the first passed argument [Closed]

> Username: memory-hunter  
> Created at: 2022-08-09 19:53:59 UTC  
> Updated at: 2022-08-10 11:14:05 UTC  
> Closed at: 2022-08-10 02:12:05 UTC  
> Url: https://github.com/boostorg/filesystem/issues/248  

OS: Win10  
Version: 1.79.0  
  
Issue:  
  
I am trying to write a program that simply copies over some files to another directory.  
I am using the `boost::filesystem` to do that.  
  
Thing is, whenever I pass no arguments, the current path seems to point at where the executable is.  
But if I try to pass an argument (by dragging and dropping the file over the executable), the current path changes to that of the dropped file.  
  
Is this supposed to happen?

---

## Comment 1

> Username: Lastique  
> Created at: 2022-08-10 02:12:05 UTC  
> Url: https://github.com/boostorg/filesystem/issues/248#issuecomment-1210070171  

Boost.Filesystem has nothing to do with drag&drop, and it doesn't change the current directory of the process unless you explicitly call `current_path` to set it. The initial current path of the process depends on how the process is started, and I frankly have no idea what it is when you drag&drop a file onto the executable. It seems possible that the OS is launching your process from the directory of the dropped file. In any case, this is out of scope of Boost.Filesystem.

---

## Comment 2

> Username: memory-hunter  
> Created at: 2022-08-10 06:02:56 UTC  
> Url: https://github.com/boostorg/filesystem/issues/248#issuecomment-1210199577  

> Boost.Filesystem has nothing to do with drag&drop, and it doesn't change the current directory of the process unless you explicitly call `current_path` to set it. The initial current path of the process depends on how the process is started, and I frankly have no idea what it is when you drag&drop a file onto the executable. It seems possible that the OS is launching your process from the directory of the dropped file. In any case, this is out of scope of Boost.Filesystem.  
  
It may be because of my inexperience with things but I surely am not changing the current path with the argument passed. I am going to have to do some research why does that happen...

---

## Comment 3

> Username: memory-hunter  
> Created at: 2022-08-10 06:16:47 UTC  
> Url: https://github.com/boostorg/filesystem/issues/248#issuecomment-1210209419  

> Boost.Filesystem has nothing to do with drag&drop, and it doesn't change the current directory of the process unless you explicitly call `current_path` to set it. The initial current path of the process depends on how the process is started, and I frankly have no idea what it is when you drag&drop a file onto the executable. It seems possible that the OS is launching your process from the directory of the dropped file. In any case, this is out of scope of Boost.Filesystem.  
  
From what I observed, I tried to pass a file as an argument by calling from PowerShell, when done like that, it doesn't change the current path, but if I just drag and drop a file upon the executable (You said you had no idea about such thing, As far as I know, that is one way to simply start an executable with the dropped thing as the first argument (as in argv[1])). Strange.. As you said, it definitely is an OS thing..

---

## Comment 4

> Username: Lastique  
> Created at: 2022-08-10 10:28:02 UTC  
> Updated at: 2022-08-10 10:28:48 UTC  
> Url: https://github.com/boostorg/filesystem/issues/248#issuecomment-1210478533  

As I said, it all depends on how the process is launched. If your program is e.g. in "C:\\DirA\\proc.exe" and the file is in "C:\\DirB\\file.txt" then you can start it from "C:\\DirA" and specify the full path "C:\\DirB\\file.txt" as an argument (in which case the current path will be "C:\\DirA") or from "C:\\DirB" using the full path "C:\\DirA\\proc.exe" and specify just "file.txt" as an argument (in which case the current path will be "C:\\DirB"). Or you can launch the process from an entirely unrelated directory "D:\\DirC" and use full paths for both the program and the argument (naturally, the current path will be "D:\\DirC" in this case). I don't know what exactly Windows does when you drag&drop, but given your experience it looks like it does it according to the second scenario.  
  
In any case, a good program should be prepared to be launched from an arbitrary directory.

---

## Comment 5

> Username: memory-hunter  
> Created at: 2022-08-10 11:14:05 UTC  
> Url: https://github.com/boostorg/filesystem/issues/248#issuecomment-1210527882  

> As I said, it all depends on how the process is launched. If your program is e.g. in "C:\\DirA\\proc.exe" and the file is in "C:\\DirB\\file.txt" then you can start it from "C:\\DirA" and specify the full path "C:\\DirB\\file.txt" as an argument (in which case the current path will be "C:\\DirA") or from "C:\\DirB" using the full path "C:\\DirA\\proc.exe" and specify just "file.txt" as an argument (in which case the current path will be "C:\\DirB"). Or you can launch the process from an entirely unrelated directory "D:\\DirC" and use full paths for both the program and the argument (naturally, the current path will be "D:\\DirC" in this case). I don't know what exactly Windows does when you drag&drop, but given your experience it looks like it does it according to the second scenario.  
>   
> In any case, a good program should be prepared to be launched from an arbitrary directory.  
  
That is true. I have made sure to correct my code to be proof in any scenario. Thank you for the reply.
