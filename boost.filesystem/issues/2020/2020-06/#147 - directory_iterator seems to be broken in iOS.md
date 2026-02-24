# #147 - directory_iterator seems to be broken in iOS [Closed]

> Username: mackworth  
> Created at: 2020-06-02 23:48:39 UTC  
> Updated at: 2020-09-02 12:08:31 UTC  
> Closed at: 2020-06-13 22:36:49 UTC  
> Url: https://github.com/boostorg/filesystem/issues/147  

tldr: When I use the boost filesystem::directory_iterator in my iOS program, it just plain doesn't work even in a tiny test program.   
  
I started by building the current boost library using the [Apple platform install script](https://github.com/faithfracture/Apple-Boost-BuildScript)  with command line:  
  
> ./boost.sh -ios -macos --boost-version 1.73.0 --ios-sdk 13.4 --ios-archs "armv7 arm64" --min-ios-version 10.2  --hidden-visibility  --universal --boost-libs "date_time filesystem program_options regex system"  
  
directory_iterator wasn't working and sometimes crashed in the project I'm working on, so I  created a new Xcode project (attached below) to narrow it down.  All I did is:  
-   Build a new Single-view iOS project in Xcode  
-   Rename `AppDelegate.m` to `AppDelegate.mm` to use c++  
-   Add code to create a file, then list the directory (see bottom of this post) to `didFinishLaunchingWithOptions`  
-   Add boost headers to Header Search Path  
-   Add library directory to Library Search paths  
-   Add libboost_filesystem.a to "Link Binary with Libraries" phase  
-   Then run the program.  
  
I'm using current Xcode Version 11.4.1 (11E503a) on MacOS: 10.15.4 (19E287) and iOS: 13.4.1 (17E262).  
  
Results: the `itr->path` exists; once for each file, but is always empty, giving error msg: `"" does not exist`. As discussed below, sometimes I also get a malloc crash in `directory.cpp > dir_itr_close` when the for-loop finishes.  
  
Note that the preceding Cocoa-based access to the same directory works fine. So the file(s) are there, and the app has rights to get to them.   
  
I did the same steps on a macOS version (both a command line version in main.mm and a sandboxed app version in applicationDidFinishLaunching; also attached for your interest). No changes to code at all, and both worked fine, respectively giving me a listing of my real Documents directory, or just showing the dummy file in the sandboxed Documents.  
  
The initial trigger for my investigation was that I got warning messages when compiling in my real project (below are the ones from the test project; I compressed the long names to $DerivedData). As discussed below, these only appear when I compile on the simulator with `Inline Methods Hidden: NO`  
  
> Direct access in function 'boost::filesystem::detail::directory_iterator_construct(boost::filesystem::directory_iterator&, boost::filesystem::path const&, unsigned int, boost::system::error_code*)' from file '$DerivedData/boost-ios/build/iOS/libboost_filesystem.a(directory.o)' to global weak symbol 'boost::system::detail::is_generic_value(int)::gen' from file '$DerivedData/Debug-iphonesimulator/testBoost.build/Objects-normal/x86_64/AppDelegate.o' means the weak symbol cannot be overridden at runtime. This was likely caused by different translation units being compiled with different visibility settings.>   
>   
> Direct access in function 'boost::system::detail::system_error_category::default_error_condition(int) const' from file '$DerivedData/boost-ios/build/iOS//libboost_filesystem.a(directory.o)' to global weak symbol 'boost::system::detail::is_generic_value(int)::gen' from file '$DerivedData//Debug-iphonesimulator/testBoost.build/Objects-normal/x86_64/AppDelegate.o' means the weak symbol cannot be overridden at runtime. This was likely caused by different translation units being compiled with different visibility settings.  
  
So, with that clue... I tried changing Xcode's  Hidden options. In the test project, if I set `Inline Methods Hidden` (matching the compilation), then I get no warnings, empty file names reported and then a malloc crash. If I turn it off, I get the warnings, empty file names, but no crash. The `Symbols Hidden by Default` option seems to have no effect in either case.  I note that the warnings only appear when I'm compiling on the simulator, but the other results are the same.   
  
I then rebuilt the boost library removing the `--hidden-visibility` option. Absolutely no effect: `Inline Methods hidden` still gives no warning, no names and malloc crash; `Inline Methods hidden` off still gives warning, no names, and no malloc crash.  
  
We tried switching this test program to c++17 and it still failed, but then further switching to `std::filesystem`, it works fine, but that isn't possible for the main project.  
  
I'm sure there's just some flag I need to set when building boost::filesystem for iOS, but I haven't been able to find it. I've asked the developer of the build script, and he couldn't see any obvious answers either.  
  
I did some breakpoints/single-stepping and got deep into filesystem code, but eventually hit a brick wall. I'll post the details on this process in a comment, as it's a little hairy.    
  
Thanks for taking a look!  
  
```  
 using namespace std;  
namespace fs = boost::filesystem;  
  
@implementation AppDelegate  
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {  
 //Create a temp file to list  
  NSString * objCPath = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) objectAtIndex:0];  
  fs::path doc_path(objCPath.fileSystemRepresentation);  
  fs::ofstream( doc_path / "/dummyFile.txt" );  
 // fs::ofstream( doc_path / "/dummyFile2.txt" );  
    
  NSLog(@"Directory Contents: %@", [[NSFileManager defaultManager] contentsOfDirectoryAtPath:objCPath error:nil]);  
  
  fs::directory_iterator end_itr;  // default construction yields past-the-end  
  for (fs::directory_iterator itr(doc_path); itr != end_itr; ++itr) {  
   fs::path path = itr->path();  
   //  
    if (exists(path))  
      if (is_regular_file(path))  
        std::cout << path << "; file size is " << file_size(path) << '\n';  
      else if (is_directory(path))  
        std::cout << path << " is a directory\n";  
      else  
        std::cout << path << "exists, but is neither a file nor a directory\n";  
    else  
      cout << path << " does not exist\n";  
  }  
  cout << "All done";  
  return YES;  
  
```  
  
[TestBoost.zip](https://github.com/faithfracture/Apple-Boost-BuildScript/files/4604972/TestBoost.zip)

---

## Comment 1

> Username: mackworth  
> Created at: 2020-06-02 23:52:58 UTC  
> Updated at: 2020-06-02 23:55:38 UTC  
> Url: https://github.com/boostorg/filesystem/issues/147#issuecomment-637870892  

My feeble attempt at finding the problem:  
  
First, I had to switch optimization off to single-step through the boost code (after recompiling, I admit I was a little surprised that the problem still exists).   
  
Setting a breakpoint when it enters the loop in my main program...  
```  
for (fs::directory_iterator itr(doc_path); itr != end_itr; ++itr) {  
==>   fs::path path = itr->path();  
```  
**p itr**  
```  
(boost::filesystem::directory_iterator) $1 = {  
  m_imp = {  
    px = 0x0000600002045300  
  }  
}  
```  
 **p itr.m_imp.px->dir_entry.m_path**  
```  
(boost::filesystem::path) $2 = (m_pathname = “[[long iOS path here]]/Documents/dummyFile.txt”)  
```  
So that looks great, BUT then a call to path (which should simply return m_path) fails, both from lldb and the program...  
**p itr->path()**  
`(const boost::filesystem::path) $3 = (m_pathname = "")  
`  
When I single-step through the `itr->path() `call, and it first takes me to   
    `iterator_facade arrow operator ->`, which calls `apply(*this->derived())`.  (derived seems to be just a cast)  
    `* `calls `iterator_core_access(aka Facade)::dereference` which  calls `f.dereference()`  
  
the source of f.dereference is just   
```  
  boost::iterator_facade<    directory_iterator,  directory_entry,   boost::single_pass_traversal_tag >::reference  
 dereference() const  {  
    BOOST_ASSERT_MSG(!is_end(), "attempt to dereference end directory iterator");  
    return m_imp->dir_entry;  
  }  
```  
BUT if you print `f.dereference()` you get the bad path, and if you print what `f.dereference()` is supposed to return: `f.m_imp.px->dir_entry`, you get the right path.   
  
**p f**  
```  
(const boost::filesystem::directory_iterator) $3 = {  
  m_imp = {  
    px = 0x0000600001739400  
  }  
}  
```  
**p &(f.dereference())**   _bad one_  
**p f.dereference()**  
```  
(boost::filesystem::directory_entry *) $5 = 0x0000600001739408  
(boost::filesystem::directory_entry) $2 = {  
  m_path = (m_pathname = "")  
  m_status = (m_value = status_error, m_perms = no_perms)  
  m_symlink_status = (m_value = fifo_file | socket_file | 0xfffffff0, m_perms = -1)  
}  
```  
**p &(f.m_imp.px->dir_entry)** _correct one_  
**p f.m_imp.px->dir_entry**  
```  
(boost::filesystem::directory_entry *) $4 = 0x0000600001739448  
(boost::filesystem::directory_entry) $1 = {  
  m_path = (m_pathname = "[[long iOS path]]/Documents/dummyFile.txt")  
  m_status = (m_value = status_error, m_perms = perms_not_known)  
  m_symlink_status = (m_value = status_error, m_perms = perms_not_known)  
}  
```  
 I note that the addresses of both of these directory entries are very similar, but I have no idea where the bad path one comes from; it looks like general garbage. In particular, that's not the "end of directory" entry that we had just created as that has a null px pointer.  
  
And that's about as far as I can take it. It looks like a function `dereference` is returning a correct value, but the calling function gets a different one. 🤷

---

## Comment 2

> Username: mackworth  
> Created at: 2020-06-13 01:36:27 UTC  
> Url: https://github.com/boostorg/filesystem/issues/147#issuecomment-643549673  

Any thoughts on this?

---

## Comment 3

> Username: Lastique  
> Created at: 2020-06-13 08:36:25 UTC  
> Url: https://github.com/boostorg/filesystem/issues/147#issuecomment-643591835  

No idea. I have zero knowledge about iOS programming and no way to test.  
  
If dereferencing the iterator is where it breaks then possibly this is a compiler issue.

---

## Comment 4

> Username: Lastique  
> Created at: 2020-06-13 09:44:12 UTC  
> Url: https://github.com/boostorg/filesystem/issues/147#issuecomment-643599214  

Just as an idea to try, does it work if you change the code like this:  
  
```  
fs::directory_entry entry = *itr;  
fs::path path = entry.path();  
```

---

## Comment 5

> Username: mackworth  
> Created at: 2020-06-13 20:20:45 UTC  
> Url: https://github.com/boostorg/filesystem/issues/147#issuecomment-643673359  

This had no effect; in fact you can take out the for loop and just have  
` fs::directory_iterator itr(doc_path); `  
and it has the same effect.  
  
I was incorrect about the dereference. The problem is sooner than that.  If I single step through the directory_iterator constructor, it calls `detail::directory_iterator_construct`. line 463 is `imp = new detail::dir_itr_imp();` which creates an `instrusive_ptr` to a new `dir_itr_imp`. If you single step into dir_itr_tmp, it returns a proper value (e.g. handle/buffer null). Then it creates the intrusive_ptr. Then it gets weird.  In the debugger (stopped after line 463 is completed), I get the following results. As you can see, the dir_itr_tmp pointed to by imp.px is correct, but when I access any individual field it is not.  
  
````  
(lldb) p imp.px  
(boost::filesystem::detail::dir_itr_imp *) $18 = 0x00000002810d8200  
(lldb) p *imp.px  
(boost::filesystem::detail::dir_itr_imp) $19 = {  
  boost::sp_adl_block::intrusive_ref_counter<boost::filesystem::detail::dir_itr_imp, boost::sp_adl_block::thread_safe_counter> = {  
    m_ref_counter = {  
      mutex_ = (__sig = 1297437786, __opaque = "")  
      value_ = 1  
    }  
  }  
  dir_entry = {  
    m_path = (m_pathname = "")  
    m_status = (m_value = status_error, m_perms = perms_not_known)  
    m_symlink_status = (m_value = status_error, m_perms = perms_not_known)  
  }  
  handle = 0x0000000000000000  
  buffer = 0x0000000000000000  
}  
(lldb) p imp.px->buffer  
(void *) $21 = 0x4d55545a4d55545a  
(lldb) p imp.px->handle  
(__NSTaggedDate *) $22 = 0xfffffffd7ef27dff 1893-11-29 03:37:20 UTC  
(lldb) p imp.px->dir_entry  
(boost::filesystem::directory_entry) $23 = {  
  m_path = (m_pathname = "")  
  m_status = (m_value = status_error, m_perms = no_perms)  
  m_symlink_status = (m_value = fifo_file | socket_file | 0xfffffff0, m_perms = -1)  
}  
  
(lldb) p (*imp.px).buffer  
(void *) $24 = 0x4d55545a4d55545a  
(lldb) p (*imp.px).handle  
(__NSTaggedDate *) $25 = 0xfffffffd7ef27dff 1893-11-29 03:37:20 UTC  
  
````  
(Note, after the above commands, p *imp.px prints the unchanged values)  
  
And again, I'm stumped. Does the intrusivePtr somehow mess with struct offsets?

---

## Comment 6

> Username: Lastique  
> Created at: 2020-06-13 20:53:58 UTC  
> Updated at: 2020-06-13 20:56:14 UTC  
> Url: https://github.com/boostorg/filesystem/issues/147#issuecomment-643676961  

I'm not use how the debugger works when it calculates the offsets to structure members. But it gives me an idea.  
  
From your output, it looks like `intrusive_ref_counter::m_ref_counter` is based on a mutex rather than an atomic. This is unusual, as on most platforms I would expect `atomic_count` to be implemented using an atomic. Is it possible that the configuration is different when Boost is compiled and when your code using Boost is compiled? This could create ABI incompatibility between Boost and your code because of a different representation of `atomic_count` and, consequently, `dir_itr_imp`.  
  
Possible sources of inconsistency between building Boost and your code:  
  
- Different set of macros defined. Especially, Boost-specific macros.  
- Different C++ version is specified.  
- Different target CPU architecture is specified. (This could potentially enable atomic operations in one case and disable in the other.)  
- Different compiler is used.  
  
PS: I don't know what language is used in the original code sample, but it's definitely not C++. Maybe your compiler is not C++?

---

## Comment 7

> Username: mackworth  
> Created at: 2020-06-13 22:36:49 UTC  
> Updated at: 2020-06-13 22:38:47 UTC  
> Url: https://github.com/boostorg/filesystem/issues/147#issuecomment-643686706  

That turned out to be an excellent clue; thank you.  I note that the Mac (same language/compiler/ program; different OS/CPU architecture) shows just `m_ref_counter = {  value_ = 1 }` for the dir_itrwithout the mutex.  
  
Other than the architecture flags, the boost build script does add a change to the compiler flags:  
`EXTRA_ARM_FLAGS="-DBOOST_AC_USE_PTHREADS -DBOOST_SP_USE_PTHREADS -g -DNDEBUG"` with the comment:  
  
> The EXTRA_ARM_FLAGS definition works around a thread race issue in shared_ptr. I encountered this historically and have not verified that the fix is no longer required. Without using the posix thread primitives, an invalid compare-and-swap ARM instruction (non-thread-safe) was used for the shared_ptr use count causing nasty and subtle bugs.  
>   
> Note these flags (BOOST_AC_USE_PTHREADS and BOOST_SP_USE_PTHREADS) should only be defined for arm targets. They will cause random (but repeatable)shared_ptr crashes on macOS in boost thread destructors.  
  
The shared_ptr documentation says   
> You can define the macro `BOOST_SP_USE_PTHREADS` to turn off the lock-free platform-specific implementation and fall back to the generic `pthread_mutex_t`-based code.  
  
So, I tried turning off these flags, and ... now directory_iterator works fine.  So now my only problem is that rather vague threat about "nasty and subtle" bugs.  
  
This article: [Boost threads in iOS](https://stackoverflow.com/questions/14692802/boost-threads-in-ios-thread-info-object-is-being-destructed-before-the-thread) from back in 2013, says the answer is to use BOOST_SP_USE_SPINLOCK instead. I put that in, and it still works, although again, I have no way of knowing if this is reliable. Is there a test bed for to exercise atomics?  
  
So, thanks again. It's definitely not an issue with directory_iterator, so I'll close this out.  
  
---  
  
ps FYI, the language of the main program above is Objective C++, which is implemented by clang. It's a remarkable achievement: a true superset of both c++ and Objective C, so it's how one can use boost in Mac and iOS.

---

## Comment 8

> Username: Lastique  
> Created at: 2020-06-13 23:22:14 UTC  
> Updated at: 2020-06-13 23:26:42 UTC  
> Url: https://github.com/boostorg/filesystem/issues/147#issuecomment-643691457  

On most recent compilers `shared_ptr` should use compiler intrinsics to generate atomic operations, so you should be safe in that regard. I'm not sure about the original issue with the "compare-and-swap ARM instruction" (especially since there is no such single instruction), but the only ARM-specific piece I can see in the current code is [`boost/smart_ptr/detail/spinlock_gcc_arm.hpp`](https://github.com/boostorg/smart_ptr/blob/d1295a9974923e15d2bfd35aaae488325d348b20/include/boost/smart_ptr/detail/spinlock_gcc_arm.hpp#L55-L75), and it looks fine. And that code should not be used anyway if `std::atomic` is available.  
  
Whatever macros you define, you should define them consistently when Boost and your code is built. The same goes for the other things I mentioned earlier, which may affect ABI.  
  
Lastly, a side note regarding your code. You should avoid querying file status multiple times, which is what you do when you call `exists`, `is_regular_file` and `is_directory` on a path. Querying the filesystem is expensive. You can query the status once, from the `directory_entry` (e.g. by doing `it->status()`), and then test it.

---

## Comment 9

> Username: johnmai-dev  
> Created at: 2020-09-02 08:46:17 UTC  
> Url: https://github.com/boostorg/filesystem/issues/147#issuecomment-685451867  

@mackworth Have you solved this problem?

---

## Comment 10

> Username: mackworth  
> Created at: 2020-09-02 12:08:31 UTC  
> Url: https://github.com/boostorg/filesystem/issues/147#issuecomment-685688947  

Yes, turning off the flags “ -DBOOST_AC_USE_PTHREADS -DBOOST_SP_USE_PTHREADS” made it work fine.
