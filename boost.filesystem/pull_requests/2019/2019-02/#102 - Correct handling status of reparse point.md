# #102 Correct handling status of reparse point [Merged]

> Username: stima  
> Created at: 2019-02-06 17:23:19 UTC  
> Updated at: 2020-05-06 12:00:00 UTC  
> Merged at: 2020-05-06 12:00:00 UTC  
> Closed at: 2020-05-06 12:00:00 UTC  
> Url: https://github.com/boostorg/filesystem/pull/102  

Correct handling of placeholder file (https://msdn.microsoft.com/en-us/windows/compatibility/placeholder-files) in status. If 'is_reparse_point_a_symlink' returns false, return reparse_file as status. Otherwise resolve target and retake attributes of it.

---

## Comment 1

> Username: stima  
> Created_at: 2019-02-06 17:28:34 UTC  
> Url: https://github.com/boostorg/filesystem/pull/102#issuecomment-461111803  

this is reopen of https://github.com/boostorg/filesystem/pull/74, I added test and did rebase.

---

## Comment 2

> Username: Lastique  
> Created_at: 2020-05-02 17:27:48 UTC  
> Url: https://github.com/boostorg/filesystem/pull/102#issuecomment-622987450  

Please, rebase on the current develop, if this is still relevant.

---

## Comment 3

> Username: Lastique  
> Created_at: 2020-05-04 22:59:05 UTC  
> Url: https://github.com/boostorg/filesystem/pull/102#issuecomment-623751517  

Also, please add a description of the problem.

---

## Comment 4

> Username: Lastique  
> Created_at: 2020-05-05 10:27:47 UTC  
> Url: https://github.com/boostorg/filesystem/pull/102#issuecomment-623975637  

Please factor out the test to a separate file and condition it on the `check-mklink` rule. See [here](https://github.com/boostorg/filesystem/blob/fcbdae1581a8a334b7b5c1b9d6c15210a1451c44/test/Jamfile.v2#L80) for an example.

---

## Comment 5

> Username: Lastique  
> Created_at: 2020-05-05 10:51:40 UTC  
> Url: https://github.com/boostorg/filesystem/pull/102#issuecomment-623985193  

Don't forget to add a license header comment and a short description of what the test verifies in the new test.

---

## Comment 6

> Username: Lastique  
> Created_at: 2020-05-05 18:25:53 UTC  
> Url: https://github.com/boostorg/filesystem/pull/102#issuecomment-624227482  

Tests have [failed](https://ci.appveyor.com/project/Lastique/filesystem/builds/32660231/job/7fy5a199919v4066#L842).

---

## Review 7 [Commented]

> Username: Lastique  
> Created_at: 2020-05-05 18:44:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/filesystem/pull/102#pullrequestreview-406060277  

📁 test/issues/reparce_tag_file_placeholder.cpp

```diff
  66 |+   }
  67 |+ 
  68 |+   BOOST_SCOPE_EXIT(&hToken)
```

> Username: Lastique  
> Created_at: 2020-05-05 18:44:22 UTC  
> Updated_at: 2020-05-06 10:37:37 UTC  
> Url: https://github.com/boostorg/filesystem/pull/102#discussion_r420328777  

Better not use the ScopeExit library. Just write a simple scope guard class.

> Username: stima  
> Created_at: 2020-05-06 08:13:50 UTC  
> Updated_at: 2020-05-06 10:37:37 UTC  
> Url: https://github.com/boostorg/filesystem/pull/102#discussion_r420616333  

done


---

## Review 8 [Commented]

> Username: Lastique  
> Created_at: 2020-05-05 18:45:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/filesystem/pull/102#pullrequestreview-406061370  

📁 test/issues/reparce_tag_file_placeholder.cpp

```diff
  98 |+ 
  99 |+   HANDLE hHandle = CreateFileW(name, GENERIC_WRITE,
 100 |+     FILE_SHARE_READ | FILE_SHARE_WRITE, NULL, CREATE_ALWAYS,
```

> Username: Lastique  
> Created_at: 2020-05-05 18:45:47 UTC  
> Updated_at: 2020-05-06 10:37:37 UTC  
> Url: https://github.com/boostorg/filesystem/pull/102#discussion_r420329629  

`#include <cstddef>` is missing for `NULL`.

> Username: stima  
> Created_at: 2020-05-06 08:13:54 UTC  
> Updated_at: 2020-05-06 10:37:37 UTC  
> Url: https://github.com/boostorg/filesystem/pull/102#discussion_r420616366  

done


---

## Review 9 [Commented]

> Username: Lastique  
> Created_at: 2020-05-05 18:46:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/filesystem/pull/102#pullrequestreview-406061830  

📁 test/issues/reparce_tag_file_placeholder.cpp

```diff
 107 |+   }
 108 |+ 
 109 |+   auto pReparse = reinterpret_cast<PREPARSE_DATA_BUFFER>(GlobalAlloc(GPTR, MAXIMUM_REPARSE_DATA_BUFFER_SIZE));
```

> Username: Lastique  
> Created_at: 2020-05-05 18:46:26 UTC  
> Updated_at: 2020-05-06 10:37:37 UTC  
> Url: https://github.com/boostorg/filesystem/pull/102#discussion_r420330009  

`auto` is C++11. Spell the type completely.

> Username: stima  
> Created_at: 2020-05-06 08:13:59 UTC  
> Updated_at: 2020-05-06 10:37:37 UTC  
> Url: https://github.com/boostorg/filesystem/pull/102#discussion_r420616425  

done


---

## Comment 10

> Username: Lastique  
> Created_at: 2020-05-05 21:32:05 UTC  
> Url: https://github.com/boostorg/filesystem/pull/102#issuecomment-624317802  

Still [failing](https://ci.appveyor.com/project/Lastique/filesystem/builds/32668507).

---

## Comment 11

> Username: Lastique  
> Created_at: 2020-05-06 09:21:15 UTC  
> Url: https://github.com/boostorg/filesystem/pull/102#issuecomment-624536852  

Still [failing](https://ci.appveyor.com/project/Lastique/filesystem/builds/32680523/job/dxih671ij8sr558j).

---

## Comment 12

> Username: stima  
> Created_at: 2020-05-06 11:33:20 UTC  
> Updated_at: 2020-05-06 11:33:33 UTC  
> Url: https://github.com/boostorg/filesystem/pull/102#issuecomment-624596455  

Thanks for helping to finalize it. There are only left:  
error: "20" is not a known value of feature <cxxstd>  
error: legal values: "98" "03" "0x" "11" "1y" "14" "1z" "17" "2a" "latest"  
  
But this code does not affect gcc/clang.

---

## Comment 13

> Username: Lastique  
> Created_at: 2020-05-06 11:59:21 UTC  
> Url: https://github.com/boostorg/filesystem/pull/102#issuecomment-624607297  

Thanks. Don't mind the Boost.Build issue, it should be fixed by https://github.com/boostorg/build/pull/592.

---
