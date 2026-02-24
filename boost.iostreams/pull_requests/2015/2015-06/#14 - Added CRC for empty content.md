# #14 Added CRC for empty content [Closed]

> Username: johnwallace123  
> Created at: 2015-06-05 19:55:23 UTC  
> Updated at: 2016-12-06 19:37:05 UTC  
> Closed at: 2016-12-06 19:37:05 UTC  
> Url: https://github.com/boostorg/iostreams/pull/14  

Added the CRC in the case no data was extracted (length == 0).  By  
definition, the CRC in this case will be 0.  This will allow the  
gzip_decompressor to match the footer with the calculated CRC instead of  
throwing a gzip_error.

---

## Comment 1

> Username: eldiener  
> Created_at: 2016-12-03 23:57:50 UTC  
> Url: https://github.com/boostorg/iostreams/pull/14#issuecomment-264674117  

PRs are never made against the 'master' branch.

---

## Comment 2

> Username: eldiener  
> Created_at: 2016-12-06 19:37:05 UTC  
> Url: https://github.com/boostorg/iostreams/pull/14#issuecomment-265250083  

This has also already been fixed on develop by always providing a crc.

---
