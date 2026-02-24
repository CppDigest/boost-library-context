# #802 - Mutiple GPUs [Open]

> Username: lovingxiuxiu  
> Created at: 2018-12-19 02:07:24 UTC  
> Updated at: 2018-12-20 08:34:33 UTC  
> Url: https://github.com/boostorg/compute/issues/802  

Is there an example that  multiple GPUS are used? The current examples are all using "default_device".

---

## Comment 1

> Username: jszuppe  
> Created at: 2018-12-19 10:57:16 UTC  
> Url: https://github.com/boostorg/compute/issues/802#issuecomment-448554048  

https://github.com/boostorg/compute/blob/master/example/list_devices.cpp This examples shows how to list devices from all OpenCL platforms.  
  
https://github.com/boostorg/compute/blob/master/example/random_walk.cpp - If have your devices, for each of them you can create context and queue. If devices are on the same platform you should be able to create one context for both of them (example: https://github.com/boostorg/compute/blob/master/test/test_context.cpp#L53), and then queue for each. If they are not on the same platform, then you need one context per device.  
  
Then you can use multiple devices (GPUs). Remember to pass correct command queue to Boost.Compute's algorithms.

---

## Comment 2

> Username: lovingxiuxiu  
> Created at: 2018-12-19 11:13:36 UTC  
> Url: https://github.com/boostorg/compute/issues/802#issuecomment-448558458  

hi,@jszuppe,I have 2 GPUS on the same platform, I have created one context for both of them and one queue for each,created separate kernels and allocated separate device buffers for each device,depart the input data into two parts for each kernel,then excute the two kernels simultaneously.  
But the result is that two GPUS costs twice as one GPU. Do you have any idea about it?Thank you

---

## Comment 3

> Username: jszuppe  
> Created at: 2018-12-19 11:20:01 UTC  
> Url: https://github.com/boostorg/compute/issues/802#issuecomment-448560226  

How big is your input data? Maybe queuing a kernel costs more than execution on a GPU. I'd recommend testing two transform algorithms on large data (for example 128MB of ints). Also make sure that there is no sync between kernel calls (no blocking API call), otherwise they are not run at the same time, but sequentially.

---

## Comment 4

> Username: lovingxiuxiu  
> Created at: 2018-12-20 02:40:03 UTC  
> Url: https://github.com/boostorg/compute/issues/802#issuecomment-448838111  

As you said,I found that getting devices,creating context and queue cost more than transferring data and execution on a GPU.And I found that the APIs mentioned cost much more(more than 10 times) on a server with 2 Navidia V100 GPUS than on a laptop with a  NVIDIA NVS 5400m GPU.

---

## Comment 5

> Username: lovingxiuxiu  
> Created at: 2018-12-20 03:11:12 UTC  
> Url: https://github.com/boostorg/compute/issues/802#issuecomment-448843345  

laptop	server  
get device(first time)	0.005s	1.9s  
create context	            0.005259s	0.36s  
create queue	              0.000171s	0.000058s

---

## Comment 6

> Username: jszuppe  
> Created at: 2018-12-20 08:34:26 UTC  
> Url: https://github.com/boostorg/compute/issues/802#issuecomment-448916080  

I doubt it's a Boost.Compute issue. It's more like NVIDIA OpenCL platform thing.
