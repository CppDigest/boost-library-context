# #95 Update GitHub Actions CI file [Closed]

> Username: sdarwin  
> Created at: 2021-08-11 17:47:41 UTC  
> Updated at: 2021-09-05 23:28:34 UTC  
> Closed at: 2021-09-05 23:25:57 UTC  
> Url: https://github.com/boostorg/core/pull/95  

The Ubuntu 16.04 environment is scheduled to be removed from GitHub Actions in September 2021. Migrate those jobs to Docker containers or Ubuntu 18.04.

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2021-08-25 10:58:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/95#pullrequestreview-738200281  

📁 .github/workflows/ci.yml

```diff
 154 |+         run: |
 155 |+             apt-get -o Acquire::Retries=3 update && DEBIAN_FRONTEND=noninteractive apt-get -y install tzdata && apt-get -o Acquire::Retries=3 install -y sudo software-properties-common wget curl apt-transport-https make apt-file sudo unzip libssl-dev build-essential autotools-dev autoconf automake g++ libc++-helpers python ruby cpio gcc-multilib g++-multilib pkgconf python3 ccache libpython-dev
 156 |+             sudo apt-add-repository ppa:git-core/ppa
```

> Username: Lastique  
> Created_at: 2021-08-25 10:55:49 UTC  
> Updated_at: 2021-08-25 10:58:52 UTC  
> Url: https://github.com/boostorg/core/pull/95#discussion_r695635747  

Why install git from PPA?

> Username: sdarwin  
> Created_at: 2021-08-25 11:51:46 UTC  
> Url: https://github.com/boostorg/core/pull/95#discussion_r695676114  

Why install git from PPA? This CI script was designed to run on all the target operating systems including ubuntu LTS 12.04, 14.04, 16.04 etc. Those versions have quite out-of-date versions of git. This strategy, of installing newer utility packages such as git on older OS versions seems to be common with CI providers. Both Github Actions and Travis are doing that: they are providing the user with _newer_ versions of compilers, shells, etc.

> Username: Lastique  
> Created_at: 2021-08-25 19:18:53 UTC  
> Updated_at: 2021-08-25 19:18:54 UTC  
> Url: https://github.com/boostorg/core/pull/95#discussion_r696044844  

We are not using ancient Ubuntu versions, and I believe the ones we're using have recent enough git to be able to checkout Boost. Attaching a PPA is another point of failure or vulnerability. Hence I would prefer if we used the stock components as much as possible. This goes for both git and CMake.  
  
As for compilers, we have to attach PPAs because there simply aren't the compiler versions we want to test in the stock Ubuntu repos.

> Username: sdarwin  
> Created_at: 2021-08-25 20:39:11 UTC  
> Url: https://github.com/boostorg/core/pull/95#discussion_r696095843  

GitHub Actions, Travis, and probably most other CI providers are installing git from that exact PPA.  It should not cause issues. Here is more information:  
https://docs.travis-ci.com/user/reference/xenial/  
https://github.com/actions/virtual-environments/blob/main/images/linux/Ubuntu1804-README.md  
When you say "We are not using ancient Ubuntu versions", do you mean boostorg/core, or all of boost?  A number of boostorg repositories do have ancient Ubuntu versions in their CI.   If you go back one more Ubuntu release, the pre-installed git is missing certain flags and features.

> Username: Lastique  
> Created_at: 2021-08-25 21:01:41 UTC  
> Url: https://github.com/boostorg/core/pull/95#discussion_r696110499  

> GitHub Actions, Travis, and probably most other CI providers are installing git from that exact PPA.  
  
If they do this as part of their CI image preparation then this is their responsibility. Since we're using the CI service in the first place, we have to trust in their judgment in that process. They prepare the image once (i.e. not on every CI job run), which makes it less of a reliability issue.  
  
External repository reliability is a real issue. I had rather frequent Travis CI failures because of LLVM repository errors, as it apparently had a traffic limiter or something. Stock repos were cached by Travis, and so were never a problem. This exact issue may not come up with other repos, but I would rather not to rely on third parties when I don't have to. And in this case, I don't, as far as I can see.  
  
> When you say "We are not using ancient Ubuntu versions", do you mean boostorg/core, or all of boost?  
  
In this instance I'm talking about this particular library, Boost.Core. Other libraries may have other preferences.

---

📁 .github/workflows/ci.yml

```diff
 153 |+         if: matrix.container != ''
 154 |+         run: |
 155 |+             apt-get -o Acquire::Retries=3 update && DEBIAN_FRONTEND=noninteractive apt-get -y install tzdata && apt-get -o Acquire::Retries=3 install -y sudo software-properties-common wget curl apt-transport-https make apt-file sudo unzip libssl-dev build-essential autotools-dev autoconf automake g++ libc++-helpers python ruby cpio gcc-multilib g++-multilib pkgconf python3 ccache libpython-dev
```

> Username: Lastique  
> Created_at: 2021-08-25 10:56:05 UTC  
> Updated_at: 2021-08-25 10:58:52 UTC  
> Url: https://github.com/boostorg/core/pull/95#discussion_r695635933  

Is `sudo` missing here?

> Username: sdarwin  
> Created_at: 2021-08-25 11:52:19 UTC  
> Url: https://github.com/boostorg/core/pull/95#discussion_r695676480  

Is sudo missing here? Not on that line, no. The sudo package isn't pre-installed on all base docker images. It will be in the next step. After it's installed, then it can be used.

> Username: Lastique  
> Created_at: 2021-08-25 19:33:23 UTC  
> Url: https://github.com/boostorg/core/pull/95#discussion_r696054257  

Hmm, the reason I'm asking is you're using `sudo` for `apt-get` below but not here. Either this script fails because of insufficient privileges, or the later `sudo` is not needed (if the script is being run under root to begin with).

> Username: sdarwin  
> Created_at: 2021-08-25 20:42:52 UTC  
> Url: https://github.com/boostorg/core/pull/95#discussion_r696098357  

`sudo` cannot be used in the first line, because it doesn't exist yet.  
Then, the package is installed.  
You are right, that the later `sudo` isn't required, because the script is run under root to begin with.  It's just boilerplate.  "sudo apt-get" appears on most web documentation.

---

📁 .github/workflows/ci.yml

```diff
 159 |+             sudo wget https://bootstrap.pypa.io/pip/$python_version/get-pip.py
 160 |+             sudo python3 get-pip.py
 161 |+             sudo /usr/local/bin/pip install cmake
```

> Username: Lastique  
> Created_at: 2021-08-25 10:56:49 UTC  
> Updated_at: 2021-08-25 10:58:52 UTC  
> Url: https://github.com/boostorg/core/pull/95#discussion_r695636449  

Why install CMake using pip?

> Username: sdarwin  
> Created_at: 2021-08-25 11:53:57 UTC  
> Updated_at: 2021-08-25 12:03:33 UTC  
> Url: https://github.com/boostorg/core/pull/95#discussion_r695677601  

Why install CMake using pip? Similar to the git PPA issue. This is a way to control the version of cmake, potentially choosing any version, and be able to install the latest copy on an older operating system.

> Username: Lastique  
> Created_at: 2021-08-25 19:32:11 UTC  
> Url: https://github.com/boostorg/core/pull/95#discussion_r696053536  

My comment re git applies. In addition, you're not controlling the version here, you're just installing the latest one.  
  
When we test on a given platform, ideally we want to ensure the build works on that platform. This includes the build toolchain shipped with that distro. In other words, if we test on Ubuntu X, it would be good to know that Boost builds with the CMake version that is shipped with Ubuntu X rather than the newest one that happens to exist at the point of testing. This is not only to check the stock CMake version, it also indirectly tests compatibility with system libraries and the kernel.  
  
Also, personally, I don't trust pip or any other non-standard package managers. Mostly because there is no single authority who verifies and maintains the packages. Some package managers (not pip specifically) have a poor security track record.

> Username: sdarwin  
> Created_at: 2021-08-25 21:02:17 UTC  
> Updated_at: 2021-08-25 21:02:18 UTC  
> Url: https://github.com/boostorg/core/pull/95#discussion_r696110894  

I agree, that it would be better to test Ubuntu X, in it's original form.   Using the standard packages.  
The CI providers aren't doing that.  They install a different copy of cmake. But, they do control the version.  
Thinking about it.

---

📁 .github/workflows/ci.yml

```diff
 217 |         run: |
 191 |-           BUILD_JOBS=$((nproc || sysctl -n hw.ncpu) 2> /dev/null)
 218 |+           BUILD_JOBS=$( (nproc || sysctl -n hw.ncpu) 2> /dev/null )
```

> Username: Lastique  
> Created_at: 2021-08-25 10:57:42 UTC  
> Updated_at: 2021-08-25 10:58:52 UTC  
> Url: https://github.com/boostorg/core/pull/95#discussion_r695637122  

Is this necessary?

> Username: sdarwin  
> Created_at: 2021-08-25 11:59:16 UTC  
> Url: https://github.com/boostorg/core/pull/95#discussion_r695681180  

Is this small whitespace code change necessary? Yes. The script was crashing on Ubuntu 16 without it.  You could confirm, by reverting the change and then run CI.  I believe the reason it wasn't crashing in GitHub Actions before, is because they are providing _newer_ versions of certain packages, a modified environment.

> Username: Lastique  
> Created_at: 2021-08-25 19:32:47 UTC  
> Url: https://github.com/boostorg/core/pull/95#discussion_r696053903  

Ok, thanks.

> Username: Lastique  
> Created_at: 2021-09-05 18:56:21 UTC  
> Updated_at: 2021-09-05 18:56:22 UTC  
> Url: https://github.com/boostorg/core/pull/95#discussion_r702463229  

I have tried this in my own GHA attempts, and it still doesn't work. I believe, the problem is that `/bin/sh` is `dash` on Ununtu and older Ubuntu versions simply don't support the `$()` syntax, or at least not completely support.  
  
I worked around this by forcing `bash` shell, as described [here](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions#defaultsrun) (by setting `defaults.run.shell` to `bash`).


---

## Comment 2

> Username: pdimov  
> Created_at: 2021-08-25 19:32:49 UTC  
> Url: https://github.com/boostorg/core/pull/95#issuecomment-905815585  

Do we need this container business at all? How about just switching the jobs to 18.04?

---

## Comment 3

> Username: Lastique  
> Created_at: 2021-08-25 19:35:48 UTC  
> Url: https://github.com/boostorg/core/pull/95#issuecomment-905817357  

> Do we need this container business at all? How about just switching the jobs to 18.04?  
  
There might be a reason to test older systems just to make sure low-level stuff like `uncaught_exceptions.hpp` works. It relies on cxxabi library which is not part of the compiler.

---

## Comment 4

> Username: sdarwin  
> Created_at: 2021-08-25 20:51:35 UTC  
> Url: https://github.com/boostorg/core/pull/95#issuecomment-905864808  

> Do we need this container business at all? How about just switching the jobs to 18.04?  
  
The oldest compilers in the list aren't available on 18.04. Switching to 18.04 would mean discontinuing a number of tests.  Which could be an option.

---

## Comment 5

> Username: pdimov  
> Created_at: 2021-08-25 21:38:10 UTC  
> Url: https://github.com/boostorg/core/pull/95#issuecomment-905893970  

Looks like gcc-4.7, gcc-4.9, and clang-3.5 to clang-3.8 aren't available on 18.04.

---

## Comment 6

> Username: sdarwin  
> Created_at: 2021-08-27 16:08:19 UTC  
> Url: https://github.com/boostorg/core/pull/95#issuecomment-907312071  

Updated the GHA file, to address your concerns about cmake, git, and sudo. Have modified sudo and cmake. In terms of git, the "--jobs" flag wasn't available in git 2.7 which comes preinstalled on Ubuntu 16.04. So, the CI crashes using the preinstalled git.  I believe the PPA is still a good choice.  The git package is sufficiently distinct from C++ libraries being tested..

---

## Comment 7

> Username: pdimov  
> Created_at: 2021-08-27 16:14:45 UTC  
> Url: https://github.com/boostorg/core/pull/95#issuecomment-907316385  

Is `pip` not available from `apt`? Even if we don't do `pip install cmake` here, as one might want to test with the system version of cmake, libraries can still want to do that (`pip install cmake`), so it shouldn't hurt to have `pip` available.  
  
Further, I would switch all the jobs that can use 18.04, to it, and forego the container there. (This includes gcc 4.8, 5, 6, clang 3.9 and above.)

---

## Comment 8

> Username: sdarwin  
> Created_at: 2021-08-27 16:19:15 UTC  
> Url: https://github.com/boostorg/core/pull/95#issuecomment-907319408  

> Is pip not available from apt?  
  
The earlier versions of this script installed pip from apt.  
The latest news is that pip has become unsupported and fails spectacularly on Ubuntu 16.04.  
  
> I would switch all the jobs that can use 18.04  
  
alright, let's see.

---

## Comment 9

> Username: pdimov  
> Created_at: 2021-08-27 16:22:32 UTC  
> Url: https://github.com/boostorg/core/pull/95#issuecomment-907321483  

Here's the run where I've replaced 16.04 with 18.04 everywhere to test it: https://github.com/boostorg/core/actions/runs/1168195302

---

## Comment 10

> Username: pdimov  
> Created_at: 2021-08-27 16:26:03 UTC  
> Url: https://github.com/boostorg/core/pull/95#issuecomment-907323612  

It now occurs to me that I never tried to use ppa:ubuntu-toolchain-r/test. Maybe if that's added some of the failing compilers will work on 18.04.

---

## Comment 11

> Username: sdarwin  
> Created_at: 2021-08-27 18:14:44 UTC  
> Url: https://github.com/boostorg/core/pull/95#issuecomment-907385062  

Switched jobs to Ubuntu 18, rather than containers.  
  
>  it shouldn't hurt to have pip available.  
  
In that case, installing pip every time.

---

## Comment 12

> Username: Lastique  
> Created_at: 2021-09-05 23:28:33 UTC  
> Url: https://github.com/boostorg/core/pull/95#issuecomment-913246258  

I have committed an updated config file that incorporates most of this PR and some other changes. Thank you for the submission.

---
