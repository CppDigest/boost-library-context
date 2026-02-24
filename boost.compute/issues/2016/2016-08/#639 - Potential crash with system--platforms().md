# #639 - Potential crash with system::platforms() [Closed]

> Username: Naviee  
> Created at: 2016-08-11 08:59:45 UTC  
> Updated at: 2016-08-19 15:57:26 UTC  
> Closed at: 2016-08-19 15:57:23 UTC  
> Url: https://github.com/boostorg/compute/issues/639  

In system::platforms():   
if the clGetPlatformIDs(0,0,&count) call returns count==0, the second call to clGetPlatformIDs crashes.  
The specification says:  
  
> "If platforms is not NULL, the num_entries must be greater than zero. "  
  
And platform_ids is empty of course.  
  
```  
static std::vector<platform> platforms()  
    {  
        cl_uint count = 0;  
        clGetPlatformIDs(0, 0, &count); //If count == 0...  
  
        std::vector<cl_platform_id> platform_ids(count);  
        clGetPlatformIDs(count, &platform_ids[0], 0); //...crashes here  
  
        std::vector<platform> platforms;  
        for(size_t i = 0; i < platform_ids.size(); i++){  
            platforms.push_back(platform(platform_ids[i]));  
        }  
  
        return platforms;  
    }  
```

---

## Comment 1

> Username: Naviee  
> Created at: 2016-08-11 09:03:27 UTC  
> Url: https://github.com/boostorg/compute/issues/639#issuecomment-239108017  

Btw. fixed it currently simply with:  
  
```  
static std::vector<platform> platforms()  
{  
        cl_uint count = 0;  
        clGetPlatformIDs(0, 0, &count);  
  
        std::vector<platform> platforms;  
        if(count > 0)   
        {  
            std::vector<cl_platform_id> platform_ids(count);  
            clGetPlatformIDs(count, &platform_ids[0], 0);  
  
            for(size_t i = 0; i < platform_ids.size(); i++) {  
                platforms.push_back(platform(platform_ids[i]));  
            }  
        }  
        return platforms;  
    }  
```

---

## Comment 2

> Username: jszuppe  
> Created at: 2016-08-11 09:13:13 UTC  
> Url: https://github.com/boostorg/compute/issues/639#issuecomment-239109961  

Yeah, you're right. You can push this fix, or I'll do it later today.

---

## Comment 3

> Username: jszuppe  
> Created at: 2016-08-18 18:48:56 UTC  
> Url: https://github.com/boostorg/compute/issues/639#issuecomment-240819409  

Fixed in https://github.com/boostorg/compute/pull/640. Can be closed.
