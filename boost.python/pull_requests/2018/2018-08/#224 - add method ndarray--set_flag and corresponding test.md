# #224 add method ndarray::set_flag and corresponding test [Open]

> Username: HDembinski  
> Created at: 2018-08-01 21:12:43 UTC  
> Updated at: 2020-11-14 02:30:48 UTC  
> Url: https://github.com/boostorg/python/pull/224  

Second attempt, no branching from develop. Addresses #171.

---

## Comment 1

> Username: HDembinski  
> Created_at: 2018-08-03 20:44:54 UTC  
> Url: https://github.com/boostorg/python/pull/224#issuecomment-410371418  

I don't understand the failing appveyor test. This looks like a configuration issue. I don't see that anything is compiled.

---

## Comment 2

> Username: HDembinski  
> Created_at: 2018-08-21 07:44:20 UTC  
> Url: https://github.com/boostorg/python/pull/224#issuecomment-414581613  

Appveyor fails with this message:  
pip install --disable-pip-version-check --user --upgrade pip  
ERROR: To modify pip, please run the following command:  
c:\python27\python.exe -m pip install --disable-pip-version-check --user --upgrade pip  
  
This is not caused by this PR.

---
