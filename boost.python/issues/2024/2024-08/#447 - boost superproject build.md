# #447 - boost superproject build [Closed]

> Username: sdarwin  
> Created at: 2024-08-18 22:24:44 UTC  
> Updated at: 2024-08-25 16:32:36 UTC  
> Closed at: 2024-08-25 16:32:35 UTC  
> Url: https://github.com/boostorg/python/issues/447  

The most recent update to generate an error in the archive builds is "Update python from develop" today.   
  
 https://app.circleci.com/pipelines/github/boostorg/boost?branch=develop  
  
There are two parallel jobs: EOL=LF and EOL=CRLF.  
  
The problem is connected to line endings. And might be solved by dos2unix.  
  
`/usr/bin/env: ‘python\r’: No such file or directory`  
  
However... the purpose of EOL=CRLF is to test alternative line endings.  how did it work before?  
  
An idea is to not merge the changes of boostorg/python into `master`, and see if the `master` branch continues to build successfully, otherwise.

---

## Comment 1

> Username: sdarwin  
> Created at: 2024-08-18 23:21:03 UTC  
> Url: https://github.com/boostorg/python/issues/447#issuecomment-2295431190  

The issue is with `docca.py`.  Another possible solution, if that were run `python3 docca.py`, python would be able to read both types of line endings.

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2024-08-19 02:35:06 UTC  
> Url: https://github.com/boostorg/python/issues/447#issuecomment-2295556246  

Sorry, I have a hard time figuring out what the problem is from the above logs. The `docca.py` script isn't part of this repo. Is anything wrong with the last merge to boost.python (i.e., https://github.com/boostorg/python/commit/8ca8724ad9eafb5a07a437fb0a676235f1c1cdec) ? Or is this a bug in an upstream tool used to build the `develop` branch ?

---

## Comment 3

> Username: sdarwin  
> Created at: 2024-08-19 08:22:25 UTC  
> Url: https://github.com/boostorg/python/issues/447#issuecomment-2295962821  

From Slack:  
  
grisumbras  7:58 PM  
So, this is how the error manifests:  
docca.jam relies on the feature <python.interpreter> to determine the Python executable to use. It then uses it like "$(PYTHON)" path/to/docca.py. If python.interpeter is empty, the command turns into invocation of docca.py, which due to its shebang line tries to use /usr/bin/env python. I've checked the file, it has \n line endings. I don't see anything in docca repo to make it use special treatment for line endings. Should it?  
Also, I'm pretty sure <python.interpreter> should be set to something, if Python is configured. And it is configured by default by bootstrap.sh.  
  
pdimov  12:42 AM  
so it's the change to boost.python, after all.  
  
--  
  
If this runs, it should be fine:   `"$(PYTHON)" path/to/docca.py`. However, what's happening is "If python.interpeter is empty, the command turns into invocation of docca.py,"  
  
A plain invocation of docca.py crashes (with CRLF line endings). `<python.interpeter>` is required.

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2024-08-19 11:30:28 UTC  
> Url: https://github.com/boostorg/python/issues/447#issuecomment-2296350889  

@grafikrobot do you have any insights into what's going on here ?

---

## Comment 5

> Username: grafikrobot  
> Created at: 2024-08-19 12:16:30 UTC  
> Url: https://github.com/boostorg/python/issues/447#issuecomment-2296434609  

I just said this in Slack..  
  
> I don't see configuration output for python in the release doc building (what would be output from a using python ... ; ). Hence, I suspect, that the doc build was relying on the Boost.Python library incidentally doing that. Which it no longer does with the modular build. As projects are loaded dynamically when actually used. Whereas previously all built projects got forcibly loaded by Jamroot. My conclusion is that the release script is not adding a using python ; to the user/project-config.jam.  
  
So, AFAICT, it's not a problem with Boost.Python. But with the release build scripts.

---

## Comment 6

> Username: stefanseefeld  
> Created at: 2024-08-19 12:31:18 UTC  
> Url: https://github.com/boostorg/python/issues/447#issuecomment-2296462184  

Thanks @grafikrobot . @sdarwin , @pdimov , let me know if there is anything I can do (related to boost.python) to help with this.

---

## Comment 7

> Username: stefanseefeld  
> Created at: 2024-08-25 16:26:48 UTC  
> Url: https://github.com/boostorg/python/issues/447#issuecomment-2308914678  

@sdarwin is there another ticket we can link to to track progress, or should I close this ticket right away ?

---

## Comment 8

> Username: sdarwin  
> Created at: 2024-08-25 16:32:35 UTC  
> Url: https://github.com/boostorg/python/issues/447#issuecomment-2308916791  

I believe `docca` was updated to use python in another way.  CI is fixed.  
`"toolset.using python ;"  `
