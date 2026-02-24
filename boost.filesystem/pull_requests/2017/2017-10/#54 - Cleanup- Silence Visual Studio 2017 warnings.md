# #54 Cleanup: Silence Visual Studio 2017 warnings. [Closed]

> Username: KevinHopps  
> Created at: 2017-10-05 19:57:30 UTC  
> Updated at: 2018-11-24 17:41:21 UTC  
> Closed at: 2018-11-24 17:41:21 UTC  
> Url: https://github.com/boostorg/filesystem/pull/54  



---

## Comment 1

> Username: MarcelRaad  
> Created_at: 2017-10-05 21:36:38 UTC  
> Url: https://github.com/boostorg/filesystem/pull/54#issuecomment-334599061  

I've also been bugged by those warnings. There's already #44 from April.

---

## Comment 2

> Username: Lastique  
> Created_at: 2018-03-02 16:24:29 UTC  
> Url: https://github.com/boostorg/filesystem/pull/54#issuecomment-369971897  

Looks like you introduced tabs with your edits. Please, convert to spaces.

---

## Comment 3

> Username: jeking3  
> Created_at: 2018-07-17 01:51:39 UTC  
> Url: https://github.com/boostorg/filesystem/pull/54#issuecomment-405433782  

@KevinHopps would you be able to convert the tabs to spaces in your pull request to align with the current code style?  Thanks.

---

## Review 4 [Commented]

> Username: jeking3  
> Created_at: 2018-07-17 20:48:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/filesystem/pull/54#pullrequestreview-138010466  

I'd prefer to see numeric_cast used to ensure overflows are handled properly, even if they are edge cases - or am I being too defensive in style?

📁 src/unique_path.cpp

```diff
 114 |+     // Use static cast to silence Visual Studio 2017
 115 |+     // warning C4267: 'argument': conversion from 'size_t' to 'DWORD', possible loss of data
 116 |+     BOOL gen_ok = ::CryptGenRandom(handle, static_cast<DWORD>(len), static_cast<unsigned char*>(buf));
```

> Username: jeking3  
> Created_at: 2018-07-17 20:44:30 UTC  
> Updated_at: 2018-07-18 19:13:01 UTC  
> Url: https://github.com/boostorg/filesystem/pull/54#discussion_r203171778  

@Lastique here's another random implementation that is not ready for Windows UWP.

> Username: jeking3  
> Created_at: 2018-07-17 20:46:00 UTC  
> Updated_at: 2018-07-18 19:13:01 UTC  
> Url: https://github.com/boostorg/filesystem/pull/54#discussion_r203172263  

Should we be using `static_cast` or should we be using `boost::numeric_cast` to ensure proper handling of math here (and the other instances I see in the other file).

> Username: KevinHopps  
> Created_at: 2018-07-17 21:09:47 UTC  
> Updated_at: 2018-07-18 19:13:01 UTC  
> Url: https://github.com/boostorg/filesystem/pull/54#discussion_r203179699  

numeric_cast seems appropriate. I will make that change.


---

## Comment 5

> Username: alexeikh  
> Created_at: 2018-07-18 09:31:27 UTC  
> Url: https://github.com/boostorg/filesystem/pull/54#issuecomment-405870739  

Now in 2 of 3 places the comment says "Use static cast", but the code uses `numeric_cast`.

---

## Comment 6

> Username: jeking3  
> Created_at: 2018-07-18 13:38:24 UTC  
> Url: https://github.com/boostorg/filesystem/pull/54#issuecomment-405933855  

In general, the comments in the code here are unnecessary.

---

## Review 7 [Commented]

> Username: alexeikh  
> Created_at: 2018-07-18 16:07:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/filesystem/pull/54#pullrequestreview-138322263  

📁 src/windows_file_codecvt.cpp

```diff
  62 |     int count;
  63 |+     // Use static cast to silence Visual Studio 2017:
  64 |+     // warning C4244: 'argument': conversion from '__int64' to 'int', possible loss of data
```

> Username: alexeikh  
> Created_at: 2018-07-18 15:53:26 UTC  
> Updated_at: 2018-07-18 19:13:01 UTC  
> Url: https://github.com/boostorg/filesystem/pull/54#discussion_r203433710  

Sorry for nitpicking, but this unnecessary (and inaccurate) 2-line comment still hasn't been removed.

---

📁 src/windows_file_codecvt.cpp

```diff
  65 |     if ((count = ::WideCharToMultiByte(codepage, WC_NO_BEST_FIT_CHARS, from,
  63 |-       from_end - from, to, to_end - to, 0, 0)) == 0)
  66 |+        boost::numeric_cast<int>(from_end - from), to, boost::numeric_cast<int>(to_end - to), 0, 0)) == 0)
```

> Username: alexeikh  
> Created_at: 2018-07-18 16:00:29 UTC  
> Updated_at: 2018-07-18 19:13:01 UTC  
> Url: https://github.com/boostorg/filesystem/pull/54#discussion_r203436398  

Sorry for nitpicking again. This line has 1 extra indentation space, compared to the original line.  
  
Also, this line has become very long. I'd rewrite the whole fragment, for example, as:  
  
```c++  
int count = ::WideCharToMultiByte(  
  codepage, WC_NO_BEST_FIT_CHARS,  
  from, boost::numeric_cast<int>(from_end - from),  
  to, boost::numeric_cast<int>(to_end - to),  
  0, 0);  
if (count == 0)  
  return error;  // conversion failed  
```

---

📁 src/windows_file_codecvt.cpp

```diff
  45 |     {
  46 |       return error;  // conversion failed
  47 |     }
```

> Username: alexeikh  
> Created_at: 2018-07-18 16:06:05 UTC  
> Updated_at: 2018-07-18 19:13:01 UTC  
> Url: https://github.com/boostorg/filesystem/pull/54#discussion_r203438396  

The expression in `if` is now very long. Suggesting to rewrite as:  
  
```c++  
int count = ::MultiByteToWideChar(  
  codepage, MB_PRECOMPOSED,  
  from, boost::numeric_cast<int>(from_end - from),  
  to, boost::numeric_cast<int>(to_end - to));  
if (count == 0)  
  return error;  // conversion failed  
```


---

## Comment 8

> Username: Lastique  
> Created_at: 2018-11-24 17:41:21 UTC  
> Url: https://github.com/boostorg/filesystem/pull/54#issuecomment-441383721  

Seems to be already fixed by d88945564a4ed037474f9a5952b87f4f9244d4ba.

---
