# #101 remove .lnk, .suo and .user files [Closed]

> Username: cenit  
> Created at: 2018-01-19 16:29:16 UTC  
> Updated at: 2018-01-21 11:13:33 UTC  
> Closed at: 2018-01-21 10:09:46 UTC  
> Url: https://github.com/boostorg/math/pull/101  

Starting from an error I was receiving in a Windows installation under Parallels  
```  
CMake Error: Problem with archive_write_header(): Can't stat existing object  
CMake Error: Current file: math-boost-1.66.0/dot_net_example/boost_math/boost_math.sln.lnk  
CMake Error: Problem extracting tar: .\boostorg-math-boost-1.66.0.tar.gz  
```  
I decided to look at unused (and typically unshared) files inside the repository and removed them.  
I don't know why .lnk files have problems to be untarred with the command  
```  
cmake -E tar xjf boostorg-math-boost-1.66.0.tar.gz  
```  
but since the file itself is unused, I think that removing it should be the least effort solution.  
  
Please let me know if any other modification is required for acceptance

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2018-01-21 10:09:46 UTC  
> Url: https://github.com/boostorg/math/pull/101#issuecomment-359237213  

The link files can go, but not the Visual Studio project files, I've updated these and hopefully fixed your issue in https://github.com/boostorg/math/commit/bac580583bbfa1967f5aa61f9163e75bc2f07482

---

## Comment 2

> Username: cenit  
> Created_at: 2018-01-21 11:10:47 UTC  
> Updated_at: 2018-01-21 11:13:33 UTC  
> Url: https://github.com/boostorg/math/pull/101#issuecomment-359240488  

.suo and .user files are not Visual Studio project files.  
Only .*proj (not .*proj.user) files and .sln files are noteworthy of distributions! The ones I was removing are files user/machine dependent that are recreated if missing every time vs opens :). They contain visualisation preferences, indexes for faster performances, etc.  
As you can see also in Visual Studio defaults, those kind of extensions are suggested to put in the .gitignore!  
Anyway, the .lnk are gone, at least the problem with using cmake to untar the repo should be gone

---
