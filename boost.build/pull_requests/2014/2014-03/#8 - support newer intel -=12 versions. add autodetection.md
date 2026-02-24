# #8 support newer intel >=12 versions. add autodetection [Merged]

> Username: bernhard-b  
> Created at: 2014-03-27 22:26:36 UTC  
> Updated at: 2021-10-02 22:35:58 UTC  
> Merged at: 2014-05-09 01:53:36 UTC  
> Closed at: 2014-05-09 01:53:36 UTC  
> Url: https://github.com/boostorg/build/pull/8  

Can someone comment if those changes are acceptable?  
It allows now to put: using intel ; in the user-config.jam and it auto registers all detected intel versions for the registered msvc toolsets.  
b2 can be invoked with b2 --toolset=intel-14-vc12 to select the intel 14.0 compiler with msvc12 compatibility.

---

## Comment 1

> Username: swatanabe  
> Created_at: 2014-03-27 23:00:01 UTC  
> Url: https://github.com/boostorg/build/pull/8#issuecomment-38871586  

AMDG  
  
On 03/27/2014 03:26 PM, bernhard-b wrote:  
  
> Can someone comment if those changes are acceptable?  
> It allows now to put: using intel ; in the user-config.jam and it auto registers all detected intel versions for the registered msvc toolsets.  
> b2 can be invoked with b2 --toolset=intel-14-vc12 to select the intel 14.0 compiler with msvc12 compatibility.  
> You can merge this Pull Request by running:  
  
Is this the patch that you intended you submit?  
It doesn't quite match your description.  
using intel ; will fail with a hard error.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: bernhard-b  
> Created_at: 2014-03-28 19:01:16 UTC  
> Url: https://github.com/boostorg/build/pull/8#issuecomment-38956170  

Sorry i mixed up the versions. i updated the intel-win.jam with the corret one. i tested following commands:  
(no user-config): b2 -a --toolset=intel-12.0-vc10 .  
  
in user-config one of the following using statements:  
using intel ;  
using intel : : : <compatibility>vc10 ;  
using intel : 12.1 : : <compatibility>vc10 ;  
using intel : 12.1 : "C:/Program Files (x86)/Intel/Composer XE 2011 SP1/bin/ia32/icl.exe" : <compatibility>vc10 ;

---

## Comment 3

> Username: swatanabe  
> Created_at: 2014-03-28 21:49:50 UTC  
> Updated_at: 2014-03-31 18:15:54 UTC  
> Url: https://github.com/boostorg/build/pull/8#discussion_r11087245  

We should probably not issue an error if they match.

---

## Comment 4

> Username: swatanabe  
> Created_at: 2014-03-28 21:52:51 UTC  
> Updated_at: 2014-03-31 18:15:54 UTC  
> Url: https://github.com/boostorg/build/pull/8#discussion_r11087371  

Why is the character "V" forbidden to be part of the version?

---

## Comment 5

> Username: swatanabe  
> Created_at: 2014-03-28 21:54:09 UTC  
> Updated_at: 2014-03-31 18:15:54 UTC  
> Url: https://github.com/boostorg/build/pull/8#discussion_r11087431  

s/know/known/

---

## Comment 6

> Username: swatanabe  
> Created_at: 2014-03-28 22:00:10 UTC  
> Updated_at: 2014-03-31 18:15:54 UTC  
> Url: https://github.com/boostorg/build/pull/8#discussion_r11087657  

What about vc7.1?

---

## Comment 7

> Username: swatanabe  
> Created_at: 2014-03-28 22:11:12 UTC  
> Url: https://github.com/boostorg/build/pull/8#issuecomment-38973711  

AMDG  
  
On 03/28/2014 12:01 PM, bernhard-b wrote:  
  
> Sorry i mixed up the versions. i updated the intel-win.jam with the corret one. i tested following commands:  
> (no user-config): b2 -a --toolset=intel-12.0-vc10 .  
>   
> in user-config one of the following using statements:  
> using intel ;  
> using intel : : : <compatibility>vc10 ;  
> using intel : 12.1 : : <compatibility>vc10 ;  
> using intel : 12.1 : "C:/Program Files (x86)/Intel/Composer XE 2011 SP1/bin/ia32/icl.exe" : <compatibility>vc10 ;  
  
If you have multiple versions of msvc installed,  
can you build with both?  
  
b2 toolset=intel-12.0-vc10 toolset=intel-12.0-vc11  
  
It looks like your code should be able  
handle this. Have you tested it?  
  
In Christ,  
Steven Watanabe

---

## Comment 8

> Username: bernhard-b  
> Created_at: 2014-03-31 18:17:00 UTC  
> Url: https://github.com/boostorg/build/pull/8#discussion_r11128929  

changed that in the new commit

---

## Comment 9

> Username: bernhard-b  
> Created_at: 2014-03-31 18:20:04 UTC  
> Url: https://github.com/boostorg/build/pull/8#discussion_r11129089  

I probably wanted to stop before vc... i changed it now to (.+)-vc.+$ so it allows now versions 12 or 12.0

---

## Comment 10

> Username: bernhard-b  
> Created_at: 2014-03-31 18:24:15 UTC  
> Url: https://github.com/boostorg/build/pull/8#discussion_r11129307  

I changed the matching to include the dot now, and put it in an extra function. Intel > 12 doesn't support 7.1 anymore.

---

## Comment 11

> Username: bernhard-b  
> Created_at: 2014-04-02 04:35:26 UTC  
> Url: https://github.com/boostorg/build/pull/8#issuecomment-39288098  

I tried b2 --toolset=intel-14.0-vc10 --toolset=intel-14.0-vc12  and works like you expected. I fixed the other problems in the new version.

---
