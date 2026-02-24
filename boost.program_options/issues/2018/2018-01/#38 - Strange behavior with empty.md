# #38 - Strange behavior with empty "--" [Closed]

> Username: chfast  
> Created at: 2018-01-15 15:29:36 UTC  
> Updated at: 2018-03-22 15:40:52 UTC  
> Closed at: 2018-03-22 15:28:47 UTC  
> Url: https://github.com/boostorg/program_options/issues/38  

In this example  
  
```sh  
example -- --option  
```  
  
arguments are going to be parsed as positional arguments: "--" and "--option". I don't understand why.

---

## Comment 1

> Username: jeking3  
> Created at: 2018-03-06 14:30:56 UTC  
> Url: https://github.com/boostorg/program_options/issues/38#issuecomment-370799258  

I found no information in a cursory search of the boost program_options documentation about positional argument handling or the use of "--", however this is a common practice:  
  
https://unix.stackexchange.com/questions/11376/what-does-double-dash-mean-also-known-as-bare-double-dash

---

## Comment 2

> Username: vprus  
> Created at: 2018-03-06 19:31:32 UTC  
> Url: https://github.com/boostorg/program_options/issues/38#issuecomment-370899738  

Indeed, it is the intended behaviour to stop looking for named options  
after --. What problem does it cause?  
  
On Tue, 6 Mar 2018, 17:30 James E. King, III, <notifications@github.com>  
wrote:  
  
> I found no information in a cursory search of the boost program_options  
> documentation about positional argument handling or the use of "--",  
> however this is a common practice:  
>  
>  
> https://unix.stackexchange.com/questions/11376/what-does-double-dash-mean-also-known-as-bare-double-dash  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/program_options/issues/38#issuecomment-370799258>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AANye9CkFHVUzlTk5HCdnsQN_lKOsDi4ks5tbp2ggaJpZM4Rek-S>  
> .  
>  
--   
Vladimir Prus  
http://vladimirprus.com

---

## Comment 3

> Username: vprus  
> Created at: 2018-03-22 15:28:46 UTC  
> Url: https://github.com/boostorg/program_options/issues/38#issuecomment-375348175  

I am gonna close it as 'works as designed'.

---

## Comment 4

> Username: jeking3  
> Created at: 2018-03-22 15:40:52 UTC  
> Url: https://github.com/boostorg/program_options/issues/38#issuecomment-375352710  

Perhaps this behavior should be documented, however.
