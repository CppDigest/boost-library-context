# #64 Fix -Wtautological-overlap-compare clang warning in linear_congruenti… [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2020-02-26 14:34:20 UTC  
> Updated at: 2020-07-07 23:02:41 UTC  
> Merged at: 2020-07-07 23:02:41 UTC  
> Closed at: 2020-07-07 23:02:41 UTC  
> Url: https://github.com/boostorg/random/pull/64  

…al.hpp  
  
Clang 10 shows this warning/error (with -Werror):  
  
/remote/intdeliv/components/osp/Boost/19-0-0-18/include/boost/random/linear_congruential.hpp:140:20: error: overlapping comparisons always evaluate to false [-Werror,-Wtautological-overlap-compare]  
        if(_x <= 0 && _x != 0) {  
           ~~~~~~~~^~~~~~~~~~  
/remote/intdeliv/components/osp/Boost/19-0-0-18/include/boost/random/linear_congruential.hpp:393:11: note: in instantiation of member function 'boost::random::linear_congruential_engine<unsigned long, 25214903917, 11, 281474976710656>::seed' requested here  
    { lcf.seed(cnv(x0)); }

---

## Comment 1

> Username: swatanabe  
> Created_at: 2020-04-08 01:02:41 UTC  
> Url: https://github.com/boostorg/random/pull/64#issuecomment-610694294  

AMDG  
  
This is quite unfortunate, as the code in question is written  
the way it is to avoid a different warning.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: Romain-Geissler-1A  
> Created_at: 2020-04-08 08:58:21 UTC  
> Url: https://github.com/boostorg/random/pull/64#issuecomment-610839656  

So what do you suggest then ? What was this other warning ?  
  
Shall we use a pragma to silence it then ?

---

## Comment 3

> Username: swatanabe  
> Created_at: 2020-04-09 00:43:27 UTC  
> Url: https://github.com/boostorg/random/pull/64#issuecomment-611265861  

AMDG  
  
On 4/8/20 2:58 AM, Romain Geissler @ Amadeus wrote:  
> So what do you suggest then ? What was this other warning ?  
>   
  
I don't remember.  It looks like it comes from  
  
commit 6e35d9a732f37928d5fc45fa32a3c69e4889a9ad  
Author: Steven Watanabe <steven@providere-consulting.com>  
Date:   Sat Nov 14 21:07:31 2009 +0000  
  
so the warning may be completely obsolete by now.  
  
> Shall we use a pragma to silence it then ?  
>   
  
If no current compiler (gcc/clang/msvc) warns on your  
code with max warnings enabled, then your patch should  
be fine.  If there is still an issue I'd prefer to  
accept your patch and suppress the other warning, as  
the code is cleaner that way.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: Romain-Geissler-1A  
> Created_at: 2020-04-09 09:25:49 UTC  
> Url: https://github.com/boostorg/random/pull/64#issuecomment-611428439  

I compile my projects with `-Wall`, `-Wextra`, `-Werror`, and both gcc (current trunk) and clang (current trunk as well). I have not tried released version. This code doesn't seem to raise any more warning for these two cases.  
  
I am not working at all in a Windows environment, and I really don't know anything about msvc, so I can't test there.

---

## Comment 5

> Username: Romain-Geissler-1A  
> Created_at: 2020-06-08 10:24:10 UTC  
> Url: https://github.com/boostorg/random/pull/64#issuecomment-640515740  

So, do you think we should merge this in the end ?

---
