# #110 - Boost release tag frozen since 3 years ? [Closed]

> Username: ilelann  
> Created at: 2021-07-10 10:50:42 UTC  
> Updated at: 2021-07-10 19:20:09 UTC  
> Closed at: 2021-07-10 19:20:09 UTC  
> Url: https://github.com/boostorg/program_options/issues/110  

Hello, I am puzzled by https://github.com/boostorg/program_options/releases  
  
I fell on this while trying to understand why I got warnings about boost/bind placeholders in global namespace even though this is fixed since long in this repository : https://github.com/boostorg/program_options/commit/a2379420f67071e997d0d2aecd1d782c04db9ce7   
  
Any reason why new Boost releases don't get new program_options code ?

---

## Comment 1

> Username: vprus  
> Created at: 2021-07-10 18:57:03 UTC  
> Url: https://github.com/boostorg/program_options/issues/110#issuecomment-877686929  

Thanks for the question. Unfortunately, I don't have a good answer. The commit you mention is this  
  
```  
commit a2379420f67071e997d0d2aecd1d782c04db9ce7  
Author: Mateusz Łoskot <mateusz@loskot.net>  
Date:   Fri Mar 6 08:45:25 2020 +0100  
```   
  
After applying it to the develop branch, I have failed to merge it to master, so it stayed on the develop branch until this   
  
```  
commit 43cc2af3b17e883cfa13eeb1c2b09ebf4a1236a9  
Merge: 0414abe bc0c90a  
Author: Peter Dimov <pdimov@gmail.com>  
Date:   Tue Feb 2 19:19:09 2021 +0200  
  
    Merge branch 'master' into develop  
```  
Release 1.76.0 was made on April 16, so I am really unsure why a merge made on Feb 2 was not included. I'll ask on Slack.

---

## Comment 2

> Username: vprus  
> Created at: 2021-07-10 19:15:30 UTC  
> Url: https://github.com/boostorg/program_options/issues/110#issuecomment-877689913  

After discussion on Slack, the conclusion is that the above is entirely my fault. I forgot to merge that commit and another one after, and there were no further commits, so the master branch only got those commits around Jun 9 as part of unrelated work. Hopefully next release will include the change.

---

## Comment 3

> Username: vprus  
> Created at: 2021-07-10 19:20:09 UTC  
> Url: https://github.com/boostorg/program_options/issues/110#issuecomment-877690628  

SInce the master and develop branches are now exactly in sync, I'll close the issue. Thanks for the report!
