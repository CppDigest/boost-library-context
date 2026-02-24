# #245 - code coverity test defect [Closed]

> Username: mozeat-sun  
> Created at: 2019-07-25 04:31:19 UTC  
> Updated at: 2020-04-21 14:23:37 UTC  
> Closed at: 2020-03-30 07:03:17 UTC  
> Url: https://github.com/boostorg/test/issues/245  

hi  
    will fix coverity issue ?  
    scan boost 1.70.0 by Coverity ,report many issues,like bellow:  
   
![image](https://user-images.githubusercontent.com/32629245/61845862-855a4c80-aed7-11e9-9268-a1636284a01f.png)

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-12-17 20:01:16 UTC  
> Url: https://github.com/boostorg/test/issues/245#issuecomment-566725770  

Thanks for reporting this, and sorry for the late reply (ticket was in the wrong place). The fix is easy and should go in for 1.73.

---

## Comment 2

> Username: mozeat-sun  
> Created at: 2020-01-10 01:20:43 UTC  
> Url: https://github.com/boostorg/test/issues/245#issuecomment-572830606  

version: 1.70.0  
 more:  
![image](https://user-images.githubusercontent.com/32629245/72117883-3e2f1d80-338a-11ea-9b88-911533ba696c.png)  
![image](https://user-images.githubusercontent.com/32629245/72117935-6ae33500-338a-11ea-936d-495cd7427a42.png)  
@Raffi Enficiaud

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2020-01-10 06:24:06 UTC  
> Updated at: 2020-01-10 06:24:30 UTC  
> Url: https://github.com/boostorg/test/issues/245#issuecomment-572892325  

The second post is not exactly related to Boost.Test but to Beat, it would be good if you post those images on the Beast project's issue tracker. Thanks!  
  
+CC @boostorg/beast

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2020-03-30 07:03:17 UTC  
> Url: https://github.com/boostorg/test/issues/245#issuecomment-605820473  

Fixed in master for the issues related to boost/test. For the other issues, please contact @boostorg/beast team.
