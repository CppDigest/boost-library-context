# #58 - recursive_directory_iterator noexcept constructor may call std::terminate [Closed]

> Username: ermakov-i-o  
> Created at: 2017-10-30 14:55:27 UTC  
> Updated at: 2018-11-24 16:01:04 UTC  
> Closed at: 2018-11-24 16:01:04 UTC  
> Url: https://github.com/boostorg/filesystem/issues/58  

[recursive_directory_iterator constructor](https://github.com/boostorg/filesystem/blob/develop/include/boost/filesystem/operations.hpp#L1173) marked as noexcept but `new detail::recur_dir_itr_imp` may throw std::bad_alloc
