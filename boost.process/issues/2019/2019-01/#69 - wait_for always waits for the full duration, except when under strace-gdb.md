# #69 - wait_for always waits for the full duration, except when under strace/gdb [Open]

> Username: griwes  
> Created at: 2019-01-13 17:40:46 UTC  
> Updated at: 2025-02-26 15:05:15 UTC  
> Url: https://github.com/boostorg/process/issues/69  

System: Debian sid, x86_64  
  
Since 09381034276ce76eedd6936ae330159dec9938d9, the following program:  
  
```  
#include <iostream>  
#include <string>  
#include <vector>  
  
#include <boost/process.hpp>  
  
int main() {  
    std::vector<std::string> args{"/bin/echo", "hello from child"};  
  
    boost::process::ipstream out;  
  
    auto begin = std::chrono::high_resolution_clock::now();  
    bool timeout_flag = false;  
  
    boost::process::child child(args);  
    std::error_code ec;  
    if (!child.wait_for(std::chrono::seconds{10}, ec)) {  
        timeout_flag = true;  
        child.terminate(ec);  
    }  
  
    std::cout << "after wait_for" << std::endl;  
}  
```  
  
ends up taking 10 seconds to complete, even though the spawned process exits pretty much immediately... except when running under `strace` or `gdb`. When I attach `gdb` to an already running process, I can see that it is blocking in `sigtimedwait`.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2019-01-14 08:26:36 UTC  
> Url: https://github.com/boostorg/process/issues/69#issuecomment-453928799  

Thanks for reporting - are you using the Boost 1.69 or the current HEAD of the git repo?

---

## Comment 2

> Username: griwes  
> Created at: 2019-01-14 12:27:48 UTC  
> Url: https://github.com/boostorg/process/issues/69#issuecomment-453988500  

I was using 1.69, but I can see the same behavior on current master and develop as well.

---

## Comment 3

> Username: pfaffe  
> Created at: 2019-01-28 22:24:25 UTC  
> Url: https://github.com/boostorg/process/issues/69#issuecomment-458326395  

Duplicate of #60?

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2019-01-29 07:27:25 UTC  
> Url: https://github.com/boostorg/process/issues/69#issuecomment-458435048  

@pfaffe not if he has the same behaviour on current master. No idea what it is.

---

## Comment 5

> Username: griwes  
> Created at: 2019-02-01 22:54:19 UTC  
> Url: https://github.com/boostorg/process/issues/69#issuecomment-459895826  

@klemens-morgenstern have you been able to reproduce this? Or should I spend some more time investigating what may be the cause of this?

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2019-02-02 12:29:35 UTC  
> Url: https://github.com/boostorg/process/issues/69#issuecomment-459961412  

I have not been, I actually have tests for that: https://github.com/boostorg/process/blob/develop/test/wait_for.cpp  
  
I think the best solution would be if you add a test that shows the error.  
  
Did you try to run `echo` through the console by adding `bp::shell` btw.?

---

## Comment 7

> Username: pfaffe  
> Created at: 2019-02-02 13:14:47 UTC  
> Url: https://github.com/boostorg/process/issues/69#issuecomment-459964257  

@klemens-morgenstern  It reproduces for me on 03cba8f.

---

## Comment 8

> Username: klemens-morgenstern  
> Created at: 2019-02-03 13:20:26 UTC  
> Url: https://github.com/boostorg/process/issues/69#issuecomment-460051195  

Sure, but I can't reproduce it and the CI pass. So I am not sure how to approach this problem, honestly.

---

## Comment 9

> Username: griwes  
> Created at: 2019-03-20 13:04:07 UTC  
> Updated at: 2019-03-20 13:14:49 UTC  
> Url: https://github.com/boostorg/process/issues/69#issuecomment-474819467  

@klemens-morgenstern I don't have a good way to explain this yet, but as far as I can tell - the test you've linked verifies that the wait returns correct values after specific timeouts, and does some arbitrary test if a 1s timeout takes less than 5s, but that doesn't prove this bug isn't happening; what I described would still make that test pass.  
  
I'm still able to reproduce this with 1.70.0.beta1; here's a docker image of that, built with Clang and libc++ from LLVM 8, that I've reproduced it with: `griwes/llvm-boost:8.0.0-1.70.0.beta1` (ubuntu-based, so use `apt` to install further packages you may need). Please just use `c++` to compile, it's configured to build with Clang and libc++. (Boost.Test isn't built in that image, in case that matters.)  
  
Kernel versions of docker hosts I've reproduced this with, in case that matters:  
* Linux <snip> 4.20.14-towo.1-siduction-amd64 #1 SMP PREEMPT siduction 4.20-26 (2019-03-05) x86_64 GNU/Linux  
* Linux <snip> 4.19.0-3-amd64 #1 SMP Debian 4.19.20-1 (2019-02-11) x86_64 GNU/Linux

---

## Comment 10

> Username: klemens-morgenstern  
> Created at: 2019-03-20 15:57:12 UTC  
> Url: https://github.com/boostorg/process/issues/69#issuecomment-474899798  

Can you design a test that fails with the behaviour described by you?  
Please note that this might be a Kernle issue, i.e. that the kernel implementes sigtimedwait differently.

---

## Comment 11

> Username: griwes  
> Created at: 2019-03-20 16:09:19 UTC  
> Url: https://github.com/boostorg/process/issues/69#issuecomment-474905759  

Can't test right now, but I'd assume that something like this would fail (modeled after the first test in the test suite you've linked):  
  
```  
BOOST_AUTO_TEST_CASE(wait_for_exit_before_timeout)  
{  
    using boost::unit_test::framework::master_test_suite;  
  
    std::error_code ec;  
  
    auto launch_time = std::chrono::system_clock::now();  
    bp::child c(  
        master_test_suite().argv[1],  
        bp::args+={"test", "--wait", "1"},  
        ec  
    );  
    BOOST_REQUIRE(!ec);  
  
    BOOST_CHECK( c.wait_for(std::chrono::seconds(20)));  
  
    auto timeout_t = std::chrono::system_clock::now();  
  
    // check that we didn't wait the entire timeout period  
    BOOST_CHECK_LT(std::chrono::duration_cast<std::chrono::seconds>(timeout_t - launch_time).count(), 20);  
}  
```  
  
The problem with this is that on a system that is sufficiently under load this could still _in theory_ fail even when the behavior is correct; I think that something like 20 seconds should be enough to be sure about it, but feel free to adjust it.

---

## Comment 12

> Username: klemens-morgenstern  
> Created at: 2019-03-21 04:52:40 UTC  
> Updated at: 2019-03-21 08:27:37 UTC  
> Url: https://github.com/boostorg/process/issues/69#issuecomment-475112812  

Alright, added. Waiting for results now, build's running [here](https://travis-ci.org/klemens-morgenstern/boost-process/builds/509265760).  
  
Looks like it reproduces the bug. Will investigate further.

---

## Comment 13

> Username: griwes  
> Created at: 2019-05-06 16:42:55 UTC  
> Url: https://github.com/boostorg/process/issues/69#issuecomment-489688420  

I can see that that test has failed when you've run it initially. #197 does _not_ fix this, I can still see this same exact issue with current `develop`.

---

## Comment 14

> Username: senthil-ram  
> Created at: 2020-05-06 16:43:24 UTC  
> Url: https://github.com/boostorg/process/issues/69#issuecomment-624760463  

Is this issue resolved? After upgrade to boost 1.72 - we are seeing this issue. Did not see it in boost 1.67.

---

## Comment 15

> Username: klemens-morgenstern  
> Created at: 2020-05-07 05:57:08 UTC  
> Url: https://github.com/boostorg/process/issues/69#issuecomment-625045190  

I only added a test since 1.72, so the 1.73 wouldn't fix this. I will hopefully revisit the issue soon,  
  
Are you running on OSX?

---

## Comment 16

> Username: klemens-morgenstern  
> Created at: 2020-05-07 06:39:25 UTC  
> Url: https://github.com/boostorg/process/issues/69#issuecomment-625060951  

Would using `asio::deadline_timer` work for you? I am hesitant to do that implicitly, but that might be the only way to build a reliable solution.

---

## Comment 17

> Username: senthil-ram  
> Created at: 2020-05-12 01:22:25 UTC  
> Url: https://github.com/boostorg/process/issues/69#issuecomment-627052242  

> I only added a test since 1.72, so the 1.73 wouldn't fix this. I will hopefully revisit the issue soon,  
>   
> Are you running on OSX?  
  
On linux centos6/7.

---

## Comment 18

> Username: emenchen  
> Created at: 2021-09-29 22:49:56 UTC  
> Url: https://github.com/boostorg/process/issues/69#issuecomment-930601663  

Are you all sure that the process wasn't executing so quickly that it was done before you entered the wait? I'm not seeing this in 1.72. I put in a 30 second wait, and my test is finishing in ~10 seconds (doing other stuff). I'm checking running() before calling wait_for(std::chrono::seconds(30).

---

## Comment 19

> Username: jtousley  
> Created at: 2023-04-05 08:56:57 UTC  
> Url: https://github.com/boostorg/process/issues/69#issuecomment-1497148506  

@klemens-morgenstern Still seeing this issue as of Boost 1.81 - do we have any plans for a solution, or at least a workaround, yet? This was not a problem in Boost 1.68.  
  
@emenchen I am checking running() before calling wait_for as in the example, and it occurs both when executing fast commands (echo) and slow commands (sleep).

---

## Comment 20

> Username: ivantishchenko  
> Created at: 2024-01-03 12:55:04 UTC  
> Url: https://github.com/boostorg/process/issues/69#issuecomment-1875330200  

@klemens-morgenstern @griwes   
  
I also get the same issue. In my use case, I start one boost process from a unit test using GTest:  
  
```  
    boost::process::ipstream pipeStream;  
    boost::process::child boundingBoxChildProcess(  
        boundingBoxCommand, boost::process::std_in.close(), boost::process::std_out.close(),  
        boost::process::std_err > pipeStream);  
  
     ...  
  
     boundingBoxChildProcess.wait_for(timeout);  
  
     ...  
```  
  
wait_for will block for the exact duration specified in the variable **timeout**.  Interestingly enough, If I substitute wait_for with wait the snippet will always take around 230 ms. If I put wait_for back with a timeout equal to 5 ms, the function will take ~ 5 seconds 230 ms.  As such, it's always timeout_durtaion + duration as with regular .wait().  
  
Furthermore, I have another use case where I need to launch two boost processes one by one, with one process using the results of the previous process as inputs. If I use wait_for in both of these processes, sometimes the second process will be blocked indefinitely.  If I swap the call to wait_for(timeout) with wait() the code will be executed without waiting and no indefinite blocking will occur. Here are the outputs. In this run, I start two bounding box processes, followed by two rasterization processes, both type of processes use wait_for(timeout) to finish gracefully.  
```  
Starting to wait on bounding box  
Starting to wait on bounding box  
Bounding box waiting finished   
Bounding box waiting finished   
The sizes are 1 1  
Starting to wait for rasterization  
  
...  
  
The execution hangs at this point  
```  
I assume that it's a special case of the previous use case.  
  
Build information:  
Boost version 1.74.0, gcc-11, compiled with C++20 on Debian GNU/Linux 12 (bookworm) x86_64, 6.1.0-15-amd64.  
  
P.S.  
  
I ended up using wait instead of wait_for and spawning a subprocess on a separate detached thread in my application. It solves the issue. However, it would be still nice to use wait_for instead to avoid potential zombie processes.

---

## Comment 21

> Username: ivantishchenko  
> Created at: 2024-01-03 13:04:49 UTC  
> Url: https://github.com/boostorg/process/issues/69#issuecomment-1875344555  

@emenchen   
  
> Are you all sure that the process wasn't executing so quickly that it was done before you entered the wait? I'm not seeing this in 1.72. I put in a 30-second wait, and my test is finishing in ~10 seconds (doing other stuff). I'm checking running() before calling wait_for(std::chrono::seconds(30).  
  
I suspect that checking for `running()` isn't necessary before calling wait_for. wait_for employes wait_until which has a check for `if (!_exited())` inside.

---

## Comment 22

> Username: tstx  
> Created at: 2025-02-04 15:53:23 UTC  
> Url: https://github.com/boostorg/process/issues/69#issuecomment-2634385301  

are there any updates on this bug?   
running into the same issue on SLES 15 using: `boost-devel-1.69.0-150400.1.1.noarch`

---

## Comment 23

> Username: klemens-morgenstern  
> Created at: 2025-02-26 15:05:15 UTC  
> Url: https://github.com/boostorg/process/issues/69#issuecomment-2685343777  

The wait_for/wait_until functions are broken & deprecated. You should switch to boost process v2 and use asio's cancel_after.
