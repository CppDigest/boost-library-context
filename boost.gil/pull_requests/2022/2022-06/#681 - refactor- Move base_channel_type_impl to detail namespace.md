# #681 refactor: Move base_channel_type_impl to detail namespace [Open]

> Username: marco-langer  
> Created at: 2022-06-04 09:15:40 UTC  
> Updated at: 2024-08-20 13:02:28 UTC  
> Url: https://github.com/boostorg/gil/pull/681  

### Description  
  
This PR moves base_channel_type_impl to detail namespace.  
  
### Tasklist  
  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2022-06-26 09:24:46 UTC  
> Url: https://github.com/boostorg/gil/pull/681#issuecomment-1166466925  

@marco-langer  Why closing?

---

## Comment 2

> Username: marco-langer  
> Created_at: 2022-06-26 09:31:46 UTC  
> Url: https://github.com/boostorg/gil/pull/681#issuecomment-1166468770  

@mloskot I wanted to discuss this PR with you before working on it again, but I thought currently the Boost 1.80 PRs are more important.  
  
I think those structs belong to the detail namespace, but they are currently not. However,  just moving stuff around would violate boosts deprecation policy, right?   
  
So, some of my solutions:  
  
- Keep the PR as is and have a possible breaking change if someone relied on those structs (I doubt it, but I am not sure).  
- Create new detail structs, redirect the library calls to the detail structs and deprecate the old structs in the gil namespace.  
- Leave everything as it is currently implemented and don't wake up sleeping dogs.

---

## Comment 3

> Username: mloskot  
> Created_at: 2022-06-26 09:52:26 UTC  
> Url: https://github.com/boostorg/gil/pull/681#issuecomment-1166474951  

OK. You can always turn PR to Draft.  
  
>  Boost 1.80 PRs are more important.  
  
Yes, indeed.  
  
> However, just moving stuff around would violate boosts deprecation policy, right?  
  
Generally speaking, yes.  
However, I doubt any user directly refers to `base_channel_type_impl` type. We can not exclude such possibility though.  
  
Let's postpone it.

---
