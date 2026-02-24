# #75 - Support no-exception environments. [Closed]

> Username: liarokapisv  
> Created at: 2019-10-24 09:42:10 UTC  
> Updated at: 2019-12-13 17:39:05 UTC  
> Closed at: 2019-12-13 17:39:05 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/75  

The library currently cannot be used in projects where exceptions are disabled. This is due to the `exception_policies.hpp` header defining the `throw_exception` policy that makes use of the throw statement which is disallowed (forced by the compiler) in a no-exception setting. This is currently blocking us from using `safe_numerics` in our project. We want to make use of the trap policy variants but due to the `throw_exception` policy, it is not possible to include the relevant header.   
A low-cost solution would be to either define a macro and let the user override the behaviour or even separate the policies into different headers.

---

## Comment 1

> Username: johnmcfarlane  
> Created at: 2019-10-24 11:18:29 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/75#issuecomment-545871464  

FYI cnl:: overflow_integer supports trap behavior by default and can be  
built in projects with EH disabled. https://github.com/johnmcfarlane/cnl  
  
On Thu 24 Oct 2019, 10:42 Liarokapis Alexandros, <notifications@github.com>  
wrote:  
  
> The library currently cannot be used in projects where exceptions are  
> disabled. This is due to the exception_policies.hpp header defining the  
> throw_exception policy that makes use of the throw statement which is  
> disallowed (forced by the compiler) in a no-exception setting. This is  
> currently blocking us from using safe_numerics in our project. We want to  
> make use of the *trap* policy variants but due to the throw_exception  
> policy, it is not possible to include the relevant header.  
> A low-cost solution would be to either define a macro and let the user  
> override the behaviour or even seperate the policies into different headers.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/safe_numerics/issues/75?email_source=notifications&email_token=AAFTGN4UVOYO5WZQZ2KNA5DQQFUXHA5CNFSM4JERPZP2YY3PNVWWK3TUL52HS4DFUVEXG43VMWVGG33NNVSW45C7NFSM4HUB4Z6Q>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AAFTGN35ESVP7EMNFT7ND5LQQFUXHANCNFSM4JERPZPQ>  
> .  
>

---

## Comment 2

> Username: robertramey  
> Created at: 2019-10-25 18:38:44 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/75#issuecomment-546465811  

I've pushed some changes to the develop branch which I think will address this issue.  Please try using this and let me know how it works.

---

## Comment 3

> Username: liarokapisv  
> Created at: 2019-10-29 15:04:17 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/75#issuecomment-547466595  

Thanks for responding this quickly to the issue! The changes seem fine in a first inspection,  I will try to test a few more configurations just for completeness sake but the changes are minimal, I don't think there will be any issues.
