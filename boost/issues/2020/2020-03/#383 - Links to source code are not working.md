# #383 - Links to source code are not working. [Closed]

> Username: CapSel  
> Created at: 2020-03-28 13:35:24 UTC  
> Updated at: 2020-06-06 09:23:05 UTC  
> Closed at: 2020-05-19 19:06:24 UTC  
> Url: https://github.com/boostorg/boost/issues/383  

All links at https://www.boost.org/users/download/ lead to page that says "forbidden".

---

## Comment 1

> Username: shirha  
> Created at: 2020-03-29 22:08:26 UTC  
> Url: https://github.com/boostorg/boost/issues/383#issuecomment-605709415  

same issue

---

## Comment 2

> Username: CapSel  
> Created at: 2020-03-29 22:56:29 UTC  
> Url: https://github.com/boostorg/boost/issues/383#issuecomment-605715051  

When I wrote to jfrog about it they replied with:  
  
> Hi,  
>   
> Thank you for contacting JFrog Support. With regards to query on unable to download files from boostorg, I verified the account and found that the OSS quota limit has reached the maximum and the account was blocked. I further checked internally and unblocked the account, now you should be able to download the files without any issues. Please let me know if you have any further queries, I will be glad to assist you.  
>   
> Looking forward to your response.  
>

---

## Comment 3

> Username: CapSel  
> Created at: 2020-03-29 23:58:06 UTC  
> Url: https://github.com/boostorg/boost/issues/383#issuecomment-605721988  

I've managed to download sources here:  
http://capsel.org/tmp/boost_1_72_0.7z  
http://capsel.org/tmp/boost_1_71_0.tar.bz2

---

## Comment 4

> Username: jlschrag  
> Created at: 2020-03-30 03:22:26 UTC  
> Url: https://github.com/boostorg/boost/issues/383#issuecomment-605761561  

This appears to have broken the boost Conan packages as well: https://stackoverflow.com/questions/60918097/why-do-my-conan-packages-appear-to-be-getting-corrupted

---

## Comment 5

> Username: mloskot  
> Created at: 2020-03-30 12:54:56 UTC  
> Url: https://github.com/boostorg/boost/issues/383#issuecomment-605981591  

Dup of #384. The issue is due to overuse of the monthly bandwidth allowance, wait until 1st of April.   
  
We hit this issue frequently also due to the fact people are being dumb with their CI configurations and build systems.

---

## Comment 6

> Username: wouterbeek  
> Created at: 2020-06-06 09:23:05 UTC  
> Url: https://github.com/boostorg/boost/issues/383#issuecomment-640019079  

I am observing this problem again today.  Waiting until the 1st of July is not a good fix IMO.  
  
Is there something users of Boost can do to help fix this?  Donate or a paid version for instance?
