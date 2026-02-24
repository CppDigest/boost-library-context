# #128 Replace broken links. [Merged]

> Username: tinko92  
> Created at: 2019-04-23 07:11:03 UTC  
> Updated at: 2019-04-24 17:54:07 UTC  
> Merged at: 2019-04-24 17:54:06 UTC  
> Closed at: 2019-04-24 17:54:07 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/128  

Hi, this commit fixes a couple of broken links. Basically, it contains the following changes:  
  
1) The link http://754r.ucbtest.org/standards/754.pdf , which doesn't work, is replaced by https://doi.org/10.1109/IEEESTD.1985.82928 , which is a permalink to the document on the IEEEXplore ( https://ieeexplore.ieee.org/document/30711 ). Unfortunately, they don't provide the actual PDF for free without some institutional login but given that the original site seems to have removed it and the actual document contains a copyright notice prohibiting reproduction without written permission, maybe it was not meant to be provided for free.  
  
2) The link http://www.boost.org/LICENSE_1_ at the end of a line is replaced by https://www.boost.org/LICENSE_1_0.txt using the following commands  
  
`find . -path ./.git -prune -o -type f -name '*.cpp' -print0 | xargs -0 sed -i 's/http:\/\/www\.boost\.org\/LICENSE_1_$/https:\/\/www.boost.org\/LICENSE_1_0\.txt/g'`  
`find . -path ./.git -prune -o -type f -name '*.hpp' -print0 | xargs -0 sed -i 's/http:\/\/www\.boost\.org\/LICENSE_1_$/https:\/\/www.boost.org\/LICENSE_1_0\.txt/g'`  
  
in all occurrences, some of which were rendered to dead links in the HTML documentation.  
  
3) What looks like Search & Replace for 0 at test/test_nothrow_cpp_int.cpp has been fixed.  
  
edit:  
4) The link to info about pibook was also broken. Replaced it with a permalink to the springer information page about the book that provides similar information to the original link (according to web archive).

---
