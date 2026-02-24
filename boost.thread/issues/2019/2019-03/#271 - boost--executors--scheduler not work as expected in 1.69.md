# #271 - boost::executors::scheduler not work as expected in 1.69 [Closed]

> Username: DengZuoheng  
> Created at: 2019-03-17 09:04:18 UTC  
> Updated at: 2019-05-09 19:26:19 UTC  
> Closed at: 2019-05-09 19:26:19 UTC  
> Url: https://github.com/boostorg/thread/issues/271  

In following code, every 500ms submit a work, but each work's time duration less than before:  
~~~  
#include <boost/chrono/system_clocks.hpp>  
#include <boost/thread/executors/scheduler.hpp>  
  
int main() {  
	boost::executors::scheduler<boost::chrono::steady_clock> sc;  
	auto start = boost::chrono::steady_clock::now();  
	int count = 10;  
	for (int i = 0; i < count; ++i) {  
		sc.submit_after([=] {  
			std::cout << count - i << "s" << std::endl;  
			auto dur = boost::chrono::steady_clock::now() - start;  
			std::cout << boost::chrono::duration_cast<boost::chrono::milliseconds>(dur) << std::endl;  
		}, boost::chrono::seconds(count - i));  
		boost::this_thread::sleep_for(boost::chrono::milliseconds(500));  
		std::cout << i << std::endl;  
	}  
  
	boost::this_thread::sleep_for(boost::chrono::seconds(count * 2));  
	return 0;  
  
}  
~~~  
  
1.69 behave different with 1.66, while 1.66's output is:  
  
~~~  
0  
1  
2  
3  
4  
5  
6  
7  
8  
9  
1s  
5507 milliseconds  
2s  
6006 milliseconds  
3s  
6506 milliseconds  
4s  
7004 milliseconds  
5s  
7504 milliseconds  
6s  
8004 milliseconds  
7s  
8504 milliseconds  
8s  
9003 milliseconds  
9s  
9501 milliseconds  
10s  
10001 milliseconds  
~~~  
  
but 1.69's output is   
  
~~~  
0  
1  
2  
3  
4  
5  
6  
7  
8  
9  
1s  
10001 milliseconds  
2s  
10002 milliseconds  
3s  
10002 milliseconds  
4s  
10003 milliseconds  
5s  
10003 milliseconds  
6s  
10004 milliseconds  
7s  
10005 milliseconds  
8s  
10005 milliseconds  
9s  
10006 milliseconds  
10s  
10006 milliseconds  
~~~  
  
1.66 is what I want. It's that a bug of 1.69 or I use it wrong?  
  
Thanks.

---

## Comment 1

> Username: viboes  
> Created at: 2019-03-17 15:11:22 UTC  
> Updated at: 2019-03-18 20:34:04 UTC  
> Url: https://github.com/boostorg/thread/issues/271#issuecomment-473674769  

I confirm you figures.  
  
https://wandbox.org/permlink/Z3oiCHlYTsMQdOk6  
  
The difference is between 1.66 and 1.67 and my understanding is as well that the result for 1.66 are better.  
  
However, the results for 1.67 and 1.69 are also good. This is because the request is to execute after the specified duration and the exeution take place after this duration is elapsed.  
  
I believe the changes is due to   
https://github.com/boostorg/thread/issues/162 fix as much time-related issues as possible and improve the QOI  
  
need to check for the concerned commits.

---

## Comment 2

> Username: viboes  
> Created at: 2019-03-18 20:34:41 UTC  
> Url: https://github.com/boostorg/thread/issues/271#issuecomment-474089939  

I suspect that the issue was introduce by this change (https://github.com/boostorg/thread/commit/e96516d10d32ec54c43d3a0a148a0ac79b207168#diff-fca6695c01c8ed364635a2532c8c2564), but I'm not sure yet.  
  
@austin-beer, Austin please, could you take a look?

---

## Comment 3

> Username: austin-beer  
> Created at: 2019-03-18 20:36:29 UTC  
> Url: https://github.com/boostorg/thread/issues/271#issuecomment-474090556  

Yes, I will take a look and get back to you with what I find.

---

## Comment 4

> Username: austin-beer  
> Created at: 2019-03-19 01:32:03 UTC  
> Url: https://github.com/boostorg/thread/issues/271#issuecomment-474163151  

Yes, this issue was introduced by the commit you referenced. I've created a pull request to fix this issue.  
  
https://github.com/boostorg/thread/pull/272

---

## Comment 5

> Username: viboes  
> Created at: 2019-03-19 05:51:39 UTC  
> Url: https://github.com/boostorg/thread/issues/271#issuecomment-474207485  

I've merged the PR.  
@DengZuoheng please could you confirm this restores the behavior of 1.69?  
  
It would be great to have a test that do the real check, not a visual one (using e.g. stream streams or something else).  
  
@DengZuoheng, @austin-beer  would some of you mind to provide it?

---

## Comment 6

> Username: viboes  
> Created at: 2019-03-19 05:59:38 UTC  
> Url: https://github.com/boostorg/thread/issues/271#issuecomment-474208982  

I'm not sure I could integrate it fo Boost.1.70 as the beta test is already on the air.  
I will try.  
  
Thanks @DengZuoheng for your report and thanks @austin-beer  for the collaboration.

---

## Comment 7

> Username: austin-beer  
> Created at: 2019-03-20 18:53:55 UTC  
> Url: https://github.com/boostorg/thread/issues/271#issuecomment-474982114  

I think I can create an automated test case based on the code we used to manually test this issue. I'll try to get a PR for it in the next day or two.

---

## Comment 8

> Username: austin-beer  
> Created at: 2019-03-22 16:56:09 UTC  
> Url: https://github.com/boostorg/thread/issues/271#issuecomment-475697428  

I've created a PR that adds a simple test case for this issue: https://github.com/boostorg/thread/pull/273

---

## Comment 9

> Username: viboes  
> Created at: 2019-03-23 09:49:19 UTC  
> Url: https://github.com/boostorg/thread/issues/271#issuecomment-475856026  

I've merged it :)

---

## Comment 10

> Username: austin-beer  
> Created at: 2019-04-01 16:34:24 UTC  
> Url: https://github.com/boostorg/thread/issues/271#issuecomment-478652004  

I re-ran the time jump tests and discovered that my fix for this issue re-introduced some time jump issues with sync_timed_queue. I've submitted a PR to address that: #277.
