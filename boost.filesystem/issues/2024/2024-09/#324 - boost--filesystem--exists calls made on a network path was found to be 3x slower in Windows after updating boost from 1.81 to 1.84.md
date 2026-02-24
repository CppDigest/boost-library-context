# #324 - boost::filesystem::exists calls made on a network path was found to be 3x slower in Windows after updating boost from 1.81 to 1.84. [Open]

> Username: SajasKK  
> Created at: 2024-09-10 13:45:07 UTC  
> Updated at: 2024-09-11 09:50:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/324  

In our code we have some calls made to boost/filesystem/operations methods. One of these calls was repeatedly checking the existence of some files. After an update from boost 1.81 to 1.84 it was noticed that these calls have become about 3 to 4 times slower in Windows.  
I checked the same on a simple test application which performs a call to boost::filesystem::exists on a network path(formatted as "\\\\NETWORK_DRIVE\\FILEPATH") for about a 1000 times. The timing that I got when using 1.81 and 1.84 versions showed a difference of 3.4 times.  
I am building the test application using **Visual Studio Professional 2022(64 bit) Version 17.10.6(Visual C++ 2022)**  
And running it on a **Windows 11 Pro OS**  
  
The test app that I was running(while using 1.81 and 1.84) separately was following:  
```  
#include <boost/filesystem/path.hpp>  
#include <boost/filesystem/operations.hpp>  
#include <iostream>  
#include <chrono>  
  
int main()  
{  
	boost::system::error_code ec;  
	auto pathString = std::string("\\\\NETWORK_DRIVE\\FILEPATH");  
  
	std::size_t totalTime = 0;  
	for (int i = 0; i < 1000; ++i)  
	{  
		auto startTime = std::chrono::high_resolution_clock::now();  
		auto path = boost::filesystem::path(pathString);  
		boost::filesystem::exists(path, ec);  
		auto endTime = std::chrono::high_resolution_clock::now();  
		auto elapsedTime = std::chrono::duration_cast<std::chrono::milliseconds>(endTime - startTime).count();  
		totalTime += elapsedTime;  
		std::cout << "TimeElapsed : " << elapsedTime << "\n";  
	}  
	std::cout << "TotalTimeElapsed : " << totalTime << "\n";  
	return 0;  
}  
```  
  
Is this an expected performance degradation due to some required fixes or could this be improved?

---

## Comment 1

> Username: Lastique  
> Created at: 2024-09-10 17:31:27 UTC  
> Url: https://github.com/boostorg/filesystem/issues/324#issuecomment-2341558745  

I do not see changes to `status` (on which `exists` is based) between 1.81 and 1.84 except https://github.com/boostorg/filesystem/commit/1db4474d1ae7512af06f63156352f2ca5db07e35. I don't know if adding `FILE_READ_EA` caused a performance regression, but if so, that change was necessary for fixing incorrect behavior on SMBv1. Can you test if removing `FILE_READ_EA` affects performance in your case?  
  
As to improving performance, technically, we don't need the entire `file_status` just to test if the file exists. Perhaps, there is a cheaper way to do it, but I don't readily know it, and it may depend on the actual underlying filesystem.

---

## Comment 2

> Username: SajasKK  
> Created at: 2024-09-11 09:50:53 UTC  
> Url: https://github.com/boostorg/filesystem/issues/324#issuecomment-2343172300  

> I do not see changes to `status` (on which `exists` is based) between 1.81 and 1.84 except [1db4474](https://github.com/boostorg/filesystem/commit/1db4474d1ae7512af06f63156352f2ca5db07e35). I don't know if adding `FILE_READ_EA` caused a performance regression, but if so, that change was necessary for fixing incorrect behavior on SMBv1. Can you test if removing `FILE_READ_EA` affects performance in your case?  
>   
> As to improving performance, technically, we don't need the entire `file_status` just to test if the file exists. Perhaps, there is a cheaper way to do it, but I don't readily know it, and it may depend on the actual underlying filesystem.  
  
Thank you. I tried your suggestion and ran the test application after removing FILE_READ_EA flags from the status_impl methods( symlink_status_impl and  status_impl). That brings the speed back to how it was in 1.81.
