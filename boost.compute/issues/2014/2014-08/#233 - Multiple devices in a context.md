# #233 - Multiple devices in a context [Closed]

> Username: twopisoft  
> Created at: 2014-08-14 01:53:44 UTC  
> Updated at: 2014-08-15 03:28:30 UTC  
> Closed at: 2014-08-15 03:28:30 UTC  
> Url: https://github.com/boostorg/compute/issues/233  

Hi All  
  
Is there a boost::compute example that shows using of multiple devices in a context?  
  
TIA  
Arshad

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-08-14 02:35:16 UTC  
> Url: https://github.com/boostorg/compute/issues/233#issuecomment-52137277  

Hi Arshad,  
  
No, not currently. And I just relized that the `context` class doesn't yet have a constructor for multiple devices. I've added in pull-request #234. After that gets merged you should be able to create a `context` from a `std::vector<device>`. I've also added a small test which iterates through each platform on the system and creates a context for all of the devices on the platform. If you could test it out that would be great. It should be merged soon (assuming the tests pass).  
  
Cheers,  
Kyle

---

## Comment 2

> Username: kylelutz  
> Created at: 2014-08-14 02:37:57 UTC  
> Url: https://github.com/boostorg/compute/issues/233#issuecomment-52137438  

Also, I'll work on an example for multiple-device contexts.

---

## Comment 3

> Username: twopisoft  
> Created at: 2014-08-15 00:14:10 UTC  
> Url: https://github.com/boostorg/compute/issues/233#issuecomment-52261376  

Hi Kyle  
  
Thanks for getting this feature added so quickly. I'll test it out and let you know.  
  
Arshad

---

## Comment 4

> Username: kylelutz  
> Created at: 2014-08-15 01:53:33 UTC  
> Url: https://github.com/boostorg/compute/issues/233#issuecomment-52266869  

No problem. Hope it works for you.

---

## Comment 5

> Username: twopisoft  
> Created at: 2014-08-15 02:48:21 UTC  
> Url: https://github.com/boostorg/compute/issues/233#issuecomment-52269372  

Tested the changes on a 2 GPU system. The test program (test_context.cpp) completed successfully.

---

## Comment 6

> Username: kylelutz  
> Created at: 2014-08-15 03:28:30 UTC  
> Url: https://github.com/boostorg/compute/issues/233#issuecomment-52271138  

Cool. Let me know if you run into any other issues.
