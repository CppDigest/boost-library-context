# #443 Runtime version checking [Merged]

> Username: bkchr  
> Created at: 2015-03-25 22:03:15 UTC  
> Updated at: 2015-06-23 02:59:53 UTC  
> Merged at: 2015-06-23 02:59:53 UTC  
> Closed at: 2015-06-23 02:59:53 UTC  
> Url: https://github.com/boostorg/compute/pull/443  

Added runtime version checking in the context constructor. We for example compile with an OpenCl 2.0 sdk, but also want to use NVIDIA devices which only support OpenCl 1.1.  
Fixed a bug in the function context.get_device(), you only can get always all devices and not only one device!  
Added a third fix/workaround for a test on NVIDIA.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2015-03-26 03:17:56 UTC  
> Updated_at: 2015-06-04 10:08:09 UTC  
> Url: https://github.com/boostorg/compute/pull/443#discussion_r27185969  

Hmm, was there actually a bug in this code? What errors were you seeing?  
  
The code as it is now only returns the first device. Changing it to call `get_devices()` will perform a dynamic-memory allocation (to create a `std::vector<device>`) which is unnecessary if we only need the first device for the context.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2015-03-26 03:20:02 UTC  
> Updated_at: 2015-06-04 10:08:09 UTC  
> Url: https://github.com/boostorg/compute/pull/443#discussion_r27186039  

Any reason for this change? This will mess up the formatting when used as a source-code snippet in the generated documentation.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2015-03-26 03:21:03 UTC  
> Updated_at: 2015-06-04 10:08:09 UTC  
> Url: https://github.com/boostorg/compute/pull/443#discussion_r27186075  

Does this always work for you? It seems somewhat fragile to me. Ideally this could be implemented without a sleep of some arbitrary amount of time.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2015-03-26 03:25:53 UTC  
> Url: https://github.com/boostorg/compute/pull/443#issuecomment-86323515  

Looks good! I left some comments in-line. I think it would be best to trim this pull-request down to just the change for `context`'s constructor.  
  
Also, when writing the `git commit` message, could you format it something like this:  
  
```  
Add runtime version checking in the context constructor  
  
This adds runtime version checking in the context constructor which fixes  
issues when using an OpenCL 1.x device with OpenCL 2.x headers.  
```  
  
The first line is usually a summary of the change, followed by a blank line, followed by one or more paragraphs describing the change in more detail (usually word-wrapped to ~80 characters). This makes it easier to look over the history from `git log` and `git blame`. Thanks!

---

## Comment 5

> Username: bkchr  
> Created_at: 2015-03-26 12:32:39 UTC  
> Updated_at: 2015-06-04 10:08:09 UTC  
> Url: https://github.com/boostorg/compute/pull/443#discussion_r27208262  

The second call to clGetContextInfo was returning -30(CL_INVALID_VALUE). Somehow the function wants to get the same size of memory allocated it returns in the first call.   
Maybe you can only query always all devices? Do you have any hardware where you could try this?

---

## Comment 6

> Username: bkchr  
> Created_at: 2015-03-26 12:34:07 UTC  
> Updated_at: 2015-06-04 10:08:09 UTC  
> Url: https://github.com/boostorg/compute/pull/443#discussion_r27208333  

Sorry I thought the indentation was missing there, I will revert this!

---

## Comment 7

> Username: bkchr  
> Created_at: 2015-03-26 12:46:29 UTC  
> Updated_at: 2015-06-04 10:08:09 UTC  
> Url: https://github.com/boostorg/compute/pull/443#discussion_r27208949  

Yeah it's a dirty fix. But NVIDIA calls the callback from a different thread and the check is executed before the callback was hit. In this case the test fails, the normal destructor callback tests works without problems. I think that the pure c callback doesn't need so much "computation" until the callback is hit.  
You're right that the arbitrary amount of time isn't a good indicator, do maybe have another idea? Because somehow you have to wait. As long as the callback is asynchronous, I don't know a better way to wait for this callback to get called.

---

## Comment 8

> Username: bkchr  
> Created_at: 2015-03-26 12:48:22 UTC  
> Url: https://github.com/boostorg/compute/pull/443#issuecomment-86498899  

Okay, I will consider this the next time!

---

## Comment 9

> Username: kylelutz  
> Created_at: 2015-03-27 04:29:40 UTC  
> Updated_at: 2015-06-04 10:08:09 UTC  
> Url: https://github.com/boostorg/compute/pull/443#discussion_r27275474  

Interesting, what hardware/platform does this occur on?  
  
Just FYI, both this version and the original work fine on the platforms I have (NVIDIA, AMD, Intel).

---

## Comment 10

> Username: kylelutz  
> Created_at: 2015-03-27 04:33:21 UTC  
> Updated_at: 2015-06-04 10:08:09 UTC  
> Url: https://github.com/boostorg/compute/pull/443#discussion_r27275537  

The way I've dealt with this before is to use a `mutex` and `condition_variable::wait_for()` to wait for the event to complete with a finte time-out. See the `event_callback` test in [test_event.cpp](https://github.com/kylelutz/compute/blob/master/test/test_event.cpp#L44) for an example.

---

## Comment 11

> Username: bkchr  
> Created_at: 2015-03-30 18:33:58 UTC  
> Updated_at: 2015-06-04 10:08:09 UTC  
> Url: https://github.com/boostorg/compute/pull/443#discussion_r27418584  

Do you have a platform with more than one device per context?  
We got two GeForce GTX 460 running with driver version 349.12. The two cards are in the same context and then this call doesn't work for me. The operating system is an Linux.

---

## Comment 12

> Username: kylelutz  
> Created_at: 2015-04-01 03:19:45 UTC  
> Updated_at: 2015-06-04 10:08:09 UTC  
> Url: https://github.com/boostorg/compute/pull/443#discussion_r27542283  

Yeah, I've tested again on a system with multiple NVIDIA-devices, but not with 349.12. But if this fixes the issue for that configuration then we should get this merged.

---

## Comment 13

> Username: bkchr  
> Created_at: 2015-04-02 19:39:25 UTC  
> Updated_at: 2015-06-04 10:08:09 UTC  
> Url: https://github.com/boostorg/compute/pull/443#discussion_r27689760  

You tried it also with creating one context with both devices? So the first call to clGetContextInfo returns more than 8 byte as result?  
For me this fix works. Maybe the function is only able to return all devices and not only one?

---

## Comment 14

> Username: bkchr  
> Created_at: 2015-05-15 10:08:07 UTC  
> Updated_at: 2015-06-04 10:08:09 UTC  
> Url: https://github.com/boostorg/compute/pull/443#discussion_r30397791  

Hi, do you have any plans to merge this fix?

---

## Comment 15

> Username: kylelutz  
> Created_at: 2015-05-15 15:54:13 UTC  
> Updated_at: 2015-06-04 10:08:09 UTC  
> Url: https://github.com/boostorg/compute/pull/443#discussion_r30420556  

Sorry for the delay, I've been quite busy. Could you rebase this on the latest `develop` branch and fix-up the commit messages? Should be fairly easy with an interactive rebase (`git rebase -i boostorg/develop`). Thanks!

---

## Comment 16

> Username: bkchr  
> Created_at: 2015-06-04 10:10:00 UTC  
> Url: https://github.com/boostorg/compute/pull/443#issuecomment-108823121  

This time I have to say sorry for the long delay :/ I hope that now everything is okay for merging :)

---

## Comment 17

> Username: kylelutz  
> Created_at: 2015-06-11 04:17:39 UTC  
> Updated_at: 2015-06-11 04:18:06 UTC  
> Url: https://github.com/boostorg/compute/pull/443#issuecomment-110994921  

Yeah, we should definitely get these fixes merged. One issue I'm seeing currently is the following type of warning (on OSX with OpenCL 1.2 headers):  
  
```  
In file included from ../test/test_extrema.cpp:14:  
In file included from ../include/boost/compute/system.hpp:24:  
../include/boost/compute/command_queue.hpp:1219:19: warning: 'clEnqueueMarker' is deprecated: first deprecated in OS X 10.8 [-Wdeprecated-declarations]  
            ret = clEnqueueMarker(m_queue, &event_.get());  
                  ^  
/System/Library/Frameworks/OpenCL.framework/Headers/cl.h:1192:1: note: 'clEnqueueMarker' has been explicitly marked deprecated here  
clEnqueueMarker(cl_command_queue    /* command_queue */,  
^  
```  
  
I'm not sure the best way to work around this. We could disable the "`-Wdeprecated-declarations`" flag for our tests but then this may still show up in user code. We could potentially work around that with `#pragma`'s. Any other ideas?

---

## Comment 18

> Username: bkchr  
> Created_at: 2015-06-11 06:52:29 UTC  
> Url: https://github.com/boostorg/compute/pull/443#issuecomment-111016320  

Yeah I also get these warnings in my code. Maybe we create a "BOOST_COMPUTE_ENABLE_DEPRECATED" define and if the define is enabled, the user is responsible for handling these warnings. If you want to delete them from the tests, your're idea with a pragma seems to be fine!

---

## Comment 19

> Username: kylelutz  
> Created_at: 2015-06-23 02:59:51 UTC  
> Url: https://github.com/boostorg/compute/pull/443#issuecomment-114336052  

Yeah, that sounds workable. I'll try to get a patch in to automatically disable the warnings but this looks good. Thanks for all your work on this!

---
