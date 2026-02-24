# #233 Improve odr test [Closed]

> Username: HDembinski  
> Created at: 2019-10-09 22:07:33 UTC  
> Updated at: 2019-10-20 08:51:29 UTC  
> Closed at: 2019-10-12 14:44:35 UTC  
> Url: https://github.com/boostorg/histogram/pull/233  



---

## Comment 1

> Username: henryiii  
> Created_at: 2019-10-12 03:16:30 UTC  
> Url: https://github.com/boostorg/histogram/pull/233#issuecomment-541278677  

You may need to normalize paths on Windows in the Python script, I see slashes in both directions (`/` and `\`)

---

## Review 2 [Commented]

> Username: henryiii  
> Created_at: 2019-10-12 03:22:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/233#pullrequestreview-300982631  

📁 test/odr_test.py

```diff
   6 |+ 
   7 |+ all_headers = set()
   8 |+ include_path = this_path + "/../include/"
```

> Username: henryiii  
> Created_at: 2019-10-12 03:18:22 UTC  
> Updated_at: 2019-10-12 12:57:58 UTC  
> Url: https://github.com/boostorg/histogram/pull/233#discussion_r334217993  

```diff  
-include_path = this_path + "/../include/"  
+include_path = os.path.join(this_path, "..", "include")  
```

> Username: henryiii  
> Created_at: 2019-10-12 03:44:55 UTC  
> Updated_at: 2019-10-12 12:57:58 UTC  
> Url: https://github.com/boostorg/histogram/pull/233#discussion_r334218818  

(These are suggestions to help debug/fix - I have not tested locally and do not have a Windows machine)

> Username: HDembinski  
> Created_at: 2019-10-12 09:40:43 UTC  
> Updated_at: 2019-10-12 12:57:58 UTC  
> Url: https://github.com/boostorg/histogram/pull/233#discussion_r334229785  

Good point, will do. I just applied that fix to iminuit's setup.py as well the other day.

---

📁 test/odr_test.py

```diff
   9 |+ for root, dirs, files in os.walk(include_path):
  10 |+     for fn in files:
  11 |+         fn = root + "/" + fn
```

> Username: henryiii  
> Created_at: 2019-10-12 03:20:06 UTC  
> Updated_at: 2019-10-12 12:57:58 UTC  
> Url: https://github.com/boostorg/histogram/pull/233#discussion_r334218043  

```diff  
-        fn = root + "/" + fn  
+        fn = os.path.join(root,  fn)  
```

---

📁 test/odr_test.py

```diff
  10 |+     for fn in files:
  11 |+         fn = root + "/" + fn
  12 |+         assert fn.startswith(include_path)
```

> Username: henryiii  
> Created_at: 2019-10-12 03:20:41 UTC  
> Updated_at: 2019-10-12 12:57:58 UTC  
> Url: https://github.com/boostorg/histogram/pull/233#discussion_r334218071  

I would probably make sure these are resolved with `os.path.abspath` first. Might be fine as is though.

> Username: HDembinski  
> Created_at: 2019-10-12 09:41:31 UTC  
> Updated_at: 2019-10-12 12:57:58 UTC  
> Url: https://github.com/boostorg/histogram/pull/233#discussion_r334229798  

What difference would abspath make?

---

📁 test/odr_test.py

```diff
  25 |+ included_headers = set()
  26 |+ unread_headers = set()
  27 |+ for hdr in get_headers(this_path + "/odr_test.cpp"):
```

> Username: henryiii  
> Created_at: 2019-10-12 03:22:08 UTC  
> Updated_at: 2019-10-12 12:57:58 UTC  
> Url: https://github.com/boostorg/histogram/pull/233#discussion_r334218122  

```diff  
-for hdr in get_headers(this_path + "/odr_test.cpp"):  
+for hdr in get_headers(os.path.join(this_path, "/odr_test.cpp")):  
```  
  
This is why I love pathlib, plumbum.path, pretty much anything OO for paths!

> Username: HDembinski  
> Created_at: 2019-10-12 09:42:14 UTC  
> Updated_at: 2019-10-12 12:57:58 UTC  
> Url: https://github.com/boostorg/histogram/pull/233#discussion_r334229822  

I don't mind using os.path.join for the time being.

---

📁 test/odr_test.py

```diff
  32 |+     for hdr in tuple(unread_headers):
  33 |+         unread_headers.remove(hdr)
  34 |+         for hdr2 in get_headers(include_path + hdr):
```

> Username: henryiii  
> Created_at: 2019-10-12 03:22:42 UTC  
> Updated_at: 2019-10-12 12:57:58 UTC  
> Url: https://github.com/boostorg/histogram/pull/233#discussion_r334218146  

```diff  
-        for hdr2 in get_headers(include_path + hdr):  
+        for hdr2 in get_headers(os.path.join(include_path, hdr)):  
```


---

## Comment 3

> Username: henryiii  
> Created_at: 2019-10-12 03:25:11 UTC  
> Url: https://github.com/boostorg/histogram/pull/233#issuecomment-541279271  

Instead of path.join everywhere, maybe do a sub of back slash for forward slash, probably on the walk. path.join will actualy put in the backslash, so the strings won't match what you pull out of the source unless you do abspath on them.

---

## Comment 4

> Username: henryiii  
> Created_at: 2019-10-12 04:51:27 UTC  
> Updated_at: 2019-10-12 05:03:36 UTC  
> Url: https://github.com/boostorg/histogram/pull/233#issuecomment-541284838  

I made a PR to your branch with a modified script that *should* work on Windows. https://github.com/HDembinski/histogram/pull/57

---

## Comment 5

> Username: henryiii  
> Created_at: 2019-10-12 12:20:12 UTC  
> Url: https://github.com/boostorg/histogram/pull/233#issuecomment-541319838  

Have you looked at the version I provided in the PR? It is much simpler, and should have worked out of the box.

---

## Comment 6

> Username: HDembinski  
> Created_at: 2019-10-12 14:44:35 UTC  
> Url: https://github.com/boostorg/histogram/pull/233#issuecomment-541331158  

Already merged in 05c1bbfac12531e9f629ae40d1e53f47ffdbf1ee

---

## Comment 7

> Username: henryiii  
> Created_at: 2019-10-12 14:51:14 UTC  
> Url: https://github.com/boostorg/histogram/pull/233#issuecomment-541331735  

:+1:

---
