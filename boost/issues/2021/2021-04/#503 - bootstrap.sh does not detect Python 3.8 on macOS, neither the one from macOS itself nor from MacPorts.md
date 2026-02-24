# #503 - bootstrap.sh does not detect Python 3.8 on macOS, neither the one from macOS itself nor from MacPorts [Open]

> Username: fdik  
> Created at: 2021-04-12 22:35:28 UTC  
> Updated at: 2021-05-27 11:54:41 UTC  
> Url: https://github.com/boostorg/boost/issues/503  

After running:  
`$ ./bootstrap.sh --prefix=$HOME --with-python=/opt/local/bin/python3.8`  
still Python 2.7 of macOS is detected. Tried with and without path.  
Expected would be that it takes the mentioned Python version from this binary.  
A workaround is to use  
`$ sudo port select python python38`  
This makes Python 3.8 the default reachable with just `python`, and `./bootstrap.sh` works. But this is most likely not what the author of this script intended.

---

## Comment 1

> Username: fdik  
> Created at: 2021-04-12 22:40:07 UTC  
> Url: https://github.com/boostorg/boost/issues/503#issuecomment-818289337  

[debug.txt](https://github.com/boostorg/boost/files/6300183/debug.txt)

---

## Comment 2

> Username: DMaroo  
> Created at: 2021-05-27 11:54:41 UTC  
> Url: https://github.com/boostorg/boost/issues/503#issuecomment-849569862  

What output do you get when you run this manually?  
  
`/opt/local/Library/Frameworks/Python.framework/Versions/3.8/bin/python -c "from sys import *; print('version=%d.%d\nplatform=%s\nprefix=%s\nexec_prefix=%s\nexecutable=%s' % (version_info[0],version_info[1],platform,prefix,exec_prefix,executable))" 2>&1`
