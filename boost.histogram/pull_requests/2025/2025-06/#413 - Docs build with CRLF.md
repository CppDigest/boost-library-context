# #413 Docs build with CRLF [Merged]

> Username: sdarwin  
> Created at: 2025-06-06 13:21:05 UTC  
> Updated at: 2025-06-12 07:13:04 UTC  
> Merged at: 2025-06-12 07:13:03 UTC  
> Closed at: 2025-06-12 07:13:04 UTC  
> Url: https://github.com/boostorg/histogram/pull/413  

Boost superproject docs builds are failing:    
  
https://app.circleci.com/pipelines/github/boostorg/boost?branch=develop  
  
cc. @pdimov

---

## Comment 1

> Username: HDembinski  
> Created_at: 2025-06-06 18:45:16 UTC  
> Url: https://github.com/boostorg/histogram/pull/413#issuecomment-2950169711  

There has to be a better way than reverting. The old code doesn't work on platforms which do not create the python alias, e.g. Ubuntu.  
  
The proper solution here is to get the interpreter from the Python module, but it doesn't expose the interpreter after detecting it AFAIU.

---

## Comment 2

> Username: HDembinski  
> Created_at: 2025-06-06 18:51:07 UTC  
> Url: https://github.com/boostorg/histogram/pull/413#issuecomment-2950189460  

By the way the boost book docs on the website all contain faulty characters, they used to look correct in the past.

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-06-06 18:53:19 UTC  
> Url: https://github.com/boostorg/histogram/pull/413#issuecomment-2950201475  

I remember having issues with `python` on containers and switching to `python3`, which seems to work everywhere.

---

## Comment 4

> Username: sdarwin  
> Created_at: 2025-06-06 18:55:27 UTC  
> Url: https://github.com/boostorg/histogram/pull/413#issuecomment-2950212484  

- A python virtual environment creates a `python` alias.  
- `apt-get install python-is-python3` creates a `python` alias.  
  
Another option is to switch this to `python3`.      
  
Microsoft Windows with chocolatey installs `python` but not `python3`.    
  
If you prefer python3, that may assist on some installations of Ubuntu (not all will require that), but it breaks Windows.       
I will update the PR.  
  
>  boost book docs on the website   
  
Please file a more detailed bug report at https://github.com/boostorg/website-v2 , with specific URLs and links that contain the problems, with explanation.

---

## Comment 5

> Username: sdarwin  
> Created_at: 2025-06-06 19:04:36 UTC  
> Url: https://github.com/boostorg/histogram/pull/413#issuecomment-2950275310  

There is a jamfile syntax `PYTHON  <python.interpreter> ;`  It might help.  However if that requires experimentation just put back `python` for the moment.

---

## Comment 6

> Username: sdarwin  
> Created_at: 2025-06-09 13:12:12 UTC  
> Url: https://github.com/boostorg/histogram/pull/413#issuecomment-2955756712  

Updated the PR to check the operating system. It will use `python` on Windows otherwise `python3`.

---

## Comment 7

> Username: HDembinski  
> Created_at: 2025-06-12 07:13:00 UTC  
> Url: https://github.com/boostorg/histogram/pull/413#issuecomment-2965399221  

Thanks, looks good!

---
