# #264 - Calling `canonical("\\?\UNC\Server\Share")` fails with `No such file or directory` [Closed]

> Username: emmenlau  
> Created at: 2022-11-28 20:55:31 UTC  
> Updated at: 2022-12-15 20:05:59 UTC  
> Closed at: 2022-12-15 20:05:59 UTC  
> Url: https://github.com/boostorg/filesystem/issues/264  

I'm trying to use `canonical()` with long UNC server paths. I think the correct syntax should be something like `\\?\UNC\Server\Share`, for example `\\?\UNC\192.168.33.180\DataStore\Folder\File.txt`, correct? This fails for me. The same call works when I leave out the long path prefix `\\?\UNC\` and specify as `\\192.168.33.180\DataStore\Folder\File.txt`.  
  
Am I doing something wrong?

---

## Comment 1

> Username: Lastique  
> Created at: 2022-11-29 15:29:37 UTC  
> Url: https://github.com/boostorg/filesystem/issues/264#issuecomment-1330828852  

> This fails for me.  
  
Fails in what way?  
  
Also, what Boost version are you using and which Boost.Filesystem version (v3 or v4)? Note that v3 (which is currently the default) has issues with special Windows path prefixes which cannot be resolved. If you want to use prefixes you should use v4.

---

## Comment 2

> Username: emmenlau  
> Created at: 2022-11-29 16:36:05 UTC  
> Url: https://github.com/boostorg/filesystem/issues/264#issuecomment-1330928310  

> > This fails for me.  
>   
> Fails in what way?  
  
Sorry, I should have been more specific. It fails with an exception saying `No such file or directory`. Would you need more details from the exception?  
  
> Also, what Boost version are you using and which Boost.Filesystem version (v3 or v4)? Note that v3 (which is currently the default) has issues with special Windows path prefixes which cannot be resolved. If you want to use prefixes you should use v4.  
  
That is very interesting, and I was not aware of v4 until just now! I will test v4 first before we need to even consider this issue. However if you don't mind, I'd kindly like to leave the issue open until I can report back how v4 behaves in the same scenario. Then we can see how to proceed? It may take a few days, maybe even a few week though.

---

## Comment 3

> Username: Lastique  
> Created at: 2022-11-29 16:51:02 UTC  
> Url: https://github.com/boostorg/filesystem/issues/264#issuecomment-1330954068  

Please test on the latest Boost version from develop, too. There were changes that may be relevant and are about to be shipped in Boost 1.81.

---

## Comment 4

> Username: Lastique  
> Created at: 2022-12-02 20:59:46 UTC  
> Url: https://github.com/boostorg/filesystem/issues/264#issuecomment-1335840357  

This does not reproduce for me, `canonical("\\\\?\\UNC\\127.0.0.1\\Share\\file.txt")` succeeds and returns the same path. Please note the escaped back slashes. Also note that the file must actually be accessible at the given path.

---

## Comment 5

> Username: emmenlau  
> Created at: 2022-12-05 11:13:48 UTC  
> Updated at: 2022-12-05 11:16:09 UTC  
> Url: https://github.com/boostorg/filesystem/issues/264#issuecomment-1337158497  

Hmm, this does not work for me, at least not with boost::filesystem v3. I've tested from a cygwin bash shell:  
```  
#>  ls -la '\\?\UNC\192.168.33.111\Test\lenna_1.tif'  
-rw-r--r-- 1 username Test User 16554 Nov  3  2020 '\\?\UNC\192.168.33.111\Test\lenna_1.tif'  
```  
Then I execute the following C++ code in a Google test:  
```  
::boost::filesystem::canonical("\\\\?\\UNC\\192.168.33.111\\Test\\lenna_1.tif");  
```  
but it throws exception:  
```  
unknown file: error: C++ exception with description "boost::filesystem::canonical: No such file or directory [generic:2]: "\\?\UNC\192.168.33.111\Test\lenna_1.tif"" thrown in the test body.  
```  
  
The same test works in C++ when I remove the `UNC`-prefix, so it works with `\\\\192.168.33.111\\Test\\lenna_1.tif`.  
  
I will test with boost::filesystem v4 next...

---

## Comment 6

> Username: Lastique  
> Created at: 2022-12-05 11:33:38 UTC  
> Updated at: 2022-12-05 11:34:55 UTC  
> Url: https://github.com/boostorg/filesystem/issues/264#issuecomment-1337182105  

> `ls -la '\\?\UNC\192.168.33.111\Test\lenna_1.tif'`  
  
I was testing on native Windows 10. To my knowledge, UNIX systems don't support UNC paths. I'm not sure about Cygwin and MSYS, but it is possible they don't support it either.  
  
Also, I was testing current develop.

---

## Comment 7

> Username: emmenlau  
> Created at: 2022-12-05 12:04:52 UTC  
> Url: https://github.com/boostorg/filesystem/issues/264#issuecomment-1337222343  

> > `ls -la '\\?\UNC\192.168.33.111\Test\lenna_1.tif'`  
>   
> I was testing on native Windows 10. To my knowledge, UNIX systems don't support UNC paths. I'm not sure about Cygwin and MSYS, but it is possible they don't support it either.  
  
I'm so sorry @Lastique , I think my comment about Cygwin added more confusion! I'm not actually using Cygwin for anything relevant, other than confirming that the file exists and is accessible. As an alternative test, I've now used a dos box and tried:  
```  
C:\Users\Test User>copy /V \\?\UNC\192.168.33.111\Test\lenna_1.tif C:\data\  
        1 file(s) copied.  
```  
So the file exists and is accessible.  
  
However, even when using boost::filesystem version 4, the C++ program fails. So with boost 1.80.0 I'm not able to use canonical on long path `UNC`-paths.  
  
Is it fine to leave this issue open until boost 1.81.0, and then I can try again?

---

## Comment 8

> Username: Lastique  
> Created at: 2022-12-05 12:34:45 UTC  
> Updated at: 2022-12-05 12:37:21 UTC  
> Url: https://github.com/boostorg/filesystem/issues/264#issuecomment-1337268337  

I have now tested on Cygwin64 and it also works. Turns out we are using WinAPI on Cygwin, so it should work the same as on native Windows, which it does.  
  
For the record, I'm testing on a Windows 10 VM (a KVM guest) accessing a network share on the host. The test is done by modifying "libs/filesystem/test/operations_test.cpp" by adding `BOOST_TEST_EQ(fs::canonical("\\\\?\\UNC\\192.168.1.2\\Share\\file.txt"), "\\\\?\\UNC\\192.168.1.2\\Share\\file.txt");` to `canonical_basic_tests` and then running Boost.Filesystem tests. This runs the tests in v4 mode. Compilers used: MSVC-14.2 on native Windows, clang-8 and gcc-10 on Cygwin64.  
  
> Is it fine to leave this issue open until boost 1.81.0, and then I can try again?  
  
I don't want to leave open issues where no further work is planned, because I'm sure I won't be revisiting this unless there is some new information. I recommend you try with the current Boost develop.

---

## Comment 9

> Username: emmenlau  
> Created at: 2022-12-15 13:58:47 UTC  
> Url: https://github.com/boostorg/filesystem/issues/264#issuecomment-1353134313  

Dear @Lastique , I've finally built my libraries against boost v1.81.0 (released yesterday) with boost::filesystem version set to v4. However my simple test still fails for me. Here are the details of what fails:  
```  
const std::string vPath = "\\\\?\\UNC\\192.168.33.180\\DataStore\\FileFormats\\StandardSet\\lenna_1.tif";  
// This will throw boost::filesystem::canonical: No such file or directory [generic:2]: "\\?\UNC\192.168.33.180\DataStore\FileFormats\StandardSet\lenna_1.tif":  
::boost::filesystem::canonical(vPath).string();  
```  
  
When I trim half the backslashes, and try the resulting string in a `cmd` box, it works:  
```  
#> copy "\\?\UNC\192.168.33.180\DataStore\FileFormats\StandardSet\lenna_1.tif" .  
        1 file(s) copied.  
```

---

## Comment 10

> Username: Lastique  
> Created at: 2022-12-15 15:35:47 UTC  
> Url: https://github.com/boostorg/filesystem/issues/264#issuecomment-1353280258  

As I said earlier, this doesn't reproduce on my setup, so I'm afraid you will have to debug this on your end. Can you step through the code to see what syscall fails?  
  
Also, what Windows are you running? Both the local machine and the share. Is it possible there are permission issues? Maybe anti-virus software preventing access to the file?

---

## Comment 11

> Username: emmenlau  
> Created at: 2022-12-15 20:05:59 UTC  
> Url: https://github.com/boostorg/filesystem/issues/264#issuecomment-1353644850  

Ok, I'm very sorry, this time it was my mistake - the update of boost was not performed cleanly! I found an issue in the boost built that I overlooked before, and after fixing it, the updated boost was used and everything works now! Thanks a lot, issue is fixed with boost 1.81.0!
