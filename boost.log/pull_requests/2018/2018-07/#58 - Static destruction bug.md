# #58 Static destruction bug [Closed]

> Username: thughes  
> Created at: 2018-07-18 17:32:03 UTC  
> Updated at: 2018-10-08 20:07:06 UTC  
> Closed at: 2018-10-08 20:07:06 UTC  
> Url: https://github.com/boostorg/log/pull/58  

This demonstrates an interesting bug that occurs on program exit during static destruction.  
  
statics are destructed in the reverse order in which they are created. In this case, the creation order is `LogDestructor` -> `LogConstructor` -> `lazy_singleton`, so reverse destruction means that when `LogDestructor` attempts to log in its destructor, `lazy_singleton` has already been destroyed.  
  
You can reproduce the crash 100% of the time by running this test case: https://github.com/boostorg/log/commit/907f3e2675afc5abac9d019d2cc42ed7ef926d50  
  
I added an additional commit that is a hacky workaround to make sure the singleton outlives all static variables:  https://github.com/boostorg/log/commit/9455f20d9bb1f9d8fe390f676b734f1c4384ffae. It will probably cause valgrind to complain (haven't tested). If you run with this workaround the test case no longer crashes.

---

## Comment 1

> Username: Lastique  
> Created_at: 2018-07-19 08:13:42 UTC  
> Url: https://github.com/boostorg/log/pull/58#issuecomment-406193549  

Introducing a leak is not an acceptable solution because there is essential logic happening during some singletons destruction. Also, the leaked resources may not be just memory but also file descriptors and handles to kernel objects.  
  
The current solution is that loggers hold the shared pointer reference to the logging core, which keeps it from destroying. There are a number of other singletons that don't use this trick, so Boost.Log offers only a very limited support for logging in global destructors.

---

## Comment 2

> Username: thughes  
> Created_at: 2018-07-19 23:01:52 UTC  
> Url: https://github.com/boostorg/log/pull/58#issuecomment-406439889  

Yep, I agree that a leak is not an acceptable solution (though since this is all happening when the program is exiting the OS should clean up any file descriptors, etc -- there could definitely be log completeness issues if things aren't flushed correctly etc, but a crash will have the same problem ;) ).  
  
Mainly I wanted to demonstrate that it was indeed the singleton lifetime that was the cause of the crash and generate some discussion to see if you had ideas on a better way to solve this. Perhaps it’s documented somewhere that you can’t log in a static object’s destructor, but often it’s quite useful (e.g.., tracing through object lifetime).  
  
It's actually quite easy to workaround; you simply have to ensure that the lazy_singleton is instantiated as the very first thing in the program. Unfortunately, the [specific singleton](https://github.com/boostorg/log/blob/cf8b4a3238ce27e3abd2fba662c5118de4c6493c/src/record_ostream.cpp#L71-L77  
) that seems to be causing this crash is lazily created at the time of the first log message, so you have to actually log something, not just create the logger:  
  
```patch  
--- a/test/run/static_destruction.cpp  
+++ b/test/run/static_destruction.cpp  
@@ -27,6 +27,8 @@ class LogDestructor {  
  
 int main(int argc, char* argv[])  
 {  
+  logger log;  // this alone not sufficient  
+  BOOST_LOG_SEV(log, boost::log::trivial::debug) << "Log from main";  
   static LogDestructor d;  
   static LogConstructor c;  
  
```   
  
I’d be fine with having an “InitializeLogging” API that I have to call as the very first thing in the program that does something exactly like this underneath the hood.  
  
In my case, I’d also be fine with having the singleton created non-lazily so that the lifetime of the static is tied to the lifetime of the very first logger instance created (`boost::log::sources::severity_channel_logger_mt<level>`). Generally, I think that’s behavior most people expect (if the logger instance hasn’t been destroyed you should be able to log).

---
