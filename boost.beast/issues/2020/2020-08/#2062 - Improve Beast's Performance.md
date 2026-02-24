# #2062 - Improve Beast's Performance [Closed]

> Username: bigvzhang  
> Created at: 2020-08-25 04:42:39 UTC  
> Updated at: 2024-06-06 05:34:20 UTC  
> Closed at: 2020-11-16 03:45:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2062  

### Version of Beast 189  
  
When network is slow, nodejs on windows7 is faster(double beast's speed) to download one 5M size file.  I compared  the performance of Beast with NodeJS and Apache Http.  Is it possible to improve its performance?   
  
### Comparisons  
``` bash  
Download a file whose size is 4452K on CentOS6 on one VirtualBox  
 Beast:  
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current  
                                 Dload  Upload   Total   Spent    Left  Speed  
100 4452k  100 4452k    0     0  29.9M      0 --:--:-- --:--:-- --:--:-- 54.1M  
  Node:  
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current  
                                 Dload  Upload   Total   Spent    Left  Speed  
100 4452k  100 4452k    0     0  58.2M      0 --:--:-- --:--:-- --:--:-- 63.9M  
Apache:  
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current  
                                 Dload  Upload   Total   Spent    Left  Speed  
100 4452k  100 4452k    0     0  78.4M      0 --:--:-- --:--:-- --:--:--  114M  
Download a file whose size is 100M on CentOS6 on one VirtualBox  
 Beast:  
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current  
                                 Dload  Upload   Total   Spent    Left  Speed  
100  100M  100  100M    0     0  46.0M      0  0:00:02  0:00:02 --:--:-- 46.4M  
  Node:  
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current  
                                 Dload  Upload   Total   Spent    Left  Speed  
100  100M  100  100M    0     0  55.9M      0  0:00:01  0:00:01 --:--:-- 56.3M  
Apache:  
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current  
                                 Dload  Upload   Total   Spent    Left  Speed  
100  100M  100  100M    0     0  99.9M      0  0:00:01  0:00:01 --:--:--  106M  
  
  
Download a file whose size is 4452K on Windows7  
 Beast:  
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current  
                                 Dload  Upload   Total   Spent    Left  Speed  
100 4452k  100 4452k    0     0  14.0M      0 --:--:-- --:--:-- --:--:-- 14.1M  
  Node:  
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current  
                                 Dload  Upload   Total   Spent    Left  Speed  
100 4452k  100 4452k    0     0  38.1M      0 --:--:-- --:--:-- --:--:-- 39.1M  
Apache:  
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current  
                                 Dload  Upload   Total   Spent    Left  Speed  
100 4452k  100 4452k    0     0  38.1M      0 --:--:-- --:--:-- --:--:-- 38.8M  
Download a file whose size is 100M on Windows7  
 Beast:  
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current  
                                 Dload  Upload   Total   Spent    Left  Speed  
100  100M  100  100M    0     0  49.9M      0  0:00:02  0:00:02 --:--:-- 50.1M  
  Node:  
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current  
                                 Dload  Upload   Total   Spent    Left  Speed  
100  100M  100  100M    0     0  43.9M      0  0:00:02  0:00:02 --:--:-- 44.2M  
Apache:  
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current  
                                 Dload  Upload   Total   Spent    Left  Speed  
100  100M  100  100M    0     0  52.7M      0  0:00:01  0:00:01 --:--:-- 52.9M  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-08-25 06:27:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2062#issuecomment-679829258  

Oh, that's interesting - a friend of mine did a similar benchmark and got the opposite results.  
  
Are you able to share a github repo containing your benchmark so I can replicate/investigate?

---

## Comment 2

> Username: bigvzhang  
> Created at: 2020-08-25 11:45:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2062#issuecomment-679974886  

I put documents on  https://github.com/bigvzhang/vhttp/tree/master/benchmark and https://github.com/bigvzhang/vhttp/tree/master/src_ndjs.

---

## Comment 3

> Username: stale[bot]  
> Created at: 2020-11-01 07:41:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2062#issuecomment-720048933  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2020-11-16 03:45:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2062#issuecomment-727717723  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
