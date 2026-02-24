# #12 Changes necessary to integrate safe_numerics with johnmcfarlane/fixed_point [Merged]

> Username: johnmcfarlane  
> Created at: 2016-08-09 16:43:07 UTC  
> Updated at: 2018-09-23 22:26:35 UTC  
> Merged at: 2016-09-09 04:31:38 UTC  
> Closed at: 2016-09-09 04:31:38 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/12  

Warning: only tested with GCC5.4 and Cland 3.8 so far.

---

## Comment 1

> Username: robertramey  
> Created_at: 2016-08-24 17:37:14 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/12#issuecomment-242147891  

These changes look correct to me.  
Did you run the test suite on your own system?  
Is there any particular reason you used the master rather then develop branch.  I'd like to be able to merge them in to develop, run tests on my machine, and if everything is OK, merge develop into master.  But having them in master prevents this.

---

## Comment 2

> Username: johnmcfarlane  
> Created_at: 2016-08-24 18:38:28 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/12#issuecomment-242166747  

My fork has master as the default. Have you set your default branch to  
develop here (https://github.com/robertramey/safe_numerics/settings/branches)?  
That probably governs where PRs are automatically directed.  
  
I will make this change later on this evening.  
  
On Wed, Aug 24, 2016 at 10:37 AM, Robert Ramey notifications@github.com  
wrote:  
  
> These changes look correct to me.  
> Did you run the test suite on your own system?  
> Is there any particular reason you used the master rather then develop  
> branch. I'd like to be able to merge them in to develop, run tests on my  
> machine, and if everything is OK, merge develop into master. But having  
> them in master prevents this.  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> https://github.com/robertramey/safe_numerics/pull/12#issuecomment-242147891,  
> or mute the thread  
> https://github.com/notifications/unsubscribe-auth/AAszNx8U2wb7kCGoMbfLXBh4kMUmf-tHks5qjIFKgaJpZM4JgSBL  
> .

---

## Comment 3

> Username: johnmcfarlane  
> Created_at: 2016-08-27 20:36:50 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/12#issuecomment-242939408  

Sorry for the delay. This is now rebased off of develop and PR is against develop.  
  
I couldn't find any instructions on running tests. The project doesn't build on my system (Ubuntu 16.04 x86-64 with either clang version 3.8 or gcc 5.4.0). Once the fixes from this PR are applied, I presumably run:  
  
``` shell  
cmake <safe_numerics root>  
make  
ctest  
```  
  
But that fails with a number of errors such as:  
  
> Unable to find executable: /home/john/github/johnmcfarlane/safe_numerics/build/examples/example81  
> and then seems to hang. Would it be possible to add some instructions to the README.md?

---

## Comment 4

> Username: robertramey  
> Created_at: 2016-08-27 21:37:16 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/12#issuecomment-242942262  

Thanks for taking the trouble to do this.  
  
I've got a couple of very minor changes to merge in then I'll test.  
  
I've been using CMake to generate an xcode project which then has a test   
project to be run in it.  This works pretty well.  I believe I've also   
used the method you tried and had good results.  But I haven't done that   
lately so I'll try again and make sure it works and I'll let you know.  
  
On 8/27/16 1:36 PM, John McFarlane wrote:  
  
> Sorry for the delay. This is now rebased off of develop and PR is   
> against develop.  
>   
> I couldn't find any instructions on running tests. The project doesn't   
> build on my system (Ubuntu 16.04 x86-64 with either clang version 3.8   
> or gcc 5.4.0). Once the fixes from this PR are applied, I presumably run:  
>   
> cmake<safe_numerics root>  
> make  
> ctest  
>   
> But that fails with a number of errors such as:  
>   
> ```  
> Unable to find executable:  
> /home/john/github/johnmcfarlane/safe_numerics/build/examples/example81  
> and then seems to hang. Would it be possible to add some  
> instructions to the README.md?  
> ```  
>   
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub   
> https://github.com/robertramey/safe_numerics/pull/12#issuecomment-242939408,   
> or mute the thread   
> https://github.com/notifications/unsubscribe-auth/AB4R3C4H449g2WQHtvOXYdXHFdvqtmh8ks5qkJ_igaJpZM4JgSBL.  
  
##   
  
Robert Ramey  
www.rrsd.com  
(805)569-3793

---
