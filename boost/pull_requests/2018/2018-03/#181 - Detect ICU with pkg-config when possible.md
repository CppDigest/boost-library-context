# #181 Detect ICU with pkg-config when possible. [Merged]

> Username: dimztimz  
> Created at: 2018-03-12 14:46:27 UTC  
> Updated at: 2018-11-03 22:10:27 UTC  
> Merged at: 2018-08-13 19:07:20 UTC  
> Closed at: 2018-08-13 19:07:20 UTC  
> Url: https://github.com/boostorg/boost/pull/181  

Better detection of ICU in bootstrap.sh with the help of pkg-config.  
  
Checking for the header under `/usr/include/unicode` is not very robust because on some Linux distros the header is not dirctly in include. E.g. on older Ubuntu 14.04 it is in `/usr/include/x86_64-linux-gnu/unicode`.  
Asking pkg-config is a better way to check if the library is installed.

---

## Review 1 [Approved]

> Username: jeking3  
> Created_at: 2018-05-01 01:54:06 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/boost/pull/181#pullrequestreview-116488431  

Tried this in my docker container, finds icu in /usr.  Verified it went down the new code path.

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-08-12 22:02:27 UTC  
> Url: https://github.com/boostorg/boost/pull/181#issuecomment-412374926  

@mjcaisse please merge - thanks.

---

## Comment 3

> Username: mjcaisse  
> Created_at: 2018-08-13 16:05:38 UTC  
> Url: https://github.com/boostorg/boost/pull/181#issuecomment-412571517  

`command` is a builtin command for bash but I don't think it works for the standard `/bin/sh` interpreter. I'm not sure if `which` is more universal for checking the existence of `pkg-config` but platforms that don't default `/bin/sh` to bash are going to fail with the script if this is merged.  
  
Thoughts?

---

## Comment 4

> Username: dimztimz  
> Created_at: 2018-08-13 18:58:13 UTC  
> Url: https://github.com/boostorg/boost/pull/181#issuecomment-412626799  

Command is more portable than which https://stackoverflow.com/questions/592620/how-to-check-if-a-program-exists-from-a-bash-script  
It is part of POSIX http://pubs.opengroup.org/onlinepubs/9699919799/utilities/command.html

---

## Comment 5

> Username: mjcaisse  
> Created_at: 2018-08-13 19:06:57 UTC  
> Url: https://github.com/boostorg/boost/pull/181#issuecomment-412629408  

Thank you @dimztimz for the feedback.

---

## Comment 6

> Username: pdimov  
> Created_at: 2018-10-25 14:21:33 UTC  
> Url: https://github.com/boostorg/boost/pull/181#issuecomment-433070610  

Reminder that this commit has not been cherry-picked into master yet.

---
