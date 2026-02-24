# #174 - Feature: set and change directory Permissions [Closed]

> Username: navidcity  
> Created at: 2021-01-19 14:51:11 UTC  
> Updated at: 2021-01-20 14:33:43 UTC  
> Closed at: 2021-01-19 15:19:16 UTC  
> Url: https://github.com/boostorg/filesystem/issues/174  

Hi, I'm using `1.65.1.0ubuntu1`.  
I want to change the permissions of directories and set permissions when I create directory:  
```c++  
boost::filesystem::create_direcory()  
boost::filesystem::create_directories() //all the directories recursively  
```  
As far I know `boost::filesystem::permissions()` works just for files.  
I don't file to use `system` and `exec` function.  
How is that possible to do that with boost?

---

## Comment 1

> Username: Lastique  
> Created at: 2021-01-19 15:19:16 UTC  
> Url: https://github.com/boostorg/filesystem/issues/174#issuecomment-762910459  

As far as I can see, `permissions` should work for directories.

---

## Comment 2

> Username: navidcity  
> Created at: 2021-01-20 06:49:10 UTC  
> Updated at: 2021-01-20 06:50:46 UTC  
> Url: https://github.com/boostorg/filesystem/issues/174#issuecomment-763379662  

![image](https://user-images.githubusercontent.com/51493483/105135917-082c8980-5b06-11eb-8ffa-dbddf3c1c5e3.png)  
But here in the documentation it is mentioned that the `permissions` method works just for files.  
Besides this code of mine does not work properly. Actually apache with the `www-data` user does not have access to the `IM_ADDRESS` directory to save images in it.  
I run my code  with root access: `sudo ./output`  
  
```c++  
if(!boost::filesystem::is_directory(IM_ADDRESS))  
{  
    if(!boost::filesystem::create_directories(IM_ADDRESS))  
    {  
        LogText = "Store File Thread: Directory " + IM_ADDRESS + " Cannot be created !";  
        SHOW_ERROR(LogText);  
    }  
    boost::filesystem::permissions(IM_ADDRESS, boost::filesystem::owner_all |boost::filesystem::group_read | boost::filesystem::others_read);  
}  
```

---

## Comment 3

> Username: Lastique  
> Created at: 2021-01-20 08:10:25 UTC  
> Url: https://github.com/boostorg/filesystem/issues/174#issuecomment-763418567  

> But here in the documentation it is mentioned that the `permissions` method works just for files.  
  
A directory is just a special kind of file.  
  
> Besides this code of mine does not work properly.  
  
The code sample you posted changes permissions for the `IM_ADDRESS` directory to allow other users to *read* its contents, not to *write*. You also want "execution" permission to allow users to iterate over files in the directory.

---

## Comment 4

> Username: navidcity  
> Created at: 2021-01-20 12:00:37 UTC  
> Url: https://github.com/boostorg/filesystem/issues/174#issuecomment-763556647  

Ach, sorry. I put a wrong snippet of code.   
  
```c++  
if(!boost::filesystem::is_directory(IM_ADDRESS))  
{  
    if(!boost::filesystem::create_directories(IM_ADDRESS))  
    {  
        LogText = "Store File Thread: Directory " + IM_ADDRESS + " Cannot be created !";  
        SHOW_ERROR(LogText);  
    }  
    boost::filesystem::permissions(IM_ADDRESS, boost::filesystem::owner_all |boost::filesystem::group_all | boost::filesystem::others_all);  
}  
```  
  
Above is the right code witch does not work properly. Any idea about that?

---

## Comment 5

> Username: navidcity  
> Created at: 2021-01-20 13:07:39 UTC  
> Updated at: 2021-01-20 13:36:51 UTC  
> Url: https://github.com/boostorg/filesystem/issues/174#issuecomment-763594477  

Below are a sample code and screenshots of the terminal. It seems that `boost::filesystem::permissions` does not change the permissions when the code itself is run with root access(`sudo`).  
```c++  
#include <boost/filesystem.hpp>  
#include <iostream>  
  
int main()  
{  
    auto IM_ADDRESS {std::string("/home/navidcity/Desktop/test/fortest/")};  
    if(!boost::filesystem::is_directory(IM_ADDRESS))  
    {  
        if(!boost::filesystem::create_directories(IM_ADDRESS))  
        {  
            std::cout << "Store File Thread: Directory " << IM_ADDRESS << " Cannot be created !" << std::endl;  
        }  
        boost::filesystem::permissions(IM_ADDRESS, boost::filesystem::all_all);  
    }  
    return 0;  
}  
```  
running the binary:  
![image](https://user-images.githubusercontent.com/51493483/105178345-21046180-5b3d-11eb-8696-74a675ca7636.png)  
  
  
ll command on linux:  
![image](https://user-images.githubusercontent.com/51493483/105178501-56a94a80-5b3d-11eb-85da-5abee682e4be.png)  
  
As you can see group and other does not have write permission.  
Neither  `boost::filesystem::all_all` nor `boost::filesystem::owner_all | boost::filesystem::group_all | boost::filesystem::others_all` does not perform the action properly.   
  
  
PS:It is important to mention that when I run the binary without `sudo` all the permissions are granted to owner, group and others.

---

## Comment 6

> Username: Lastique  
> Created at: 2021-01-20 14:33:42 UTC  
> Url: https://github.com/boostorg/filesystem/issues/174#issuecomment-763646719  

Try specifying "/home/navidcity/Desktop/test/fortest" to `permissions` call (note there is no trailing slash). "/home/navidcity/Desktop/test/fortest/" is equivalent to "/home/navidcity/Desktop/test/fortest/.", for which I don't think you can change permissions.
