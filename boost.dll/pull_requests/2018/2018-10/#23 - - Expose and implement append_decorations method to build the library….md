# #23 - Expose and implement append_decorations method to build the library… [Merged]

> Username: Firefly35  
> Created at: 2018-10-31 17:35:50 UTC  
> Updated at: 2019-01-08 15:46:34 UTC  
> Merged at: 2019-01-03 15:23:03 UTC  
> Closed at: 2019-01-03 15:23:03 UTC  
> Url: https://github.com/boostorg/dll/pull/23  

… path without loading the library.  
  
It allows to get the concrete path that will be loaded and allows the user to use the concrete path prior to effectively load the library.  
- #22 (which is similar to #19) issue proposal fix :Add error handling for posix platforms (gnu compiler compilation) when a library with missing symbols is asked for loading : check the library path exists and we do not try to load the current executable.  
In this case : return the current dlerror (useless to try the exactly specified path as the decorated path points to a valid shared library file).

---

## Review 1 [Changes requested]

> Username: apolukhin  
> Created_at: 2018-11-07 12:59:32 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/dll/pull/23#pullrequestreview-170564984  

📁 include/boost/dll/detail/posix/shared_library_impl.hpp

```diff
  61 |     }
  62 | 
  63 |+     static boost::filesystem::path append_decorations(boost::filesystem::path sl) {
```

> Username: apolukhin  
> Created_at: 2018-11-07 12:48:48 UTC  
> Updated_at: 2018-11-13 08:12:12 UTC  
> Url: https://github.com/boostorg/dll/pull/23#discussion_r231490816  

const &

> Username: Firefly35  
> Created_at: 2018-11-07 14:04:40 UTC  
> Updated_at: 2018-11-13 08:12:12 UTC  
> Url: https://github.com/boostorg/dll/pull/23#discussion_r231514672  

```diff  
-    static boost::filesystem::path append_decorations(boost::filesystem::path sl) {  
+    static boost::filesystem::path append_decorations(const boost::filesystem::path & sl) {  
```

---

📁 include/boost/dll/detail/posix/shared_library_impl.hpp

```diff
 128 |+                 // decorated path exists : current error is not a bad file descriptor and we are not trying to load the executable itself
 129 |+                 ec = boost::system::error_code(
 130 |+                     boost::system::errc::bad_file_descriptor,
```

> Username: apolukhin  
> Created_at: 2018-11-07 12:52:33 UTC  
> Updated_at: 2018-11-13 08:12:12 UTC  
> Url: https://github.com/boostorg/dll/pull/23#discussion_r231491876  

According to the comment above errc::bad_file_descriptor is not the right error code

> Username: Firefly35  
> Created_at: 2018-11-07 14:09:33 UTC  
> Updated_at: 2018-11-13 08:12:12 UTC  
> Url: https://github.com/boostorg/dll/pull/23#discussion_r231516446  

Indeed, but I didn't find any other boost::system::errc:: value that could fit with the current error category.  
So I stuck to the "bad_file_descriptor" as here it is the real dlerror() error message that should refine the error.  
Do you know of another value in boost::system::errc:: that could best fit the error at this place ?

> Username: apolukhin  
> Created_at: 2018-11-10 02:18:23 UTC  
> Updated_at: 2018-11-13 08:12:12 UTC  
> Url: https://github.com/boostorg/dll/pull/23#discussion_r232437922  

How about `executable_format_error`?

> Username: Firefly35  
> Created_at: 2018-11-13 08:14:16 UTC  
> Url: https://github.com/boostorg/dll/pull/23#discussion_r232931779  

Fixed in latest commit

---

📁 include/boost/dll/detail/posix/shared_library_impl.hpp

```diff
 125 |+             boost::system::error_code prog_loc_err;
 126 |+             boost::filesystem::path loc = boost::dll::detail::program_location_impl(prog_loc_err);
 127 |+             if (boost::filesystem::exists(actual_path) && !boost::filesystem::equivalent(sl, loc, prog_loc_err)) {
```

> Username: apolukhin  
> Created_at: 2018-11-07 12:59:06 UTC  
> Updated_at: 2018-11-13 08:12:12 UTC  
> Url: https://github.com/boostorg/dll/pull/23#discussion_r231493736  

Looks like the same check should be added to the Windows implementation.

> Username: Firefly35  
> Created_at: 2018-11-13 08:14:51 UTC  
> Updated_at: 2018-11-13 08:14:52 UTC  
> Url: https://github.com/boostorg/dll/pull/23#discussion_r232931901  

A similar check is now present in the latest commit


📁 include/boost/dll/detail/windows/shared_library_impl.hpp

```diff
  53 |     }
  54 | 
  55 |+     static boost::filesystem::path append_decorations(boost::filesystem::path sl) {
```

> Username: apolukhin  
> Created_at: 2018-11-07 12:53:06 UTC  
> Updated_at: 2018-11-13 08:12:12 UTC  
> Url: https://github.com/boostorg/dll/pull/23#discussion_r231492034  

const &

> Username: Firefly35  
> Created_at: 2018-11-07 14:05:01 UTC  
> Updated_at: 2018-11-13 08:12:12 UTC  
> Url: https://github.com/boostorg/dll/pull/23#discussion_r231514787  

```diff  
-    static boost::filesystem::path append_decorations(boost::filesystem::path sl) {  
+    static boost::filesystem::path append_decorations(const boost::filesystem::path & sl) {  
```


📁 include/boost/dll/shared_library.hpp

```diff
 512 |     }
 513 | 
 514 |+     static boost::filesystem::path append_decorations(boost::filesystem::path sl) {
```

> Username: apolukhin  
> Created_at: 2018-11-07 12:57:31 UTC  
> Updated_at: 2018-11-13 08:12:12 UTC  
> Url: https://github.com/boostorg/dll/pull/23#discussion_r231493282  

Remove this function from the public interface for now. It has a very bad name. It looks like the function just concatenates strings, but it actually does some heavy system calls and produces different answers, depending on the user's environment.

> Username: Firefly35  
> Created_at: 2018-11-07 14:06:42 UTC  
> Updated_at: 2018-11-13 08:12:12 UTC  
> Url: https://github.com/boostorg/dll/pull/23#discussion_r231515371  

Maybe we can find another name for this method ? I'd like to have it in the public interface to figure out the computed path before the real "load" occurs.

> Username: apolukhin  
> Created_at: 2018-11-10 02:24:05 UTC  
> Updated_at: 2018-11-13 08:12:12 UTC  
> Url: https://github.com/boostorg/dll/pull/23#discussion_r232438135  

Choose one:  
decorate_and_query  
decorate_and_locate  
search_for_decorated  
query_for_decorated  
...or just invent your own.  
  
  
BTW some docs are required, if you wish to make that function a public interface

> Username: Firefly35  
> Created_at: 2018-11-13 08:08:36 UTC  
> Updated_at: 2018-11-13 08:12:12 UTC  
> Url: https://github.com/boostorg/dll/pull/23#discussion_r232930443  

Last commit changed the method name to decorate()


---

## Review 2 [Changes requested]

> Username: apolukhin  
> Created_at: 2018-11-30 08:37:03 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/dll/pull/23#pullrequestreview-180161627  

📁 include/boost/dll/detail/windows/shared_library_impl.hpp

```diff
  94 |+             if (boost::filesystem::exists(load_path)) {
  95 |+                 // decorated path exists : current error is not a bad file descriptor
  96 |+                 ec = boost::dll::detail::last_error_code();
```

> Username: apolukhin  
> Created_at: 2018-11-30 08:33:59 UTC  
> Updated_at: 2018-11-30 08:37:03 UTC  
> Url: https://github.com/boostorg/dll/pull/23#discussion_r237779790  

That's the wrong way to use last_error_code. The last_error_code function must be called immediately after the system function in wich error code we are interested in. Putting filesystem::exists between the LoadLibrary and last_error_code breaks the error reporting.  
  
So just move the `ec = boost::dll::detail::last_error_code();` line before the `if` statement. That will do the job.


📁 include/boost/dll/shared_library.hpp

```diff
 513 | 
 514 |+     /*!
 515 |+     * Returns the decorated path to a shared module name, i.e. with needed prefix/suffix added.
```

> Username: apolukhin  
> Created_at: 2018-11-30 08:36:54 UTC  
> Updated_at: 2018-11-30 08:37:03 UTC  
> Url: https://github.com/boostorg/dll/pull/23#discussion_r237780441  

Need more scary notes:  
`Returns the decorated path to a shared module name for the current runtime environment, i.e. ...`  
`Calling this function with different current directories may produce different results.`

> Username: Firefly35  
> Created_at: 2018-11-30 09:24:08 UTC  
> Url: https://github.com/boostorg/dll/pull/23#discussion_r237793588  

Can you explain what you mean with "different current directories" ?

> Username: apolukhin  
> Created_at: 2018-11-30 18:22:29 UTC  
> Updated_at: 2018-11-30 18:22:52 UTC  
> Url: https://github.com/boostorg/dll/pull/23#discussion_r237955485  

Ther result of `shared_library::decorate("foo")` may not be the same, if user changes the current directory via https://en.cppreference.com/w/cpp/filesystem/current_path  
For example it may return libfoo.so and foo.so, depending on the working directory.  
  
Oh, one more thing: please add a test, that if we create an empty file with name `test_broken.so` and try to load it, then the the loading fails. You may also write some other tests.


---

## Comment 3

> Username: apolukhin  
> Created_at: 2018-11-30 08:37:29 UTC  
> Url: https://github.com/boostorg/dll/pull/23#issuecomment-443131047  

Almost ready for merge :)

---

## Comment 4

> Username: apolukhin  
> Created_at: 2019-01-03 15:28:16 UTC  
> Url: https://github.com/boostorg/dll/pull/23#issuecomment-451176871  

Many thanks for the PR!   
  
I've changed a few things in https://github.com/boostorg/dll/commit/7981565592c135cf3e34b04f513ed7276fc79b71  
  
We need a better name for the `decorate()`. It must be scary so that people would not use it without huge need.

---

## Comment 5

> Username: Firefly35  
> Created_at: 2019-01-07 10:16:43 UTC  
> Url: https://github.com/boostorg/dll/pull/23#issuecomment-451887139  

Thanks Antony, feel sorry that I didn't have time to finalize the PR with your comments.  
I wish you an happy new year for 2019.

---

## Comment 6

> Username: apolukhin  
> Created_at: 2019-01-08 15:46:33 UTC  
> Url: https://github.com/boostorg/dll/pull/23#issuecomment-452346116  

Thanks to you! You made the most part of the job.  
  
Happy new Year :)

---
