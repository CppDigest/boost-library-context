# #1307 [examples] Update qt example [Merged]

> Username: barendgehrels  
> Created at: 2024-09-08 11:06:18 UTC  
> Updated at: 2024-09-13 20:01:46 UTC  
> Merged at: 2024-09-13 20:01:41 UTC  
> Closed at: 2024-09-13 20:01:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1307  

Recently I updated wxwidget example.  
Similarly, this PR updates the qt example.  
It's now simple to install on Mac, using brew  
  
The first example `x01` was nearly the same as the other `x06` so the first is omitted.  
  
Example of running it.  
```  
./qt_world_mapper.app/Contents/MacOS/qt_world_mapper ../../../../test/algorithms/buffer/data/gr.wkt  
```  
  
![image](https://github.com/user-attachments/assets/a87d2b3d-a0f0-4e08-8c34-14dfc3a88ddd)

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-09-08 11:06:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1307#pullrequestreview-2288509944  

📁 example/with_external_libs/wxwidgets/wxwidgets_world_mapper.cpp

```diff
  34 | #endif
  35 | 
  36 |+ #include "common/read_countries.hpp"
```

> Username: barendgehrels  
> Created_at: 2024-09-08 11:06:48 UTC  
> Updated_at: 2024-09-08 11:06:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/1307#discussion_r1749188882  

They now share code


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2024-09-10 09:26:01 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1307#pullrequestreview-2291888239  

Great, thanks!  
  
I managed to build and run in Ubuntu 22 (although not that straightforward ;)   
  
First, Vulkan SDK was needed (see [here](https://vulkan.lunarg.com/doc/sdk/1.3.239.0/linux/getting_started_ubuntu.html)).   
  
Then I had several issues with qt local setup which I will not post here because it was probably due to my setup and not applicable to other users.   
  
Something that should be mentioned in this PR is the dependency upgrade from qt4 to qt6. In particular, QT6.3 which introduces `qt_standard_project_setup()` command. For example, ubuntu 22 has QT6.2.4 thus I replaced  `qt_standard_project_setup()` with   
  
```  
set(CMAKE_CXX_STANDARD 14)  
set(CMAKE_CXX_STANDARD_REQUIRED ON)  
set(CMAKE_AUTOMOC ON)  
set(CMAKE_AUTOUIC ON)  
set(CMAKE_AUTORCC ON)  
```

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2024-09-10 19:56:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1307#issuecomment-2341901375  

Thanks for testing! Will update the README and consult you

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2024-09-13 09:15:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/1307#issuecomment-2348450771  

BTW I could build it without vulcan. But also with `brew install vulkan-tools`. Will add that also

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2024-09-13 10:50:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/1307#issuecomment-2348645431  

@vissarion I updated the readme according to your findings. Is this OK?

---

## Comment 6

> Username: vissarion  
> Created_at: 2024-09-13 13:02:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/1307#issuecomment-2348911899  

> BTW I could build it without vulcan. But also with `brew install vulkan-tools`. Will add that also  
  
That could be also possible in ubuntu, I didn't chech, I just show the warning and fix it by installing vulcan.  
  
> @vissarion I updated the readme according to your findings. Is this OK?  
  
Thanks, I am OK!

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2024-09-13 15:23:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/1307#issuecomment-2349213653  

> That could be also possible in ubuntu, I didn't chech, I just show the warning and fix it by installing vulcan.  
  
OK, I rephrase it a bit, adding "might", and merge, thanks!

---
