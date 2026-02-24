# #22 - Please clarify the license for boostbook/xsl/caramel [Open]

> Username: jwakely  
> Created at: 2023-07-13 11:14:27 UTC  
> Updated at: 2023-12-21 00:46:34 UTC  
> Url: https://github.com/boostorg/boostbook/issues/22  

The directory contains this license file, which seems to be a form of the [MTLL](https://spdx.org/licenses/MTLL.html) license:  
https://github.com/boostorg/boostbook/blob/develop/xsl/caramel/LICENSE  
  
However, the other three files in that directory all say:  
  
   Distributed under the Boost Software License, Version 1.0.  
   (See accompanying file LICENSE_1_0.txt or copy at  
   http://www.boost.org/LICENSE_1_0.txt)  
  
So which is it? If those files are distributed under BSL-1.0 then why is the other license file there?  
  
This complicates things for downstream packagers like Linux distros, who need to audit and record all relevant licenses used by a package.

---

## Comment 1

> Username: jwakely  
> Created at: 2023-07-13 11:16:33 UTC  
> Url: https://github.com/boostorg/boostbook/issues/22#issuecomment-1634062224  

> So which is it? If those files are distributed under BSL-1.0 then why is the other license file there?  
  
And if those files really are covered by that license file, then they shouldn't claim otherwise.

---

## Comment 2

> Username: Lastique  
> Created at: 2023-12-21 00:46:33 UTC  
> Url: https://github.com/boostorg/boostbook/issues/22#issuecomment-1865324070  

I think this should be addressed to @DougGregor as, according to history, he was the one who added these files.
