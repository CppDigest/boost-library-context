# #150 - fstream reuse on Windows causes a null pointer access violation [Closed]

> Username: KazNX  
> Created at: 2022-03-16 01:16:48 UTC  
> Updated at: 2022-03-18 10:22:19 UTC  
> Closed at: 2022-03-18 10:22:19 UTC  
> Url: https://github.com/boostorg/nowide/issues/150  

Re-using `nowide::fstream` after a `close()` and `open()` can attempt null pointer access while writing to the second file.  
  
A full project has been attached with instructions in the readme. Below is an excert of the code which will reproduce the issue.  
  
```cpp  
void testNowideBug(bool move_assign_stream)  
{  
  const std::string test_data = /* [snip] 795 character long string literal */;  
  
  nowide::ofstream test_stream_out("out1.txt", std::ios_base::binary);  
  test_stream_out.write(test_data.data(), test_data.size());  
  test_stream_out.close();  
  // Writing to a new file using the same stream will crash.  
  // It's fine if you change the test to "hello world".  
  // It's fine if you use a new stream object  
  if (move_assign_stream)  
  {  
    test_stream_out = nowide::ofstream("out2.txt", std::ios_base::binary);  
  }  
  else  
  {  
    test_stream_out.open("out2.txt", std::ios_base::binary);  
  }  
  test_stream_out.write(test_data.data(), test_data.size());  
}  
```  
  
The final line will fail, the program will crash and `out2.txt` will be empty. The test string in the example has been removed for breivity, but I've used two strings of 795 characters (I doubt it's that specified) with the same failed results. The string `"hellow world"` works as expected.  
  
This appears to be specific to Windows and does not affect Ubuntu 18.04 or 20.04.  
  
Testing using MSC 16.11.11 (Visual Studio 2019).  
  
[nowide.zip](https://github.com/boostorg/nowide/files/8258199/nowide.zip)

---

## Comment 1

> Username: KazNX  
> Created at: 2022-03-16 04:07:49 UTC  
> Url: https://github.com/boostorg/nowide/issues/150#issuecomment-1068710129  

For refrence, the issue presents itself in `basic_filebuf::overflow()` : https://github.com/boostorg/nowide/blob/standalone/include/nowide/filebuf.hpp#L240  
  
The `buffer_` pointer is null.

---

## Comment 2

> Username: KazNX  
> Created at: 2022-03-16 04:15:48 UTC  
> Url: https://github.com/boostorg/nowide/issues/150#issuecomment-1068713968  

Also, the length is a furphie. Different lengths produce crashes in other locations. It also seems that `"hello world"` passing was an artefact from my debugging and is not true.

---

## Comment 3

> Username: Flamefire  
> Created at: 2022-03-16 11:37:37 UTC  
> Updated at: 2022-03-17 10:06:11 UTC  
> Url: https://github.com/boostorg/nowide/issues/150#issuecomment-1069032152  

Thanks for the issue report.  
This is caused by a bug in the `close` function which has been fixed a while ago with https://github.com/boostorg/nowide/commit/b96294d59fe193a58a4e0e46ee98c83e8f6dbe87  
However an unrelated bug caused the standalone branch to not be automatically updated which has been fixed by https://github.com/boostorg/nowide/commit/a944d2226dd7fce4fca4fe3e17bff71f7fdcfb2b  and https://github.com/boostorg/nowide/commit/fccb720589df4dd7ec4e22a4971a439a8cacd00d is the update of the standalone branch containing the fix.  
  
I tested the provided code against the current standalone branch via the following commands on Windows inside the extracted folder (didn't want to install the Python and colcon stack):  
```  
git clone --branch standalone https://github.com/boostorg/nowide.git  
cmake -S nowide -B nw-build -DCMAKE_CXX_STANDARD=17 -DCMAKE_INSTALL_PREFIX=nw-install  
cmake --build nw-build --config Debug --target install  
cmake -S nowidebug -B nw-build2 -DCMAKE_CXX_STANDARD=17 -DCMAKE_INSTALL_PREFIX=nw-install  
cmake --build nw-build2 --config Debug  
nw-build2/Debug/nowidebug.exe  
```  
  
Which yields:  
```  
Testing with move assignment  
...ok  
Testing with stream re-use (close/open)  
...ok  
```  
  
Anyway I'll include a test case based on your code to avoid this reappearing. If you want to make sure you are using a (standalone) version where this is fixed, require a version of 11.1.1 in the CMake `find_package` call which is the release that includes the fix.

---

## Comment 4

> Username: KazNX  
> Created at: 2022-03-16 22:06:49 UTC  
> Updated at: 2022-03-16 22:08:18 UTC  
> Url: https://github.com/boostorg/nowide/issues/150#issuecomment-1069681254  

Thanks for the quick response.  
  
Could you please retest using a Release build? All the testing I did yesterday was using the tip of the `standalone` branch - my `CMakeLists.txt` file for `nowide` shows it's version `11.1.4` - and it was failing on a release build. I've tried a Debug build as you did above and yes, that does indeed pass.  
  
So it looks like we have a release mode only bug.

---

## Comment 5

> Username: Flamefire  
> Created at: 2022-03-17 10:45:33 UTC  
> Url: https://github.com/boostorg/nowide/issues/150#issuecomment-1070778674  

I retested the above workflow with a release build, i.e. replacing `--config Debug` by `--config Release` and it passed.  
  
I also manually verified the code concluding that it is logically impossible for `buffer_` to be null at that point in the current develop & standalone branch. The relevant part of the fix are those 2 lines: https://github.com/boostorg/nowide/commit/b96294d59fe193a58a4e0e46ee98c83e8f6dbe87#diff-5abbcdd10563140cb03ad1a9d61a29bc562974225d8bc6888c931c59719d4676R177-R178  
  
So I suspect that you somehow ended up using an older version in your test. Check the actually used include paths for the build using nowide that it uses the expected path and that it includes the fix, i.e. those 2 lines (link to current tip of the standalone branch: https://github.com/boostorg/nowide/blob/960c2016a259275fa9dda68f34624bde87d887bd/include/nowide/filebuf.hpp#L177-L178 )  
  
So could you please check this? As mentioned I added a test case to the tests based on your code and it passes too, see #151

---

## Comment 6

> Username: KazNX  
> Created at: 2022-03-17 22:00:47 UTC  
> Url: https://github.com/boostorg/nowide/issues/150#issuecomment-1071590590  

Thanks for retesting.  
  
I've checked my includes and run a clean build and my release build is passing. I can't definitely account for the discrepancy, but I suspect I may have had some residual paths in my powershell environment when I ran my first tests.  
  
Thanks for the help.
