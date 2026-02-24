# #322 Add PEP-3149 compliant extension suffix discovery. [Closed]

> Username: xnox  
> Created at: 2018-07-04 16:19:02 UTC  
> Updated at: 2021-10-02 20:59:25 UTC  
> Closed at: 2021-06-11 01:54:36 UTC  
> Url: https://github.com/boostorg/build/pull/322  

Use python interpreter to query correct extension name, rather than guessing.  
  
This correctly detects:  
`.so`  
`_d.so`  
`.cpython-36m-x86_64-linux-gnu.so`  
and so on.  
  
And should be universally compatible.

---

## Comment 1

> Username: stale[bot]  
> Created_at: 2021-05-29 16:21:51 UTC  
> Url: https://github.com/boostorg/build/pull/322#issuecomment-850859501  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.

---

## Comment 2

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:25 UTC  
> Url: https://github.com/boostorg/build/pull/322#issuecomment-932819641  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
