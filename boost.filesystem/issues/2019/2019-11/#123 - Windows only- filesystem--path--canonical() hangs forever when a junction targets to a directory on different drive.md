# #123 - Windows only: filesystem::path::canonical() hangs forever when a junction targets to a directory on different drive [Closed]

> Username: linux-is-beste  
> Created at: 2019-11-14 13:36:32 UTC  
> Updated at: 2020-05-02 17:17:50 UTC  
> Closed at: 2020-05-02 17:17:50 UTC  
> Url: https://github.com/boostorg/filesystem/issues/123  

Windows only ...  
  
filesystem::path::canonical() hangs forever when a junction points to a different drive  
  
Example:  
  
```  
Drive D:  
mkdir  \MyJunctionTarget  
  
Drive C:  
cd \  
mklink /j C:\MyJunction  D:\MyJunction  
  
```  
now - a program containing   
  
   `boost::filesystem::path p(boost::filesystem::canonical("C:\\MyJunction"));`  
  
never returns (hangs forever). See also attached boost_junction_issue.cpp.  
  
note: tested with libboost v1.71.0. But it is also reproducable with libboost v1.57.0.

---

## Comment 1

> Username: linux-is-beste  
> Created at: 2019-11-14 13:40:13 UTC  
> Updated at: 2019-11-14 13:44:41 UTC  
> Url: https://github.com/boostorg/filesystem/issues/123#issuecomment-553892016  

[boost_junction_issue.cpp.txt](https://github.com/boostorg/filesystem/files/3846720/boost_junction_issue.cpp.txt)

---

## Comment 2

> Username: linux-is-beste  
> Created at: 2019-11-14 16:46:44 UTC  
> Updated at: 2019-11-14 16:55:01 UTC  
> Url: https://github.com/boostorg/filesystem/issues/123#issuecomment-553974838  

note: when the Junction targets to a directory on a different drive but has a different **name** then it does not hang. Instead it indicates on error with a boolean value of false.  
  
```  
Drive D:  
mkdir  \MyJunction_other_name  
  
Drive C:  
cd \  
mklink /j C:\MyJunction  D:\MyJunction_other_name  
  
```  
  
```  
boost_junction_issue.exe   C:\MyJunction  
         boost::filesystem::exists(C:\MyJunction) returned false  
```

---

## Comment 3

> Username: GitEvgen  
> Created at: 2020-04-23 19:49:15 UTC  
> Url: https://github.com/boostorg/filesystem/issues/123#issuecomment-618625857  

It may be losing the drive letter and may be related to https://github.com/boostorg/filesystem/issues/99

---

## Comment 4

> Username: GitEvgen  
> Created at: 2020-04-23 23:54:28 UTC  
> Url: https://github.com/boostorg/filesystem/issues/123#issuecomment-618728921  

Some explanations and sample fixes here: https://svn.boost.org/trac10/ticket/10900

---

## Comment 5

> Username: Lastique  
> Created at: 2020-05-02 17:17:50 UTC  
> Url: https://github.com/boostorg/filesystem/issues/123#issuecomment-622986102  

Probably fixed by https://github.com/boostorg/filesystem/pull/100.
