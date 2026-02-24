# #346 - `--prefix=~/.local` uses the literal subdirectory `~/.local` [Open]

> Username: pdimov  
> Created at: 2018-10-05 12:59:26 UTC  
> Updated at: 2021-06-11 01:55:44 UTC  
> Url: https://github.com/boostorg/build/issues/346  

The intent is of course tilde expansion to cause this to be treated as `--prefix=$HOME/.local`, but it isn't.

---

## Comment 1

> Username: Kojoley  
> Created at: 2018-10-11 12:52:15 UTC  
> Url: https://github.com/boostorg/build/issues/346#issuecomment-428943246  

I hit the same issue recently. Did not even recognize that I have not updated the previous content until finding `~` folder.

---

## Comment 2

> Username: grisumbras  
> Created at: 2019-05-28 12:00:12 UTC  
> Url: https://github.com/boostorg/build/issues/346#issuecomment-496486798  

Expansion of tilde is supposed to be done by the shell. So, the question is, why didn't your shell do it?

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-06-11 01:55:06 UTC  
> Url: https://github.com/boostorg/build/issues/346#issuecomment-859203375  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
