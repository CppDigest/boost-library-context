# #338 - Add `CL_DEVICE_MAX_WORK_ITEM_SIZES` to class device [Closed]

> Username: adamcavendish  
> Created at: 2014-12-10 13:59:43 UTC  
> Updated at: 2014-12-10 16:53:10 UTC  
> Closed at: 2014-12-10 16:53:10 UTC  
> Url: https://github.com/boostorg/compute/issues/338  

CL_DEVICE_MAX_WORK_ITEM_SIZES is a good reference for people to start up work-items, which is indicated in `local work size` in `enqueue_nd_range_kernel`.

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-12-10 16:34:26 UTC  
> Url: https://github.com/boostorg/compute/issues/338#issuecomment-66480021  

You should be able to use the generic [`device::get_info()`](http://kylelutz.github.io/compute/boost/compute/device.html) function to do this already:  
  
```  
std::vector<size_t> max_sizes = device.get_info<CL_DEVICE_MAX_WORK_ITEM_SIZES>();  
```

---

## Comment 2

> Username: adamcavendish  
> Created at: 2014-12-10 16:37:54 UTC  
> Url: https://github.com/boostorg/compute/issues/338#issuecomment-66480614  

Yeap, I know `device::get_info()`. Don't you consider it worth adding APIs like `x_max_work_items()`, `y_max_work_items()` and `z_max_work_items()` ?

---

## Comment 3

> Username: kylelutz  
> Created at: 2014-12-10 16:45:21 UTC  
> Updated at: 2014-12-10 16:45:47 UTC  
> Url: https://github.com/boostorg/compute/issues/338#issuecomment-66481917  

I don't think it would be a good idea to add every device info value as a separate method to the `device` class (there are already over 80 different info enums for `clGetDeviceInfo()`). This would increase the size of the public interface for `device` dramatically. I've added a few of the very common cases (like `name()` and `type()`) in order to make common code a little more concise but I'd rather have people use the generic function for most of the device info queries.

---

## Comment 4

> Username: adamcavendish  
> Created at: 2014-12-10 16:53:10 UTC  
> Url: https://github.com/boostorg/compute/issues/338#issuecomment-66483248  

Ok, now I understand. Thx~
