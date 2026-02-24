# #942 - BCP Not Building It Contains Deprecated Methods [Open]

> Username: BowenJohnson  
> Created at: 2024-08-20 18:18:59 UTC  
> Updated at: 2024-09-02 09:49:25 UTC  
> Url: https://github.com/boostorg/boost/issues/942  

I am having issues building tools/bcp (running bootstrap then build command, 'b2 threading=multi address-model=64 tools/bcp'). The builds fail because several files still contain deprecated methods. It looks like this issue was addressed in a pull request, [github.com/boostorg/bcp/pull/18/files](url), back in April, but for some reason this looks like it didn't make it into the release for 1.85 or 1.86. Is there a fix for this?

---

## Comment 1

> Username: dtrochow-intel  
> Created at: 2024-08-21 20:38:39 UTC  
> Url: https://github.com/boostorg/boost/issues/942#issuecomment-2302972643  

I am facing the same issue as described by Bowen. I tested it on the [Boost 1.86 Release](https://www.boost.org/users/history/version_1_86_0.html).   
Here are the logs with error messages: [boost_1_86_build.log](https://github.com/user-attachments/files/16698505/boost_1_86_build.log).  
  
**Does anyone know when this issue will be resolved?  
Is there a plan to include a fix for this issue in the next Boost release?**

---

## Comment 2

> Username: BunnySakura  
> Created at: 2024-08-30 09:01:16 UTC  
> Url: https://github.com/boostorg/boost/issues/942#issuecomment-2320562404  

希望尽快修复，谢谢🙏！ | Hope it's fixed soon, thanks 🙏!  
  
```shell  
tools/bcp/copy_path.cpp: In member function ‘void bcp_implementation::copy_path(const boost::filesystem::path&)’:  
tools/bcp/copy_path.cpp:59:18: error: ‘const class boost::filesystem::path’ has no member named ‘branch_path’  
tools/bcp/copy_path.cpp:63:10: error: ‘const class boost::filesystem::path’ has no member named ‘leaf’  
   63 |    if((p.leaf() == "Jamroot") && m_namespace_name.size())  
tools/bcp/add_dependent_lib.cpp:63:8: error: ‘directory_iterator’ is not a member of ‘fs’; did you mean ‘directory_entry’?  
   63 |    fs::directory_iterator i(p);  
……  
```

---

## Comment 3

> Username: Zangetsu38  
> Created at: 2024-09-01 05:10:03 UTC  
> Updated at: 2024-09-01 05:10:21 UTC  
> Url: https://github.com/boostorg/boost/issues/942#issuecomment-2323170723  

I have exactly same issue, like one other dev on 1.86 version  
  
![image](https://github.com/user-attachments/assets/989fbf0f-8d64-4c8f-8c72-73543aa9fdfc)  
  
[message(4).txt](https://github.com/user-attachments/files/16827408/message.4.txt)

---

## Comment 4

> Username: Zangetsu38  
> Created at: 2024-09-02 09:49:24 UTC  
> Url: https://github.com/boostorg/boost/issues/942#issuecomment-2324306757  

ok, for fix it, just replace crazy old version of bcp by last in master...
