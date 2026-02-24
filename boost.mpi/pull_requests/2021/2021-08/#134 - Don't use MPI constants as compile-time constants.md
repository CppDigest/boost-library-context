# #134 Don't use MPI constants as compile-time constants [Merged]

> Username: eschnett  
> Created at: 2021-08-18 19:22:49 UTC  
> Updated at: 2023-03-14 15:56:06 UTC  
> Merged at: 2023-03-14 09:48:28 UTC  
> Closed at: 2023-03-14 09:48:28 UTC  
> Url: https://github.com/boostorg/mpi/pull/134  

Closes https://github.com/boostorg/mpi/issues/133 .

---

## Comment 1

> Username: aminiussi  
> Created_at: 2022-06-02 13:08:50 UTC  
> Updated_at: 2022-06-02 13:09:58 UTC  
> Url: https://github.com/boostorg/mpi/pull/134#issuecomment-1144844261  

Hi, is the name of your branch HEAD ? as it is also a special name for git.  
  
Thanks

---

## Comment 2

> Username: eschnett  
> Created_at: 2022-06-02 17:28:25 UTC  
> Url: https://github.com/boostorg/mpi/pull/134#issuecomment-1145118771  

Yes. I think I accidentally forgot to create a branch or name it. I didn't spot this until you mentioned it.

---

## Comment 3

> Username: mkre  
> Created_at: 2023-03-09 08:26:54 UTC  
> Url: https://github.com/boostorg/mpi/pull/134#issuecomment-1461551494  

Hi @aminiussi. Can you please consider merging this PR? We would be interested in having this fix in the upstream version of boost.mpi. Thanks!

---

## Comment 4

> Username: aminiussi  
> Created_at: 2023-03-09 11:40:58 UTC  
> Url: https://github.com/boostorg/mpi/pull/134#issuecomment-1461871074  

Hi, I'm sorry for the delay.  
  
I need to get up to date with the build and test system. But the change looks ok and It should be merged soon.

---

## Comment 5

> Username: aminiussi  
> Created_at: 2023-03-09 13:05:11 UTC  
> Url: https://github.com/boostorg/mpi/pull/134#issuecomment-1462033095  

I see that master is closed for the 82.beta at this point.

---

## Comment 6

> Username: aminiussi  
> Created_at: 2023-03-09 13:06:49 UTC  
> Url: https://github.com/boostorg/mpi/pull/134#issuecomment-1462035712  

But it can still be merged on devlop

---

## Comment 7

> Username: aminiussi  
> Created_at: 2023-03-14 09:54:56 UTC  
> Url: https://github.com/boostorg/mpi/pull/134#issuecomment-1467766679  

@eschnett for some reason (maybe because it wasn't on a branch) I merged it though the merge button.  
  
I should have use a patch instead:  
   
```  
/scratch/alainm/view/boostorg/boost/libs/mpi/src/environment.cpp(65): error: identifier "l" is undefined  
    switch(l) {  
           ^  
  
/scratch/alainm/view/boostorg/boost/libs/mpi/src/environment.cpp(266): error: identifier "int2level" is undefined  
    return int2level(level);  
           ^  
  
```  
  
was it actually checked ?  
  
I'm going to fix it.  
  
Cheers

---

## Comment 8

> Username: eschnett  
> Created_at: 2023-03-14 15:56:05 UTC  
> Url: https://github.com/boostorg/mpi/pull/134#issuecomment-1468371425  

Apologies. I am installing Boost via Spack, and it appears that the patch I am applying via Spack is different from the one I used for this PR.

---
