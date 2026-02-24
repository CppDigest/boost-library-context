# #182 - UBSAN: sanitizers check failed for boost serialization on 1.71 and 1.72-beta [Open]

> Username: oneiric  
> Created at: 2019-11-22 03:15:41 UTC  
> Updated at: 2020-01-19 17:39:27 UTC  
> Url: https://github.com/boostorg/serialization/issues/182  

Hi expert,   
  
We enabled ubsan test in our project. But we found the failures on boost serialization.  
  
Follow the boost.build sanitizer docs, it is easy to reproduce the issues.  
https://boostorg.github.io/build/manual/master/index.html#_sanitizers  
  
Test OS: CentOS  
Compiler: gcc 8.3.1 (scl enable devtoolset-8 -- bash)  
  
Steps to reproduce it with boost test cases.  
1. cd serialization test folder:  
   boost/boost_1_72_0/libs/serialization/test  
2. Build and run with sanitizer option on:  
   b2 **undefined-sanitizer=on** -j20  
  
You will get a lot of runtime errors for "Segmentation fault".  
  
./test_array_text_archive   
../../../boost/archive/detail/interface_oarchive.hpp:47:16: runtime error: downcast of address 0x7fff396b5d10 which does not point to an object of type 'text_oarchive'  
0x7fff396b5d10: note: object is of type 'boost::archive::text_oarchive_impl<boost::archive::text_oarchive>'  
 00 00 00 00  20 a2 69 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::archive::text_oarchive_impl<boost::archive::text_oarchive>'

---

## Comment 1

> Username: oneiric  
> Created at: 2019-11-22 03:16:37 UTC  
> Url: https://github.com/boostorg/serialization/issues/182#issuecomment-557369871  

Do you have any best practice to fix these issues or options to ignore ubsan errors from boost serialization?

---

## Comment 2

> Username: robertramey  
> Created at: 2019-11-22 03:41:56 UTC  
> Url: https://github.com/boostorg/serialization/issues/182#issuecomment-557375227  

I am aware of this fault as it shows up in our test matrix here : http://www.boost.org/development/tests/develop/developer/serialization.html  
I'm not sure what to do about though.  I haven't had time to track it down.  If you want to do this and make a specific suggestion or submit a PR I would be happy to take a look at it.  
  
Robert Ramey

---

## Comment 3

> Username: Flamefire  
> Created at: 2020-01-16 15:40:07 UTC  
> Url: https://github.com/boostorg/serialization/issues/182#issuecomment-575209861  

Copied from my Slack reply:  
  
CRTP is used to cast to the most derived class boost::archive::binary_iarchive but this happens during construction and it is invalid to cast to the most derived class during construction from inside the ctor of a derived class as that most derived class does not exist yet.     
`binary_iarchive` calls the ctor of `binary_iarchive_impl` which calls `basic_binary_iprimitive::init` which calls `This()` and that does the invalid downcast. Easily verifiable in a debugger (BP at `This`)

---

## Comment 4

> Username: robertramey  
> Created at: 2020-01-16 21:53:45 UTC  
> Url: https://github.com/boostorg/serialization/issues/182#issuecomment-575364570  

I've checked in fixes which I believe address this.  You might want to verify that they actually fix the issue.

---

## Comment 5

> Username: Flamefire  
> Created at: 2020-01-19 16:18:10 UTC  
> Url: https://github.com/boostorg/serialization/issues/182#issuecomment-576020739  

They might. But now you've broken a contract for user which use this classes before this change which might have significant impact: The comment says to not derive from the most derived class: https://github.com/boostorg/serialization/commit/d95a1df37abbadc4e1de898a5b58a9cd68dd7d05#diff-dbb785895eddcd0e1994ff5dd49fada2R31-R34 but from the one where init() was moved out from: https://github.com/boostorg/serialization/commit/d95a1df37abbadc4e1de898a5b58a9cd68dd7d05#diff-0341327d12cf55daf5bec0f07104cc7dL81-R81  
  
So if a user derived from the latter he would now need to handle the init call, won't he?  
  
I don't understand those internals well enough to make a definitive call but wanted to raise this anyway so you can double-check whether this can be an issue.

---

## Comment 6

> Username: robertramey  
> Created at: 2020-01-19 17:39:26 UTC  
> Url: https://github.com/boostorg/serialization/issues/182#issuecomment-576027806  

> So if a user derived from the latter he would now need to handle the init call, won't he?  
  
He will.   
 It's a conscious trade off on my part. I don't see any other way to address this. And perhaps a breaking change.  An updated in the documentation might be in order.  I'll have to look into it.  init(...) implements the optional header that serialization archives have.  It would be a good idea to implement a method that detects the issue for existing archive classes implement by users.  Feel free to consider this.  Also I better bump the serialization library version check in case this messes up something.
