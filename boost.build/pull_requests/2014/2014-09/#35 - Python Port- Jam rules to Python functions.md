# #35 Python Port: Jam rules to Python functions [Merged]

> Username: frenchtoast747  
> Created at: 2014-09-04 18:26:59 UTC  
> Updated at: 2021-10-02 22:20:43 UTC  
> Merged at: 2014-09-08 12:02:20 UTC  
> Closed at: 2014-09-08 12:02:20 UTC  
> Url: https://github.com/boostorg/build/pull/35  

There are some Jam rules being used in our Jamfiles that haven't been ported yet. These changes port those rules to functions.  
  
As far as I know, there isn't an equivalent Python standard lib function for `path.reverse()`.  
  
`regex.replace()` and `regex.replace_list()` aren't necessary on the Python side due to having access to the `re` module, however, it's still a great help on the Jam side. The only other solution would be to allow all Python modules (such as `re`) to be importable on the Jam side.

---

## Comment 1

> Username: vprus  
> Created_at: 2014-09-08 12:04:25 UTC  
> Url: https://github.com/boostorg/build/pull/35#issuecomment-54808248  

Aaron,  
  
thanks for the extra functions. It would be great if we could make entire re module available in Jam code, but I could not find the easy way to do it - so we have to use 'jam_signature' to make functions callable.

---

## Comment 2

> Username: frenchtoast747  
> Created_at: 2014-09-08 14:42:54 UTC  
> Url: https://github.com/boostorg/build/pull/35#issuecomment-54829743  

Yeah, that's what I was thinking. I figured there wasn't a good way to do it. Thanks for merging the changes.

---
