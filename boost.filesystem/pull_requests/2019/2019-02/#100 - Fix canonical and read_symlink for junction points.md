# #100 Fix canonical and read_symlink for junction points [Merged]

> Username: Flamefire  
> Created at: 2019-02-03 11:58:20 UTC  
> Updated at: 2020-05-04 19:57:58 UTC  
> Merged at: 2020-05-02 17:06:01 UTC  
> Closed at: 2020-05-02 17:06:01 UTC  
> Url: https://github.com/boostorg/filesystem/pull/100  

Resolving junction points in read_symlink and canonical is wrong making it unusable: It silently returns the wrong results.  
  
Issues identified:  
- read_symlink does not handle junction points and reads the wrong struct  
- canonical tries to resolve the root_name as a symlink. On windows this could be `C:` and refers to the current working directory on C. Hence depending if the current dir is a symlink canonical resolves the root_name to the current dir which is wrong  
  
Supersedes #45  
  
- [x] Includes #142

---

## Comment 1

> Username: Flamefire  
> Created_at: 2019-02-04 09:51:23 UTC  
> Url: https://github.com/boostorg/filesystem/pull/100#issuecomment-460187297  

Test was verified to trigger the bug: https://ci.appveyor.com/project/pdimov/filesystem/builds/22102228/job/0k3ueppx1l562jm5

---

## Comment 2

> Username: Flamefire  
> Created_at: 2019-02-05 07:43:31 UTC  
> Url: https://github.com/boostorg/filesystem/pull/100#issuecomment-460541829  

There is a test failure remaining on MinGW resulting from a to low or missing define for `_WIN32_WINNT`. What shall be done about this? IMO it should be defined to the value for WinXP at the least if it is not defined and error if it is defined to low. Even WinXP is out-of-support so it can be dropped especially for new boost versions.

---

## Comment 3

> Username: Lastique  
> Created_at: 2019-02-05 10:24:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/100#issuecomment-460585480  

Boost doesn't drop Windows versions, it's Windows SDKs that may drop some Windows versions. Legacy MinGW is pretty conservative, it appears to have stopped evolving before it had any reasonable support for NT6 APIs, so XP is a good choice for that target. With regard to Boost.Filesystem, I'm not sure there is any NT6 API used in the library, so it may make sense to target XP on any Windows SDK.

---

## Comment 4

> Username: stima  
> Created_at: 2019-02-06 17:27:14 UTC  
> Url: https://github.com/boostorg/filesystem/pull/100#issuecomment-461111291  

@Flamefire I added tests for my pull request https://github.com/boostorg/filesystem/pull/102.

---

## Comment 5

> Username: mloskot  
> Created_at: 2019-12-19 12:38:10 UTC  
> Url: https://github.com/boostorg/filesystem/pull/100#issuecomment-567473090  

Is this PR related to issues and patches submitted here?   
https://svn.boost.org/trac10/ticket/10900

---

## Comment 6

> Username: Flamefire  
> Created_at: 2019-12-19 13:10:20 UTC  
> Url: https://github.com/boostorg/filesystem/pull/100#issuecomment-567483108  

@mloskot It seems it adresses the same issue. Can't really compare the patches though. If this change is wanted, I can rebase

---

## Comment 7

> Username: GitEvgen  
> Created_at: 2020-04-23 19:46:09 UTC  
> Url: https://github.com/boostorg/filesystem/pull/100#issuecomment-618624039  

How do I find out if this has been released and in which version?  
Somewhat related to this issue: https://stackoverflow.com/questions/44393241/boostfilesystemrelative-cannot-access-the-file-because-it-is-being-used-by#

---

## Comment 8

> Username: Flamefire  
> Created_at: 2020-04-24 07:33:51 UTC  
> Url: https://github.com/boostorg/filesystem/pull/100#issuecomment-618854299  

@mloskot @pdimov Requesting review of this PR. Rebased to current develop after the latest changes (indentation and such) caused major conflicts.  
  
Not sure why it has been pending for so long given that on Windows a wrong struct is read which could be a security vulnerability. But also forgot about this before the 1.73 release

---

## Review 9 [Approved]

> Username: mloskot  
> Created_at: 2020-04-24 10:21:44 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/filesystem/pull/100#pullrequestreview-399822994  

@Flamefire I did skim the changes, but I'm not an expert of filesystem API. If your test passes, then I take it as all is good.  
  
One nitpick thought, perhaps the test should be disabled on non-Windows completely with something like this `<target-os>unix,<build>no`

---

## Comment 10

> Username: Flamefire  
> Created_at: 2020-04-24 11:39:58 UTC  
> Url: https://github.com/boostorg/filesystem/pull/100#issuecomment-618960563  

@mloskot Thanks, that's a good idea. Wasn't that familiar with B2 back then. And syntax of it is hard too, needed a colon, not comma ;-)

---

## Comment 11

> Username: Flamefire  
> Created_at: 2020-04-24 11:43:12 UTC  
> Url: https://github.com/boostorg/filesystem/pull/100#issuecomment-618961792  

Also fixed a failed conflict resolution. Should be good now. Let's see what CI says. I'm no expert of this either but I'm confident in the changes. Especially as it now doesn't blindly reinterpret_cast a pointer but checks the "enum" first and fails for unknown values.

---

## Comment 12

> Username: mloskot  
> Created_at: 2020-04-24 13:02:54 UTC  
> Url: https://github.com/boostorg/filesystem/pull/100#issuecomment-618995052  

> needed a colon, not comma ;-)  
  
Right, I forgot the `,` is a conjunction e.g. `<target-os>windows,<toolset>gcc:<build>no`

---

## Comment 13

> Username: Flamefire  
> Created_at: 2020-04-26 10:25:15 UTC  
> Url: https://github.com/boostorg/filesystem/pull/100#issuecomment-619524733  

@mloskot @Lastique Finally fixed the remaining issues with the test. Factored out some stuff into #142 to keep this PR focused on the bugfix however this is rebased on #142 so that should be merged first.

---

## Review 14 [Changes requested]

> Username: Lastique  
> Created_at: 2020-05-01 17:24:18 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/filesystem/pull/100#pullrequestreview-404281903  

📁 src/operations.cpp

```diff
1555 |+   {
1556 |+     const wchar_t* buffer;
1557 |+     size_t offset, len;
```

> Username: Lastique  
> Created_at: 2020-05-01 17:15:17 UTC  
> Updated_at: 2020-05-02 16:41:12 UTC  
> Url: https://github.com/boostorg/filesystem/pull/100#discussion_r418640085  

`std::size_t`

---

📁 src/operations.cpp

```diff
 796 |+       // This avoids checking "C:" which is "the current directory on drive C"
 797 |+       // and hence not what we want to check/resolve here.
 798 |+       if(!result.is_absolute())
```

> Username: Lastique  
> Created_at: 2020-05-01 17:15:45 UTC  
> Updated_at: 2020-05-02 16:41:12 UTC  
> Url: https://github.com/boostorg/filesystem/pull/100#discussion_r418640262  

(nitpick) Add space after `if`.

---

📁 src/operations.cpp

```diff
1561 |+       offset = info.rdb.MountPointReparseBuffer.PrintNameOffset;
1562 |+       len = info.rdb.MountPointReparseBuffer.PrintNameLength;
1563 |+     } else if (info.rdb.ReparseTag == IO_REPARSE_TAG_SYMLINK)
```

> Username: Lastique  
> Created_at: 2020-05-01 17:16:20 UTC  
> Updated_at: 2020-05-02 16:41:12 UTC  
> Url: https://github.com/boostorg/filesystem/pull/100#discussion_r418640519  

(nitpick) Add newline before `else`. Ditto in the following code.  
  
Or even better, convert to a `switch`/`case` block.

> Username: Flamefire  
> Created_at: 2020-05-02 16:34:33 UTC  
> Updated_at: 2020-05-02 16:41:12 UTC  
> Url: https://github.com/boostorg/filesystem/pull/100#discussion_r418978934  

Good point! 👍


📁 test/issues/99_canonical_with_junction_point.cpp

```diff
   1 |+ 
```

> Username: Lastique  
> Created_at: 2020-05-01 17:17:26 UTC  
> Updated_at: 2020-05-02 16:41:12 UTC  
> Url: https://github.com/boostorg/filesystem/pull/100#discussion_r418641042  

License header missing.  
  
Also, please add a short description of what this test is testing.

---

📁 test/issues/99_canonical_with_junction_point.cpp

```diff
  24 |+ int main()
  25 |+ {
  26 |+   if(std::system("mklink /?") != 0)
```

> Username: Lastique  
> Created_at: 2020-05-01 17:18:10 UTC  
> Updated_at: 2020-05-02 16:41:12 UTC  
> Url: https://github.com/boostorg/filesystem/pull/100#discussion_r418641346  

(nitpick) Add a space after `if`.


---

## Review 15 [Commented]

> Username: Lastique  
> Created_at: 2020-05-04 15:00:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/filesystem/pull/100#pullrequestreview-405071297  

📁 test/issues/99_canonical_with_junction_point.cpp

```diff
  33 |+ int main()
  34 |+ {
  35 |+   if (std::system("mklink /?") != 0)
```

> Username: Lastique  
> Created_at: 2020-05-04 15:00:08 UTC  
> Url: https://github.com/boostorg/filesystem/pull/100#discussion_r419502266  

This check doesn't work on Windows 8.1. `mklink` exists, it prints help, and then the test decides it doesn't exist and exits.

> Username: Flamefire  
> Created_at: 2020-05-04 15:15:56 UTC  
> Url: https://github.com/boostorg/filesystem/pull/100#discussion_r419513466  

Hm, any other idea? I have no clue why this would return non-zero  
  
Last resort would be to check the below `std::system("mklink /j junction real")` directly but if that fails due to a bug in the code it will skip everything too.

> Username: Lastique  
> Created_at: 2020-05-04 15:25:46 UTC  
> Updated_at: 2020-05-04 15:25:47 UTC  
> Url: https://github.com/boostorg/filesystem/pull/100#discussion_r419520512  

I don't know why `std::system` returns 1, but it does. It also returns 1 if the file doesn't exist. I think `std::system` result is not indicative and not trustworthy, we need to find another way. Possibly, from the Jamfile.

> Username: GitEvgen  
> Created_at: 2020-05-04 18:43:44 UTC  
> Updated_at: 2020-05-04 18:43:45 UTC  
> Url: https://github.com/boostorg/filesystem/pull/100#discussion_r419648619  

Maybe try running it and check if the link got created. Only skip if it wasn't.   
Also the test should include `mklink /D ...` The failures were different with that type of link. And there could be differences whether it's an absolute or relative link to absolute or relative path.

> Username: Lastique  
> Created_at: 2020-05-04 19:26:44 UTC  
> Url: https://github.com/boostorg/filesystem/pull/100#discussion_r419673506  

I've already implemented a different check in the Jamfile.

> Username: Lastique  
> Created_at: 2020-05-04 19:57:57 UTC  
> Updated_at: 2020-05-04 19:57:58 UTC  
> Url: https://github.com/boostorg/filesystem/pull/100#discussion_r419691198  

Another problem with this test is that it doesn't remove the temporary directory it creates. `remove_all` fails at removing "<temp_dir>/real/sub" with error 32 (directory used by another process).


---
