# #189 - Bjam -j is capped at 256 [Closed]

> Username: brycelelbach  
> Created at: 2017-04-01 13:52:07 UTC  
> Updated at: 2017-07-22 04:30:11 UTC  
> Closed at: 2017-04-26 20:25:59 UTC  
> Url: https://github.com/boostorg/build/issues/189  

./b2 -j268 does not work:  
  
```  
Invalid value for the '-j' option, valid values are 1 through 256.  
```  
  
I have more than 256 cores, I'd like to use them :)

---

## Comment 1

> Username: Belcourt  
> Created at: 2017-04-01 22:13:35 UTC  
> Url: https://github.com/boostorg/build/issues/189#issuecomment-290950555  

Would 512 be sufficient for you for a while?

---

## Comment 2

> Username: swatanabe  
> Created at: 2017-04-01 23:08:58 UTC  
> Url: https://github.com/boostorg/build/issues/189#issuecomment-290953165  

AMDG  
  
On 04/01/2017 04:13 PM, Noel Belcourt wrote:  
> Would 512 be sufficient for you for a while?  
>   
  
  That's an easy change, but we really shouldn't  
be using a fixed size array in the first place.  
  
In Christ,  
Steven Watanabe

---

## Comment 3

> Username: swatanabe  
> Created at: 2017-04-02 00:34:33 UTC  
> Url: https://github.com/boostorg/build/issues/189#issuecomment-290956554  

AMDG  
  
On 04/01/2017 04:13 PM, Noel Belcourt wrote:  
> Would 512 be sufficient for you for a while?  
>   
  
  For the record, the current limits imposed by the  
implementation (other than the fact that cmdtab  
is a static array) are:  
execunix.c: FD_SETSIZE / 2 = 512  
execnt.c: (MAXIMUM_WAIT_OBJECTS - 1) * MAXIMUM_WAIT_OBJECTS = 4032  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: swatanabe  
> Created at: 2017-04-02 00:40:17 UTC  
> Url: https://github.com/boostorg/build/issues/189#issuecomment-290956747  

AMDG  
  
...and actually, the limit is slightly less than 512,  
because the b2 process has some file descriptors that  
are not used for the child processes.  Just setting  
MAXJOBS to 512 is definitely wrong.  
  
In Christ,  
Steven Watanabe

---

## Comment 5

> Username: swatanabe  
> Created at: 2017-07-22 00:24:59 UTC  
> Url: https://github.com/boostorg/build/issues/189#issuecomment-317140399  

AMDG  
  
On 07/21/2017 06:03 PM, phalexo wrote:  
> I have spent a huge amount of time trying to build a C++ package through cmake. I have tried three different version of cmake v3.0.2 and to latest ones 3.9.0 and 2.8.x.   
>   
> Cmake passes -j 512 to b2 (building boost) for some reason. I don't know where the number is set.  
>   
> Then cmake fails with the text below.  Is there any way to pass a different number to b2 from cmake?  
>   
  
  I have no idea.  Why don't you ask whoever wrote the  
cmake code in question?  In any case, the cap for -j  
has been lifted for the next release.  
  
In Christ,  
Steven Watanabe
