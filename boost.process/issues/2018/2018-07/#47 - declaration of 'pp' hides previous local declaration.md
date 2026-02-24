# #47 - declaration of 'pp' hides previous local declaration [Closed]

> Username: treebeard-66  
> Created at: 2018-07-17 16:37:21 UTC  
> Updated at: 2018-07-18 08:24:43 UTC  
> Closed at: 2018-07-18 08:24:43 UTC  
> Url: https://github.com/boostorg/process/issues/47  

Hey There. Getting a ' pp hides previous declaration' compiler error in:  
  
```  
for (const boost::filesystem::path & pp : path)  
{  
    auto p = pp / filename;  
    for (boost::filesystem::path ext : extensions)  
    {  
        boost::filesystem::path pp = p;  
```  
  
At the bottom off file: boost\process\detail\windows\search_path.hpp  
  
Is this supposed to be a differently named variable, or is that line supposed to set the value of the previously defined?  
  
Cheers

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2018-07-18 08:24:43 UTC  
> Url: https://github.com/boostorg/process/issues/47#issuecomment-405851492  

It's supposed to be a different one and I've fixed the issue already (https://github.com/boostorg/process/blob/master/include/boost/process/detail/windows/search_path.hpp).  
  
And it's a warning, perfectly valid C++ code.
