# #522 - Combined stdout and stderr in v2 [Closed]

> Username: HunterZ  
> Created at: 2025-10-21 02:17:16 UTC  
> Updated at: 2025-10-21 07:30:21 UTC  
> Closed at: 2025-10-21 07:30:21 UTC  
> Url: https://github.com/boostorg/process/issues/522  

Followup to https://github.com/boostorg/process/issues/460 for a couple of reasons:  
1. The proposed approach is awful.  
2. The proposed approach is not documented.  
3. The proposed approach is reported to not work on Linux.  
  
Can we please get first-class support for doing this, as was provided in v1?

---

## Comment 1

> Username: HunterZ  
> Created at: 2025-10-21 02:31:53 UTC  
> Url: https://github.com/boostorg/process/issues/522#issuecomment-3424433485  

I should also mention that for some reason `boost::process::process_stdio` lets you specify the same pipe for stdout and stderr, but faceplants on Windows at runtime with a cryptic "already open" exception.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2025-10-21 02:56:36 UTC  
> Url: https://github.com/boostorg/process/issues/522#issuecomment-3424477292  

> The proposed approach is awful.  
  
Can you expand on what you mean by this?

---

## Comment 3

> Username: HunterZ  
> Created at: 2025-10-21 03:19:15 UTC  
> Url: https://github.com/boostorg/process/issues/522#issuecomment-3424516566  

> > The proposed approach is awful.  
>   
> Can you expand on what you mean by this?  
  
Nobody new to Boost.Process v2 (let alone Boost.Asio) would come up with this on their own in a million years, yet it's a basic use case for running processes and capturing their output.  
  
If this is the approach that people are actually intended to use, it desperately needs to be captured in the Boost.Process v2 documentation.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2025-10-21 03:26:28 UTC  
> Url: https://github.com/boostorg/process/issues/522#issuecomment-3424529278  

That doesn't explain why it's awful and what a better approach would be.  
I'd be open to alternative solutions if they don't involve some operator overloading and pseudo keywords.

---

## Comment 5

> Username: HunterZ  
> Created at: 2025-10-21 03:38:48 UTC  
> Url: https://github.com/boostorg/process/issues/522#issuecomment-3424547271  

> That doesn't explain why it's awful and what a better approach would be. I'd be open to alternative solutions if they don't involve some operator overloading and pseudo keywords.  
  
I don't understand what you're saying. I just want a documented solution for capturing both stdout and stderr outputs from running a process into a string, which works on both Linux and Windows.  
  
This was a supported, documented use case of Boost.Process v1, and deserves to be so in v2:  
  
<img width="971" height="383" alt="Image" src="https://github.com/user-attachments/assets/0951c0c8-3f7c-439b-ad31-e055d4ac887a" />

---

## Comment 6

> Username: HunterZ  
> Created at: 2025-10-21 05:09:00 UTC  
> Updated at: 2025-10-21 05:10:03 UTC  
> Url: https://github.com/boostorg/process/issues/522#issuecomment-3424692250  

Regarding my "awful" comment: One of my issues is that it involves using a _writable_ pipe for both stdout and stderr, but according to the current Boost.Process V2 docs, writable pipes are not valid initializers for stdout/stderr:  
  
<img width="779" height="238" alt="Image" src="https://github.com/user-attachments/assets/0844abe2-6732-4e00-9da4-1d832493d04e" />  
  
Why would anyone ever think to use writable pipes for stdout/stderr given this documentation?  
  
Also, why would they think to use the same pipe for both stdout and stderr, when doing that with readable pipes results in an "already open" exception (at least in Windows)?  
  
You have to understand that this is all incredibly obscure and confusing to someone who isn't deeply immersed in this.

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2025-10-21 07:01:12 UTC  
> Url: https://github.com/boostorg/process/issues/522#issuecomment-3425038473  

Well the `writable_pipe` falls under the previous category, but I won't argue that this should be documented better.  
  
I'll add some documentation then.
