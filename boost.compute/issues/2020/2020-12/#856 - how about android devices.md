# #856 - how about android devices [Closed]

> Username: lligen  
> Created at: 2020-12-11 09:44:45 UTC  
> Updated at: 2020-12-31 07:29:48 UTC  
> Closed at: 2020-12-31 07:29:48 UTC  
> Url: https://github.com/boostorg/compute/issues/856  

hello,  
  
is this project support cross compile for android devices?

---

## Comment 1

> Username: lligen  
> Created at: 2020-12-11 09:56:16 UTC  
> Url: https://github.com/boostorg/compute/issues/856#issuecomment-743096365  

such as Qualcomm/mtk GPU.

---

## Comment 2

> Username: ksrikar1234  
> Created at: 2020-12-18 14:54:19 UTC  
> Url: https://github.com/boostorg/compute/issues/856#issuecomment-748127157  

> hello,  
>   
> is this project support cross compile for android devices?  
  
Yes it will. If ur android mobile has OpenCL driver support. Some vendor do not include one. Samsung and few others however does include OpenCL driver in their OS. U should check if normal OpenCL code runs or not. If yes u can use boost compute.
