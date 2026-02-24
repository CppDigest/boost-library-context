# #203 - devector misbehaves when used as a queue [Closed]

> Username: ScottBailey  
> Created at: 2021-12-05 23:28:00 UTC  
> Updated at: 2022-10-10 22:30:09 UTC  
> Closed at: 2022-10-10 22:30:08 UTC  
> Url: https://github.com/boostorg/container/issues/203  

The following code results in a termination after throwing an instance of 'std::bad_alloc':  
  
```cpp  
   boost::container::devector<size_t> d;  
   for(size_t i=0; true; ++i) {  
      d.push_back(i);  
      d.pop_front();  
   }  
```  
  
Capacity is growing beyond available memory even though size is alternating between 0 and 1. Evidently devector is reallocating when push_back() is called even when the container is *empty*.  
  
I've attached a file that performs a slightly friendlier set of tests.  
[main.cpp.txt](https://github.com/boostorg/container/files/7656976/main.cpp.txt)

---

## Comment 1

> Username: ScottBailey  
> Created at: 2021-12-05 23:31:13 UTC  
> Url: https://github.com/boostorg/container/issues/203#issuecomment-986323215  

Additional information:  
```sh  
$ gcc --version  
gcc (Debian 10.2.1-6) 10.2.1 20210110  
...  
$ uname --all  
Linux spirit 5.14.0-0.bpo.2-amd64 #1 SMP Debian 5.14.9-2~bpo11+1 (2021-10-10) x86_64 GNU/Linux  
```

---

## Comment 2

> Username: ScottBailey  
> Created at: 2021-12-07 00:07:28 UTC  
> Url: https://github.com/boostorg/container/issues/203#issuecomment-987398778  

Witching resource consumption with ps, I see resources growing to a number n1, then dropping to zero, growing to a new number  n2, dropping to zero, growing to n3. Each consecutive n is higher than the last until memory is exhusted.

---

## Comment 3

> Username: krishnaagarwal2001  
> Created at: 2022-01-18 06:27:33 UTC  
> Url: https://github.com/boostorg/container/issues/203#issuecomment-1015108588  

I want to rectify this issue. Can anyone help me how to start? I am a newbie in the world of open source. Thanks in advance.

---

## Comment 4

> Username: ScottBailey  
> Created at: 2022-01-18 15:11:18 UTC  
> Url: https://github.com/boostorg/container/issues/203#issuecomment-1015507578  

> I want to rectify this issue. Can anyone help me how to start? I am a newbie in the world of open source. Thanks in advance.  
  
Generally, I'd start by recreating the defect using the code I've supplied above in a fork of this project. Once you have a fix sorted out, you can write a Pull Request to merge it into this repo.

---

## Comment 5

> Username: krishnaagarwal2001  
> Created at: 2022-01-19 10:41:04 UTC  
> Url: https://github.com/boostorg/container/issues/203#issuecomment-1016306135  

I have installed boost libraries from boost.org site but it does not have any #include <boost/container/devector.hpp>. How to use this header file?

---

## Comment 6

> Username: ScottBailey  
> Created at: 2022-01-19 15:24:08 UTC  
> Url: https://github.com/boostorg/container/issues/203#issuecomment-1016576490  

Devector is only in the latests releases. I think 1.75 and later. You'll need to make  sure you have the right sources installed. After that, you'll need to ensure your build environment points to the install location.

---

## Comment 7

> Username: ScottBailey  
> Created at: 2022-09-10 19:28:12 UTC  
> Url: https://github.com/boostorg/container/issues/203#issuecomment-1242792791  

I'd like to fix this issue. I hope someone can weigh in on my thoughts, or point me to where I should look for more input.  
  
To sum up this issue: devector cannot be used as a queue. When a single element is added at one end and removed at the other, capacity will grow excessively. For example, at 100,000 iterations of push_back() followed by pop_front(), size() returns 0 and capacity() is 101923. This is excessive for a container who's size never grew beyond 1.  
  
How could we fix this? Here are some thoughts:  
  
1. When emptied, a list never updates its insertion points.   
2. When free capacity becomes zero at one end of the vector, the other is never checked.   
  
reallocation logic could:  
- shift insertion points when empty() instead of reallocate.  
- shift data instead of reallocate when an insertion at one end would cause capacity to exceed some multiple of size()   
  
Feedback? Better ideas?

---

## Comment 8

> Username: igaztanaga  
> Created at: 2022-09-10 22:58:35 UTC  
> Url: https://github.com/boostorg/container/issues/203#issuecomment-1242816719  

I'm rewriting parts of devector for Boost 1.81. The initial implementation provided in the review was not queue-friendly and changes were discussed in the mailing list during the review (there are different strategies for a double ended vector-like container). My current approach is based on the  following article [Double-ended vector - is it useful?](http://larshagencpp.github.io/blog/2016/05/22/devector), were elements are moved to the middle when there is no room in one end of the container.  
  
This new approach should fix this bug report and maintain container capacity reasonable for all patterns. It has drawbacks, though, like not having strong guarantee for exceptions when repeatedly calling push-back/front.

---

## Comment 9

> Username: ScottBailey  
> Created at: 2022-09-11 17:15:15 UTC  
> Url: https://github.com/boostorg/container/issues/203#issuecomment-1243006099  

> I'm rewriting parts of devector for Boost 1.81.  
  
That's great! Let me know if you need help or a review.

---

## Comment 10

> Username: igaztanaga  
> Created at: 2022-09-19 22:24:29 UTC  
> Url: https://github.com/boostorg/container/issues/203#issuecomment-1251632366  

You can test the new implementation I've just pushed to the develop branch. You might need to update Boost.Move and Boost.Intrusive to the latest develop snapshots.

---

## Comment 11

> Username: ScottBailey  
> Created at: 2022-10-01 22:49:17 UTC  
> Url: https://github.com/boostorg/container/issues/203#issuecomment-1264499678  

Updating move and intrusive were required.  
  
This change corrects the problem as reported. :-) Yay!!! And thanks!

---

## Comment 12

> Username: igaztanaga  
> Created at: 2022-10-10 22:30:08 UTC  
> Url: https://github.com/boostorg/container/issues/203#issuecomment-1273874458  

Thanks for the feedback!
