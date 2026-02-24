# #106 Improve reduce_on_gpu kernel for nvidia devices + creation of a SMA example [Closed]

> Username: banche  
> Created at: 2014-05-07 00:22:04 UTC  
> Updated at: 2014-06-19 04:57:29 UTC  
> Closed at: 2014-05-11 21:44:38 UTC  
> Url: https://github.com/boostorg/compute/pull/106  

Improve reduce_on_gpu kernel for nvidia device.  
Creation of a SMA example

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-05-07 00:47:55 UTC  
> Url: https://github.com/boostorg/compute/pull/106#issuecomment-42377603  

[![Coverage Status](https://coveralls.io/builds/745187/badge)](https://coveralls.io/builds/745187)  
  
Coverage remained the same when pulling **ec1f3250f5cdc1c6f749c59be6f11a64106ba46a on banche:origin/develop** into **00f782355efbc7455e73c3b76ee873e5ac3d5e37 on kylelutz:master**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-05-07 03:14:10 UTC  
> Url: https://github.com/boostorg/compute/pull/106#issuecomment-42385491  

Very cool! I see it's passing on Travis-CI now. I'll test it out on my machines and let you know what kind of performance I see.  
  
And, just FYI, you don't need to close and create a new pull-request when you want to change the code. You can just push to the same branch again (i.e. `git push -f origin HEAD`).

---

## Comment 3

> Username: banche  
> Created_at: 2014-05-07 06:32:19 UTC  
> Url: https://github.com/boostorg/compute/pull/106#issuecomment-42393850  

Thx, good to know :+1: , never used github, only a lot git at work but more in command line way.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2014-05-10 17:49:13 UTC  
> Updated_at: 2014-06-19 04:58:18 UTC  
> Url: https://github.com/boostorg/compute/pull/106#issuecomment-42749066  

Hey Benoit,  
  
I cherry-picked the "Improve reduce_on_gpu kernel" commit into my `develop` branch. It's working great and I get a nice speedup on my NVIDIA machine! The commit is here 985414ed50930e1050295ea18638b1ce15cbc39c. I cleaned up some small whitespace and formatting issues. It should be merged into `master` soon. I also added some simple functions for checking the vendor of a device (see b1eef72ec2046932c038c50eebc037811285cee7).  
  
And, the `simple_moving_average` example looks nice. Can you fix the issues I commented on? After that it should be ready to merge (probably as a new pull-request).  
  
Thanks!

---

## Comment 5

> Username: banche  
> Created_at: 2014-05-11 16:18:13 UTC  
> Url: https://github.com/boostorg/compute/pull/106#issuecomment-42774946  

Hi Kyle  
  
sure, the issues are fixed on my local repo.   
I'm still on the matrix transpose example I should have finished it by tomorrow, just need to add the timer now and try to process more than one value per thread.  
  
It should be good for tomorrow evening.

---

## Comment 6

> Username: kylelutz  
> Created_at: 2014-05-11 21:44:38 UTC  
> Url: https://github.com/boostorg/compute/pull/106#issuecomment-42784312  

Sounds good. I'm going to close this pull-request then.

---
