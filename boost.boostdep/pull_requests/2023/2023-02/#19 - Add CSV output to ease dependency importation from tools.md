# #19 Add CSV output to ease dependency importation from tools [Merged]

> Username: igaztanaga  
> Created at: 2023-02-07 22:54:55 UTC  
> Updated at: 2023-02-12 17:43:39 UTC  
> Merged at: 2023-02-10 16:11:57 UTC  
> Closed at: 2023-02-10 16:11:57 UTC  
> Url: https://github.com/boostorg/boostdep/pull/19  

First proposal for CSV output. Since boostdep can mix different operations in the same command:  
  
`boostdep --csv --primary assert --secondary container_hash --header boost/container/deque.hpp --reverse interprocess --subset assert`  
  
...a table is created for each operation, prefixed by a table marker. The marker and the table header can be excluded using --csv-no-xxx options.

---

## Comment 1

> Username: pdimov  
> Created_at: 2023-02-08 00:06:29 UTC  
> Url: https://github.com/boostorg/boostdep/pull/19#issuecomment-1421675340  

Very clean and professional, thank you. However...  
  
1. Please remove the .html changes from the PR.  
2. Could you please separate the orthogonal changes into separate commits? One for the warning (int/size_t) fixes, one for the addition of `footer()`, one for the replacement of `bool html` with an enum, and one final one with the csv additions? (Actually make that two final ones, one for the .cpp, one for the .qbk.)  
  
Thanks in advance.

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2023-02-08 08:41:39 UTC  
> Url: https://github.com/boostorg/boostdep/pull/19#issuecomment-1422231463  

Thanks for the quick reply.  
  
1- OK  
2- OK, I will do my best.  
3- Maybe a couple simple tests in the line of "assert-primary.txt" checks might be desirable too to detect some basic breaking changes.

---

## Comment 3

> Username: pdimov  
> Created_at: 2023-02-10 01:41:23 UTC  
> Url: https://github.com/boostorg/boostdep/pull/19#issuecomment-1425061122  

Where does the "table marker" format come from? I'm not aware of it being a part of the CSV format.

---

## Comment 4

> Username: igaztanaga  
> Created_at: 2023-02-10 14:02:52 UTC  
> Updated_at: 2023-02-12 17:43:39 UTC  
> Url: https://github.com/boostorg/boostdep/pull/19#issuecomment-1425854372  

There is no CSV standard that supports multi-tables and the default behaviour of boostdep is to have multiple operations (and thus, tables) on the same output (stdout), so the patch proposes some markers not to loose information that is present on txt or html formats. The proposed marker one is one with easy parsing (first "[" after empty newline) that I've used before (similar cases found in the web are https://es.mathworks.com/matlabcentral/answers/636955-read-data-from-csv-that-contains-multiple-tables-one-after-the-other-vertically).  
  
To obtain a RFC compliant (single table) CSV the user should use "--csv-no-table-marker" and specify a single command (e.g. only --primary).  
  
If RFC compliance is important, we can:  
  
- Document all this so that a user that wants RFC compliance knows which options must use (single operation, removing marker)  
  
OR  
  
- Limit boostdep to single operation mode when --csv is used and remove the marker. The type of the operation and the name of the module/header, etc. will be lost in the ouput so it should be encoded somewhere else (e.g. in the file name if the output is redirected).

---
