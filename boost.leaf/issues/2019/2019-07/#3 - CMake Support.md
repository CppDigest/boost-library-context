# #3 - CMake Support [Closed]

> Username: TartanLlama  
> Created at: 2019-07-17 12:11:28 UTC  
> Updated at: 2019-07-18 09:41:42 UTC  
> Closed at: 2019-07-18 09:41:42 UTC  
> Url: https://github.com/boostorg/leaf/issues/3  

Ideally CMake users would be able to use the library like so:  
  
```cmake  
find_package(leaf REQUIRED)  
target_link_libraries(mytarget leaf::leaf) #or zajo::leaf if you prefer  
```  
  
Would you consider a PR to add this functionality? I'd then easily be able to also create a vcpkg package.

---

## Comment 1

> Username: zajo  
> Created at: 2019-07-17 20:39:05 UTC  
> Url: https://github.com/boostorg/leaf/issues/3#issuecomment-512559471  

On Wed, Jul 17, 2019 at 5:11 AM Simon Brand <notifications@github.com>  
wrote:  
>  
> Ideally CMake users would be able to use the library like so:  
>  
> find_package(leaf REQUIRED)  
> target_link_libraries(mytarget leaf::leaf) #or zajo::leaf if you prefer  
>  
> Would you consider a PR to add this functionality? I'd then easily be  
able to also create a vcpkg package.  
  
Yes please. Thanks.
