# #237 - boost_1_76_0 collection_size_type copy.hpp filename with additional space? [Open]

> Username: zhmzhen  
> Created at: 2021-06-09 05:41:26 UTC  
> Updated at: 2023-06-06 16:08:22 UTC  
> Url: https://github.com/boostorg/serialization/issues/237  

Hi Dear receiver,  
  
I am new here, thanks for your time and input in advance.  
  
As tittle, recently, I tried to use latest boost   
https://boostorg.jfrog.io/artifactory/main/release/1.76.0/source/boost_1_76_0.tar.gz  
to build on one linux machine, it report error when doing related making due to there is one  strange space character in the filename "collection_size_type copy.hpp"     
**Is that expected or on purpose?**  
It seems not a friendly file name for linux related tool chain operation.  
![image](https://user-images.githubusercontent.com/12193710/121298098-e2254b80-c925-11eb-8eac-65b4186ed7be.png)  
  
Thanks,  
Charley

---

## Comment 1

> Username: zhmzhen  
> Created at: 2021-06-09 05:54:43 UTC  
> Url: https://github.com/boostorg/serialization/issues/237#issuecomment-857400579  

BTW, I have checked the source code of version boost_1_72_0, it is OK for the filename,  
So is that something by mistake?  
  
Thanks,  
Charley

---

## Comment 2

> Username: mclow  
> Created at: 2021-06-09 15:55:23 UTC  
> Url: https://github.com/boostorg/serialization/issues/237#issuecomment-857826189  

I can see it in the 1.76.0 release.  
I see it in the `master` branch.  
I do *not* see it in the `develop` branch.

---

## Comment 3

> Username: robertramey  
> Created at: 2021-06-09 17:05:14 UTC  
> Url: https://github.com/boostorg/serialization/issues/237#issuecomment-857874378  

this is a mistake.  I don't know how I did it, but it shouldn't appear in the master branch.  
  
I think someone pointed it out to me and I removed from develop.  I generally only update the master by doing a "squash merger" from develop and I generally only do that once soon before release.  So it should disappear from the master at the next release.    
  
If you need immediate relief, just remove it from your local copy.  
  
Thanks for pointing this out.

---

## Comment 4

> Username: zhmzhen  
> Created at: 2021-06-10 01:03:55 UTC  
> Url: https://github.com/boostorg/serialization/issues/237#issuecomment-858201882  

Thanks for all your information, it is clear for me,   
  
Besides, I checked this issue in package since version 1_74_0, it is ok in version 1_73_0.  
I can upgrade our boost from 1_72_0 to 1_73_0 for now,  
while just want to know when it will official remove related files later?  eg: 1 month later or in version 1_77_0?  
^_^   
  
Would you please let me know the date if possible, so we can work with latest great boost smoothly.  
  
Thanks,  
Charley

---

## Comment 5

> Username: mzhan0170  
> Created at: 2021-06-11 03:55:38 UTC  
> Url: https://github.com/boostorg/serialization/issues/237#issuecomment-859244103  

Will boost add this issue to release note for 1.76.0? how does boost publish such issues?  
Thanks,  
Mark

---

## Comment 6

> Username: emmataobao  
> Created at: 2021-12-14 01:42:04 UTC  
> Updated at: 2021-12-14 01:46:06 UTC  
> Url: https://github.com/boostorg/serialization/issues/237#issuecomment-993076041  

Hi,  
  
I see this issue still exist in the latest release  1.78.0? do you have plan to fix it?  
Another question, if i want to build 1.78.0, do i remove this file directly?

---

## Comment 7

> Username: icanhasmath  
> Created at: 2023-06-06 16:08:22 UTC  
> Url: https://github.com/boostorg/serialization/issues/237#issuecomment-1579056210  

Hey,  
  
This file is still in release 1.82.0.  The space causes an issue when building a Tensorflow wheel against it:  
  
`cp: cannot stat './local_config_python/python_include/boost-1_80/boost/serialization/collection_size_type': No such file or directory`  
  
I'm removing the file locally, but simply replacing the space with an underscore would make this a non-issue.  
Best,  
Marc
