# #114 Update GithubActions CI from boost-ci [Merged]

> Username: Flamefire  
> Created at: 2022-06-02 12:52:36 UTC  
> Updated at: 2022-06-10 09:06:24 UTC  
> Merged at: 2022-06-09 18:02:32 UTC  
> Closed at: 2022-06-09 18:02:32 UTC  
> Url: https://github.com/boostorg/unordered/pull/114  

Sync with upstream adding new compilers, and combined CMake jobs. See #116 on why this should have CMake tests.  
  
Also adds code coverage and a fix for a Segfault on clang.  
  
~MSYS2 builds fail due to emulated moves as it seems. See https://github.com/boostorg/unordered/runs/6708632068?check_suite_focus=true~  
  
Excluded:  
  
- GCC 4.4, 4.6, 4.7 - Quite old, wasn't tested before  
- Clang 3.5, 3.6 - Same  
- Coverity Scan - Not set up, possibly not required  
- Bigendian testing - Not required (don't see anything which could be endianess related)  
- Windows coverage collection - Takes to long

---

## Comment 1

> Username: cmazakas  
> Created_at: 2022-06-02 15:41:04 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1145011010  

@Flamefire These changes are amazing!  
  
I really appreciate you calling this out. Unfortunately, I had completely forgotten about the Boost CMake superbuild, hence why I so easily broke it.  
  
I really appreciate you adding these changes to prevent these kinds of issues in the future.

---

## Comment 2

> Username: cmazakas  
> Created_at: 2022-06-02 17:46:37 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1145135982  

@Flamefire We also might want to remove the CMake dependency on `container` here as it's no longer a dependency of the library so `depinst.py` no longer installs it. This would break the CMake build.

---

## Comment 3

> Username: Flamefire  
> Created_at: 2022-06-02 20:27:09 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1145309404  

> @Flamefire We also might want to remove the CMake dependency on `container` here as it's no longer a dependency of the library so `depinst.py` no longer installs it. This would break the CMake build.  
  
Didn't look like it broke anything. But yes, removed

---

## Comment 4

> Username: cmazakas  
> Created_at: 2022-06-02 21:12:48 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1145346353  

> > @Flamefire We also might want to remove the CMake dependency on `container` here as it's no longer a dependency of the library so `depinst.py` no longer installs it. This would break the CMake build.  
>   
> Didn't look like it broke anything. But yes, removed  
  
Something picked up from `depinst` command invocation is adding it, it seems.  
  
On my localhost, I did a "vanilla" depsint invocation and was able to create a config failure.

---

## Comment 5

> Username: Flamefire  
> Created_at: 2022-06-03 08:56:37 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1145750135  

Updated and rebased after changes from @pdimov and included the newly added compilers from https://github.com/boostorg/unordered/commit/0be48561445db619b7994b6c413d4fc9eec0fb6b  
  
@pdimov Any reason you did not use the template yml from Boost.CI? I find that format so much easier to read and quickly see what combinations/compilers are tested.  
Also the CMake tests have less C&P and are faster as the init part is only run once for each platform and not 3 times each for build-test, install-test, subdir-test.  
  
Just pointing out the relevant changes

---

## Comment 6

> Username: Flamefire  
> Created_at: 2022-06-03 10:41:17 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1145833746  

The 2 failing jobs will be fixed by #117

---

## Comment 7

> Username: pdimov  
> Created_at: 2022-06-03 11:44:35 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1145880077  

I just copy-pasted my CMake jobs from somewhere else. From what you say I infer that you have combined the three subdir/install/test into a single job? I'll take a look.  
  
The JSON style of describing the compiler configurations is probably better, yes.

---

## Comment 8

> Username: pdimov  
> Created_at: 2022-06-03 11:47:23 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1145882023  

> Something picked up from `depinst` command invocation is adding it, it seems.  
  
The depinst in this PR has `--include benchmark`, which pulls in `multi_index`, and `--include examples`, which pulls in `algorithm`. One of those then indirectly pulls in `container`, so it works. The depinst in the current ci.yml doesn't include these directories, so it doesn't install `container` and the (now redundant) linking to `Boost::container` was detected.

---

## Comment 9

> Username: Flamefire  
> Created_at: 2022-06-03 12:59:46 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1145938731  

> I just copy-pasted my CMake jobs from somewhere else.  
  
I'd suggest to C&P from Boost.CI, as that is what it is for. And if there are issues, those can be resolved there and then be applied to individual libs. That's what that repo is for: To have something uniform for all Boost authors to benefit.  
  
> From what you say I infer that you have combined the three subdir/install/test into a single job?  
  
Correct. Each of those 3 tests is now a step:   
  
- https://github.com/boostorg/boost-ci/blob/master/.github/workflows/ci.yml#L383  
- https://github.com/boostorg/boost-ci/blob/master/.github/workflows/ci.yml#L391  
- https://github.com/boostorg/boost-ci/blob/master/.github/workflows/ci.yml#L407  
  
Example timing: subdir test is 6s, but the setup alone takes over a minute. Not counting the time to spin up the runner.  
  
Looks like there are a few failures:  
- Clang 3.7 & 3.8 will be fixed by #117  
- GCC 12 shows similar failures as the (now removed) MSYS2 jobs (which IIRC also use GCC 12) `-Werror=maybe-uninitialized` at e.g. `first() = boost::move(x.first());` Not sure why it doesn't fail on develop where you added GCC 12 too. Maybe C++20 vs C++2a? Needs someone familiar with the code to fix those I guess. Or we switch to 2a and open an issue for 20.  
- Clang 14 sanitizer detects some new-free issue O.o

---

## Comment 10

> Username: pdimov  
> Created_at: 2022-06-03 13:18:26 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1145956320  

`2a` and `20` are synonyms, so there must be something else. And clang-14 is also on develop, with sanitization enabled, not sure why it passes there, too.

---

## Comment 11

> Username: pdimov  
> Created_at: 2022-06-03 13:21:29 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1145959237  

For clang-win you need `embed-manifest-via=linker`.

---

## Comment 12

> Username: pdimov  
> Created_at: 2022-06-03 13:28:31 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1145966220  

```  
gcc.compile.c++ bin.v2/libs/unordered/test/move_tests.test/gcc-12/release/cxxstd-03-iso/threading-multi/visibility-hidden/unordered/move_tests.o  
In file included from ./boost/unordered_map.hpp:17,  
                 from libs/unordered/test/unordered/move_tests.cpp:9:  
In member function ‘boost::unordered::unordered_map<K, T, H, P, A>::hasher boost::unordered::unordered_map<K, T, H, P, A>::hash_function() const [with K = test::object; T = test::object; H = test::hash; P = test::equal_to; A = test::cxx11_allocator<test::object, test::propagate_move>]’,  
    inlined from ‘void move_tests::move_construct_tests2(T*, const test::random_generator&) [with T = boost::unordered::unordered_map<test::object, test::object, test::hash, test::equal_to, test::cxx11_allocator<test::object, test::propagate_move> >]’ at libs/unordered/test/unordered/move_tests.cpp:126:7:  
./boost/unordered/unordered_map.hpp:1939:35: error: ‘y’ may be used uninitialized [-Werror=maybe-uninitialized]  
 1939 |       return table_.hash_function();  
      |                                   ^  
```  
That's cxxstd=03, so it's not about the 20. Something else is going on, but I don't know what; the gcc package that's being installed is the same in both cases.

---

## Comment 13

> Username: pdimov  
> Created_at: 2022-06-03 13:30:18 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1145967952  

The Clang sanitizer error is because you're testing libc++ and develop isn't.

---

## Comment 14

> Username: pdimov  
> Created_at: 2022-06-03 13:32:58 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1145970575  

```  
==21499==ERROR: AddressSanitizer: alloc-dealloc-mismatch (operator new vs free) on 0x606000000020  
    #0 0x558fcaa33112 in free (/home/runner/work/unordered/boost-root/bin.v2/libs/unordered/test/at_tests.test/clang-linux-14/debug/address-sanitizer-norecover/cxxstd-03-iso/stdlib-libc++/threading-multi/undefined-sanitizer-norecover/visibility-hidden/at_tests+0xb3112) (BuildId: a3be6e6107011cb71c727e21eec54dea3232739b)  
    #1 0x7fbd8b02e1dd in std::invalid_argument::~invalid_argument() (/lib/x86_64-linux-gnu/libc++abi.so.1+0x241dd) (BuildId: 92148758f185dd2a38d85fe5fe008fe28543e10c)  
    #2 0x558fcaa84b39 in boost::wrapexcept<std::out_of_range>::~wrapexcept() /home/runner/work/unordered/boost-root/./boost/throw_exception.hpp:71:47  
    #3 0x7fbd8b030553 in __cxa_end_catch (/lib/x86_64-linux-gnu/libc++abi.so.1+0x26553) (BuildId: 92148758f185dd2a38d85fe5fe008fe28543e10c)  
    #4 0x558fcaa70d96 in at_tests::at_tests_type::run() /home/runner/work/unordered/boost-root/libs/unordered/test/unordered/at_tests.cpp:29:5  
    #5 0x558fcaa74f6b in test::state::run_tests(bool) /home/runner/work/unordered/boost-root/libs/unordered/test/unordered/../helpers/test.hpp:87:12  
    #6 0x558fcaa72868 in main /home/runner/work/unordered/boost-root/libs/unordered/test/unordered/at_tests.cpp:67:1  
    #7 0x7fbd8ad02d8f  (/lib/x86_64-linux-gnu/libc.so.6+0x29d8f) (BuildId: 89c3cb85f9e55046776471fed05ec441581d1969)  
    #8 0x7fbd8ad02e3f in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x29e3f) (BuildId: 89c3cb85f9e55046776471fed05ec441581d1969)  
    #9 0x558fca9b0574 in _start (/home/runner/work/unordered/boost-root/bin.v2/libs/unordered/test/at_tests.test/clang-linux-14/debug/address-sanitizer-norecover/cxxstd-03-iso/stdlib-libc++/threading-multi/undefined-sanitizer-norecover/visibility-hidden/at_tests+0x30574) (BuildId: a3be6e6107011cb71c727e21eec54dea3232739b)  
  
0x606000000020 is located 0 bytes inside of 61-byte region [0x606000000020,0x60600000005d)  
allocated by thread T0 here:  
    #0 0x558fcaa6e18d in operator new(unsigned long) (/home/runner/work/unordered/boost-root/bin.v2/libs/unordered/test/at_tests.test/clang-linux-14/debug/address-sanitizer-norecover/cxxstd-03-iso/stdlib-libc++/threading-multi/undefined-sanitizer-norecover/visibility-hidden/at_tests+0xee18d) (BuildId: a3be6e6107011cb71c727e21eec54dea3232739b)  
    #1 0x7fbd8b099e7f in std::logic_error::logic_error(char const*) (/lib/x86_64-linux-gnu/libc++.so.1+0x4ee7f) (BuildId: b21b5b84d38618ac5758baefbe063160913da4cb)  
```  
The constructor is in libc++.so, but the destructor is in libc++abi.so. This doesn't seem right at all.

---

## Comment 15

> Username: pdimov  
> Created_at: 2022-06-03 13:48:22 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1145984282  

Looks like this arrangement is correct, the destructor is indeed in libc++abi. The code seems to use `::operator delete` correctly, https://github.com/llvm/llvm-project/blob/09c2b7c35af8c4bad39f03e9f60df8bd07323028/libcxx/src/include/refstring.h#L124, but I suppose when libc++abi is built the `operator delete` is being inlined into `free`. Not sure what can be done about that.

---

## Comment 16

> Username: pdimov  
> Created_at: 2022-06-03 14:07:11 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1146001199  

Oh, the gcc-12 difference may be because develop uses sanitizers: true there, which could be the thing masking them.

---

## Comment 17

> Username: pdimov  
> Created_at: 2022-06-03 14:22:28 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1146016094  

Looks like GCC 12 thinks that some `empty_value` part of the return value of `create` (https://github.com/boostorg/unordered/blob/8a74b192b08a22bb771b9f5763d81e4bd9d16c0f/test/unordered/move_tests.cpp#L121 and https://github.com/boostorg/unordered/blob/8a74b192b08a22bb771b9f5763d81e4bd9d16c0f/test/unordered/move_tests.cpp#L98) is uninitialized for some reason, I'll leave it to Christian to figure out why.

---

## Comment 18

> Username: cmazakas  
> Created_at: 2022-06-03 14:47:02 UTC  
> Updated_at: 2022-06-03 14:53:31 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1146039472  

> I'll leave it to Christian to figure out why.  
  
Sounds good to me!  
  
I do actually enjoy solving obscure compiler issues. Plus, I get to install gcc-12 now!  
  
> Clang 3.7 & 3.8 will be fixed by https://github.com/boostorg/unordered/pull/117  
  
Ha, I was aware of the macro-related issue before but didn't act on it because the compiler simply wasn't part of our CI. Thank you for adding this. I should've cleaned that up sooner.

---

## Comment 19

> Username: pdimov  
> Created_at: 2022-06-03 16:01:41 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1146105014  

> Oh, the gcc-12 difference may be because develop uses sanitizers: true there, which could be the thing masking them.  
  
That's indeed it. https://github.com/boostorg/unordered/runs/6727166558?check_suite_focus=true

---

## Comment 20

> Username: Flamefire  
> Created_at: 2022-06-03 21:10:42 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1146365139  

Ok rebased again and added individual commits to fix/workaround the previous failures. I was aware of the manifest thing but forgot to readd it after one of the rebases... So would be great to merge this once CI ran to avoid further conflicts

---

## Comment 21

> Username: Flamefire  
> Created_at: 2022-06-04 09:49:18 UTC  
> Updated_at: 2022-06-04 11:11:43 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1146579810  

2 more fixes:  
- GCC 12 times out after 2h O.o Trying with reducing the build jobs in half testing only static builds  
- Clang 3.8 segfaults during name mangling of a metaprogramming function -> Replace by types  
  
There still is an issue with CCache I'm currently investigating. Should be a small fix I'll add on top.  
  
Edit: Done

---

## Comment 22

> Username: Flamefire  
> Created_at: 2022-06-05 08:27:22 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1146764158  

Ok got it working now after the [fix for Clang 3.8](https://github.com/boostorg/unordered/pull/114/commits/d1796db86ade83003e11a4ff172155d49040fa90) works.  
@pdimov MP11 could use a helper function to create an `index_seq` for a list directly in addition to `make_index_sequence_for`,      
i.e. for what I did here: `index_seq = boost::mp11::make_index_sequence<boost::mp11::mp_size<list>::value>;`  
  
Not sure which is faster for compile times but the extra step of needing a function to extract the types of a list results in an extra name mangling (and hence likely an extra symbol) which in case of Clang 3.8 additionally causes a compiler segfault.

---

## Comment 23

> Username: pdimov  
> Created_at: 2022-06-05 16:15:05 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1146841676  

Yeah, I think this fix should be applied regardless of what we do with this PR.

---

## Comment 24

> Username: pdimov  
> Created_at: 2022-06-05 16:18:00 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1146842122  

As for this PR itself, if @cmazakas and @joaquintides prefer this form of CI, I will not oppose the change.

---

## Comment 25

> Username: Flamefire  
> Created_at: 2022-06-05 18:15:08 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1146859744  

> As for this PR itself, if @cmazakas and @joaquintides prefer this form of CI, I will not oppose the change.  
  
Is there any downside of "this form of CI"? The CMake-test change makes CI faster, the JSON-style the file cleaner and bringing it in line with Boost.CI makes updating it easier. Just want to make sure I'm not missing anything.

---

## Comment 26

> Username: cmazakas  
> Created_at: 2022-06-06 16:21:30 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1147635332  

In general, following the flow of scripts is actually pretty tricky and has one jumping all over the place but in general, it's a dramatic improvement from before.  
  
I only have one question, really. Is there anything else we need to do for the code coverage stuff? Do we need to make an account somewhere to link the Unordered repo to? Or does it all Just Work? I've never setup code coverage for a repo before so I'm not certain about the steps required.

---

## Comment 27

> Username: Flamefire  
> Created_at: 2022-06-06 18:52:26 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1147776898  

> I only have one question, really. Is there anything else we need to do for the code coverage stuff? Do we need to make an account somewhere to link the Unordered repo to? Or does it all Just Work? I've never setup code coverage for a repo before so I'm not certain about the steps required.  
  
I think you need to authorize at codecov.io yes, and add the codecov jobs. I excluded the jobs here but left the code dealing with that in so you can add them when you feel like. It would be a very small change. Just c&p the  ci.yml from boost-ci and you'll see the jobs.  
Do you want coverage data? If so I can add that here too. It wasn't here before so I thought you don't need or care about it.

---

## Comment 28

> Username: cmazakas  
> Created_at: 2022-06-07 16:16:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1148882379  

> Do you want coverage data? If so I can add that here too. It wasn't here before so I thought you don't need or care about it.  
  
I'd be remiss to ask you to add it but I mean, I certainly won't reject the work if you're volunteering.  
  
We care about coverage but CI can be kind of a pain and I've never setup coverage before so I was putting it off.

---

## Comment 29

> Username: Flamefire  
> Created_at: 2022-06-08 08:57:01 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1149647337  

> I'd be remiss to ask you to add it but I mean, I certainly won't reject the work if you're volunteering.  
>   
> We care about coverage but CI can be kind of a pain and I've never setup coverage before so I was putting it off.  
  
It is very easy, see https://github.com/boostorg/boost-ci/tree/master#code-coverage  
  
I updated the GHA and appveyor configs to the Boost.CI versions (plus some adjustments for here, e.g. not testing shared + static builds) and included the coverage jobs and the codecov config file. See the last commits.  
  
Note that codecov does only consider the config in the main (i.e. master) branch. So if you change that file you need to commit/merge it to master before you see the effect. Anyway I expect the rest (i.e. basic coverage report) to just work right away.

---

## Comment 30

> Username: Flamefire  
> Created_at: 2022-06-08 19:28:37 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1150313114  

99% coverage. Not bad ;-) https://codecov.io/gh/boostorg/unordered/tree/1d9f48fc94ab65310048704c24b6b74310137c42

---

## Comment 31

> Username: Flamefire  
> Created_at: 2022-06-09 09:08:54 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1150868988  

Rebased and readded MSYS2 tests after #119 was merged.

---

## Comment 32

> Username: joaquintides  
> Created_at: 2022-06-10 09:06:24 UTC  
> Url: https://github.com/boostorg/unordered/pull/114#issuecomment-1152145750  

Thanks Alexander for this huge effort!

---
