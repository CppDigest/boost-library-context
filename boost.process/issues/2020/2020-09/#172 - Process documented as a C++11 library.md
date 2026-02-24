# #172 - Process documented as a C++11 library [Open]

> Username: eldiener  
> Created at: 2020-09-13 03:09:27 UTC  
> Updated at: 2020-09-15 05:45:41 UTC  
> Url: https://github.com/boostorg/process/issues/172  

The process library should mention in its documentation that it requires at least C++11 support.

---

## Comment 1

> Username: itss1ddhant  
> Created at: 2020-09-14 12:54:18 UTC  
> Url: https://github.com/boostorg/process/issues/172#issuecomment-692032512  

@eldiener I am a beginner in open source contribution and would like to take up this issue. Could you help me out with some more details about the issue?

---

## Comment 2

> Username: eldiener  
> Created at: 2020-09-14 16:13:50 UTC  
> Url: https://github.com/boostorg/process/issues/172#issuecomment-692160676  

It is not that complicated. The process library is a C++11 library, so somewhere in the documentation for the process library it should mention that it is a C++11 library. The process library requires a compiler at the C++11 standard level or beyond, else it can not be compiled without errors.

---

## Comment 3

> Username: itss1ddhant  
> Created at: 2020-09-14 18:21:36 UTC  
> Url: https://github.com/boostorg/process/issues/172#issuecomment-692229471  

@eldiener Can you help me with where should I find out the documentation for the Process library. I cloned it but I couldn't find the documentation. Maybe it's just a beginner error!

---

## Comment 4

> Username: eldiener  
> Created at: 2020-09-14 18:53:33 UTC  
> Url: https://github.com/boostorg/process/issues/172#issuecomment-692246257  

There is a doc subfolder. The documentation is written in quickbook format. Learn quickbook if you do not know it. It is easy to learn. Are you one of the current maintainers of the process library ?

---

## Comment 5

> Username: itss1ddhant  
> Created at: 2020-09-14 18:57:57 UTC  
> Url: https://github.com/boostorg/process/issues/172#issuecomment-692248577  

No I am not a maintainer. And yes, I came across the quickbook .QBK file for process. I was thinking of it too. But I thought it's better to reconfirm. Thanks a lot.

---

## Comment 6

> Username: eldiener  
> Created at: 2020-09-14 21:13:51 UTC  
> Url: https://github.com/boostorg/process/issues/172#issuecomment-692317911  

I am glad you are willing to do the work of updating the documentation. Do you know how to create a PR for the work you do ?

---

## Comment 7

> Username: eldiener  
> Created at: 2020-09-14 22:20:24 UTC  
> Url: https://github.com/boostorg/process/issues/172#issuecomment-692344130  

No problem. Just ask for help.

---

## Comment 8

> Username: itss1ddhant  
> Created at: 2020-09-14 23:08:10 UTC  
> Url: https://github.com/boostorg/process/issues/172#issuecomment-692365236  

I did a fix and generated a PR too but it seems like Travis Cl is continuously building. #174 . Could you see to it or help me out in case of any errors.

---

## Comment 9

> Username: eldiener  
> Created at: 2020-09-15 02:36:21 UTC  
> Url: https://github.com/boostorg/process/issues/172#issuecomment-692426072  

The failure occurred because of some previous update on the develop branch. See if you can figure out what previous commit caused the failure. If you can add a fix for the failure to your PR. If you can not you can only try to bring the fatc that a previous update is causing a Travis CI failure to the attention of the process library maintainer.

---

## Comment 10

> Username: itss1ddhant  
> Created at: 2020-09-15 04:39:12 UTC  
> Url: https://github.com/boostorg/process/issues/172#issuecomment-692459118  

Okay, I will try to find out that error but in case I fail to do so , how should I know who's the process library maintainer because I see all of the pull requests which have been made so far , even by others in various issues for this repo. have failed to pass through the Travis CI failure.

---

## Comment 11

> Username: eldiener  
> Created at: 2020-09-15 05:44:08 UTC  
> Url: https://github.com/boostorg/process/issues/172#issuecomment-692478466  

Leave a note here and hope that the maintainer reads it. As an alternative, or addition, leave a message on the Boost developers mailing list and hope that someone who maintains the process library can fix the problem.

---

## Comment 12

> Username: itss1ddhant  
> Created at: 2020-09-15 05:45:41 UTC  
> Url: https://github.com/boostorg/process/issues/172#issuecomment-692479010  

Will do it then. Thanks a lot for the help.
