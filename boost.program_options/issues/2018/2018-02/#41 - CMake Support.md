# #41 - CMake Support [Closed]

> Username: MikeGitb  
> Created at: 2018-02-17 18:34:55 UTC  
> Updated at: 2018-03-21 08:10:59 UTC  
> Closed at: 2018-03-21 08:10:58 UTC  
> Url: https://github.com/boostorg/program_options/issues/41  

Are there any plans to add cmake support in the near future?

---

## Comment 1

> Username: jeking3  
> Created at: 2018-03-06 14:25:13 UTC  
> Url: https://github.com/boostorg/program_options/issues/41#issuecomment-370797450  

CMake has been discussed at length on the boost mailing list and it is a project in motion, but slow motion presently.

---

## Comment 2

> Username: MikeGitb  
> Created at: 2018-03-07 09:15:10 UTC  
> Updated at: 2018-03-07 09:16:46 UTC  
> Url: https://github.com/boostorg/program_options/issues/41#issuecomment-371073950  

Yeah, I'm definitely hoping for a boost wide solution, but I have serious doubts that that will happen in the near future.   
But even without that, it should be relatively easy to provide a cmake script that builds program_options and provides the proper targets other projects can import. I'm less sure about the tests though because I don't have any experience with boost.test.

---

## Comment 3

> Username: vprus  
> Created at: 2018-03-07 09:53:15 UTC  
> Url: https://github.com/boostorg/program_options/issues/41#issuecomment-371084663  

I am afraid I have no interest in anything cmake.  
  
On Wed, 7 Mar 2018, 10:15 MikeGitb, <notifications@github.com> wrote:  
  
> Yeah, I'm definitely going for a boost wide solution, but I have serious  
> doubts that that will happen in the near future.  
> But even without that, it should be relatively easy to provide a cmake  
> script that builds program_options and provides the proper targets other  
> projects can import. I'm less sure about the tests though because I don't  
> have any experience with boost.test.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/program_options/issues/41#issuecomment-371073950>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AANye4mtdfKTXEjj_Gegd6j4C4hKLG6Kks5tb6UegaJpZM4SJaUP>  
> .  
>  
--   
Vladimir Prus  
http://vladimirprus.com

---

## Comment 4

> Username: MikeGitb  
> Created at: 2018-03-07 10:52:12 UTC  
> Url: https://github.com/boostorg/program_options/issues/41#issuecomment-371100865  

Sad, but fair enough. Would you accept an PR if the bcm library should be accepted to into boost?

---

## Comment 5

> Username: MikeGitb  
> Created at: 2018-03-21 08:10:58 UTC  
> Url: https://github.com/boostorg/program_options/issues/41#issuecomment-374860124  

I take that as a no.
