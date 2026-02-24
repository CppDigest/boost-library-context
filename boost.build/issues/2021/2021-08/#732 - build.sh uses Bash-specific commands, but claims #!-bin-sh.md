# #732 - build.sh uses Bash-specific commands, but claims #!/bin/sh [Closed]

> Username: Quuxplusone  
> Created at: 2021-08-06 14:52:24 UTC  
> Updated at: 2021-08-07 15:50:58 UTC  
> Closed at: 2021-08-07 15:50:58 UTC  
> Url: https://github.com/boostorg/build/issues/732  

Sometime between 1.51 and 1.76, Boost's "build.sh" script started using the `local` keyword, which is legal in Bash but not in Solaris's default /bin/sh. I patched this locally by just switching the shebang from `/bin/sh` to `/bin/bash`.  
  
Here's the version of /bin/sh I see on this Solaris machine:  
```  
-bash-4.1$ uname -a  
SunOS hostname.redacted.example.com 5.11 11.3 sun4v sparc sun4v  
  
-bash-4.1$ /bin/sh --version  
  version         sh (AT&T Research) 93u 2011-02-08  
  
-bash-4.1$ /bin/bash --version  
GNU bash, version 4.1.17(1)-release (sparc-sun-solaris2.11)  
Copyright (C) 2009 Free Software Foundation, Inc.  
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>  
  
This is free software; you are free to change and redistribute it.  
There is NO WARRANTY, to the extent permitted by law.  
```  
  
And here's the exact patch I'm applying locally. It would be nice to upstream this:  
  
```  
--- old/tools/build/src/engine/build.sh	2021-04-13 12:23:21.000000000 -0400  
+++ new/tools/build/src/engine/build.sh	2021-08-06 10:36:10.000000000 -0400  
@@ -1,4 +1,4 @@  
-#!/bin/sh  
+#!/bin/bash  
   
 #~ Copyright 2002-2020 Rene Rivera.  
 #~ Distributed under the Boost Software License, Version 1.0.  
```  
  
(FWIW, I was also bitten by boostorg/boost#532.)

---

## Comment 1

> Username: mclow  
> Created at: 2021-08-07 15:50:47 UTC  
> Url: https://github.com/boostorg/build/issues/732#issuecomment-894671377  

I believe that this is a dupe of https://github.com/boostorg/build/pull/722, and has been fixed.
