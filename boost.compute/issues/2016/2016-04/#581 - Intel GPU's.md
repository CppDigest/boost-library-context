# #581 - Intel GPU's [Closed]

> Username: degski  
> Created at: 2016-04-09 06:16:41 UTC  
> Updated at: 2017-04-24 16:42:11 UTC  
> Closed at: 2017-04-24 16:42:11 UTC  
> Url: https://github.com/boostorg/compute/issues/581  

Contrary what's written in the documentation, the latest SDK/Drivers support OpenCL on Intel integrated HD Graphics GPU's. The "hello world" example reports the Graphics card as the default device.  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2016-04-09 17:27:36 UTC  
> Url: https://github.com/boostorg/compute/issues/581#issuecomment-207819733  

Can you point to which part of the documentation you're referring to? Boost.Compute should work with any conforming OpenCL implementation, including Intel's with both CPU and GPU devices.

---

## Comment 2

> Username: degski  
> Created at: 2016-04-10 04:25:42 UTC  
> Url: https://github.com/boostorg/compute/issues/581#issuecomment-207914407  

Hi Kyle,  
  
It's at the bottom of this page:  
file:///C:/boost_1_61_0/libs/compute/doc/html/boost_compute/platforms_and_compilers.html  
It says: "  
- Intel (CPUs only)  
  
"  
  
degski  
  
On 9 April 2016 at 20:27, Kyle Lutz notifications@github.com wrote:  
  
> Can you point to which part of the documentation you're referring to?  
> Boost.Compute should work with any conforming OpenCL implementation,  
> including Intel's with both CPU and GPU devices.  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/compute/issues/581#issuecomment-207819733  
  
##   
  
(___/)  
(+'.'+)  
(")_(")  
This is Bunny. Copy and paste Bunny into your  
signature to help him gain world domination!

---

## Comment 3

> Username: pavanky  
> Created at: 2016-04-10 04:30:51 UTC  
> Url: https://github.com/boostorg/compute/issues/581#issuecomment-207914997  

Link to line: https://github.com/boostorg/compute/blob/master/doc/platforms_and_compilers.qbk#L39  
  
I think it should be `CPUs and GPUs on windows only` or something along those lines.  
  
Is there any reason `Apple` is missing from the list ? From what I understand all OpenCL implementations on OSX are from Apple rather than the device manufacturers.

---

## Comment 4

> Username: kylelutz  
> Created at: 2016-04-12 04:11:27 UTC  
> Url: https://github.com/boostorg/compute/issues/581#issuecomment-208694692  

Thanks for the feedback! I've updated the list of supported platforms in PR #583.
