# #125 - JSON values are represented as blobs, not as strings [Closed]

> Username: anarthal  
> Created at: 2023-03-03 15:01:17 UTC  
> Updated at: 2023-03-05 13:01:46 UTC  
> Closed at: 2023-03-05 13:01:45 UTC  
> Url: https://github.com/boostorg/mysql/issues/125  

MySQL has a special JSON type, with a different protocol type. As we don't do any special handling for it, JSON gets represented as a blob, which is incorrect, as MySQL sends the JSON object as a string.

---

## Comment 1

> Username: anarthal  
> Created at: 2023-03-04 09:15:29 UTC  
> Url: https://github.com/boostorg/mysql/issues/125#issuecomment-1454673430  

* There is an incompatibility between MariaDB and MySQL, as MySQL does have a JSON type, while in MariaDB it's an alias.  
* We're not handling `TEXT` columns with binary collations (e.g. `utf8_bin`) correctly.

---

## Comment 2

> Username: anarthal  
> Created at: 2023-03-05 13:01:45 UTC  
> Url: https://github.com/boostorg/mysql/issues/125#issuecomment-1455085810  

Solved in 643e39f85e6a577336b0e581d39f17a4254475bf
