# #57 - Add child::async_wait [Open]

> Username: reddwarf69  
> Created at: 2018-11-21 15:20:20 UTC  
> Updated at: 2018-11-23 11:09:06 UTC  
> Url: https://github.com/boostorg/process/issues/57  

It would be very helpful to have an async_wait() method in `child`. In Unix it can be done asynchronous waiting for the SIGCHLD signal. I'm guessing there is something similar in Windows.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2018-11-21 15:51:22 UTC  
> Url: https://github.com/boostorg/process/issues/57#issuecomment-440714976  

I decided to not require `boost.asio for the usage of `booost.process`, so the current design doesn't cut it. You can use `on_exit` for that, which uses `asio` and `SIGCHLD` under the surface. Does that not cut it for you?

---

## Comment 2

> Username: reddwarf69  
> Created at: 2018-11-21 16:33:46 UTC  
> Updated at: 2018-11-21 16:33:57 UTC  
> Url: https://github.com/boostorg/process/issues/57#issuecomment-440729852  

I didn't actually notice on_exit was there, sorry. Yes, it's what I need.

---

## Comment 3

> Username: reddwarf69  
> Created at: 2018-11-22 11:06:48 UTC  
> Url: https://github.com/boostorg/process/issues/57#issuecomment-440995757  

I actually have some questions...  
  
In https://www.boost.org/doc/libs/1_68_0/doc/html/boost/process/group.html#idp44884880-bb "Wait for the process group to exit" means "wait for any process of the process group to exit" or "wait for all the processes of the process group to exit"? Whatever the case, maybe the docs could be modified to make it extra clear?  
  
I would want to be notified when my child, and every descendant of my child, has exited. This is probably only doable in Linux if cgroups are used, so limiting a bit my request (even if it would be cool to use cgroups to support this): I would want to be notified when my child, and every descendant of my child still in the same process group, has exited. This is not really doable with boost.proccess currently, is it?  
  
Does the behaviour of https://www.boost.org/doc/libs/1_68_0/doc/html/boost/process/on_exit.html depend on whether the child was created with a process group or not? There is a need for an "on_group_exit"?

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2018-11-22 12:36:58 UTC  
> Url: https://github.com/boostorg/process/issues/57#issuecomment-441017863  

1. Yeah, it means all children of the group. Rephrasing might be nice.  
2. I am not sure that's generally possible, since deep nested child processes might be in another group or have a `SIGIGN` set. You can just get the descriptor of the group through `native_handle` to use it for extensions - if that cuts it for cgroup.  
3. I am not sure that's feasible, because there's no `SIGGRP` - how would you implement that?

---

## Comment 5

> Username: reddwarf69  
> Created at: 2018-11-22 13:15:07 UTC  
> Url: https://github.com/boostorg/process/issues/57#issuecomment-441027486  

Yeah, at least to start with I would not bother trying to handle children that have changed process group. Those get lost. But I don't think it matters whether SIGCHLD is SIG_IGN in the children themselves, you will still get a signal if your own SIGCHLD is not SIG_IGN. If some child in the hierarchy has SIGCHLD with SIG_IGN either:  
- The grandchildren will clean up by themselves  
- The child will die first, the grandchildren will become your children, and you will waitid() for them.  
  
I guess "on_exit" currently works like this:  
1) Wait for SIGCHLD  
2) Waitid for the children that SIGCHLD has just signalled  
  
"on_group_exit" or "on_exit when the child has been created with a group" could:  
1) Wait for SIGCHLD  
2) waitid() for the children that SIGCHLD has just signalled  
3) (async)Wait for SIGCHLD  
4) waitid() for the process group id using the WNOHANG option  
5) If `4` returns ECHILD it means all the children in the process group have exited, `3` is cancel() and "on_group_exit"/"on_exit" is called

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2018-11-22 13:24:58 UTC  
> Url: https://github.com/boostorg/process/issues/57#issuecomment-441030119  

The second doesn't work, because you might lose information, since `waitpid` might reap a child process from the table while another part of your code waits for it.

---

## Comment 7

> Username: reddwarf69  
> Created at: 2018-11-22 13:48:36 UTC  
> Url: https://github.com/boostorg/process/issues/57#issuecomment-441036130  

Not sure I follow you here. But probably what you refer to can be solved by using WNOWAIT, in addition to WNOHANG, in `4`?  
  
http://man7.org/linux/man-pages/man2/waitpid.2.html  
`WNOWAIT     Leave the child in a waitable state; a later wait call can be used to again retrieve the child status information.`

---

## Comment 8

> Username: klemens-morgenstern  
> Created at: 2018-11-22 14:03:48 UTC  
> Url: https://github.com/boostorg/process/issues/57#issuecomment-441040099  

Ok, so you have process with pid `12` in group `13` and you wait for both asynchronously. If `SIGCHLD` gets triggered, the waiting for 12 gets it, calls `waitpid(12, &status, WNOHANG)` to check if it got it. Then it does not forward the signal. Even if it does, the handler for group 13 gets called, checks with `waitpid(-13, &status, WNOHANG)` but then gets nothing because the information already got handled by the first call. That goes both ways, so we lose information.

---

## Comment 9

> Username: reddwarf69  
> Created at: 2018-11-22 16:26:29 UTC  
> Url: https://github.com/boostorg/process/issues/57#issuecomment-441077946  

First, let me say I obviously said something silly with `The child will die first, the grandchildren will become your children, and you will waitid() for them`. init/"pid 1" becomes the parent of the grandchildren, not you.  
  
The good news is that it's not silly anymore since Linux 3.14, which has `PR_SET_CHILD_SUBREAPER`. But I don't think you would be interested in something Linux specific, only available since 30 Mar 2014?  
  
  
But this is what I was proposing:  
Supposing I have created a process group, 13, containing processes 12 and 13. Let's suppose PID 13 exits first.  
  
  
```  
/*  
 * FIRST  
 *  
 * me ->  
 *    -> PID 13 ->  
 *              -> PID 12  
 */  
  
prctl(PR_SET_CHILD_SUBREAPER, 1, 0, 0, 0);  
  
asio::signal_set chldSig(ioc, SIGCHLD);  
group myGroup;  
  
void do_child_wait() {  
	chldSig.async_wait(on_child_wait);  
}  
  
void on_child_wait(const error_code& ec, int) {  
	// PID 13 has exited  
	siginfo_t info;  
	waitid(P_PGID, myGroup.native_handle(), &info, WEXITED);  
	  
	// Add code here to store the returned status if it's the right/main child  
  
        /*  
         * NOW  
         *  
         * me ->  
         *    -> PID 12  
         */  
  
	int ret = waitid(P_PGID, myGroup.native_handle(), &info, WEXITED | WNOHANG | WNOWAIT); // Checks if there are more children, without blocking and without modifying anything  
        if(ret == -1)  
		if(errno != ECHILD) {  
			on_exit(error)  
			return;  
		}  
  
		// There are no more children after all  
		on_exit(status_code);  
		return;  
	}  
  
	// There are more children, so let's wait until on_child_wait gets called again by chldSig.async_wait()  
	do_child_wait();  
}  
```  
  
I have also changed the "`3` is cancel()" part from my explanation since I understood `chldSig` is not going to lose signals between async_wait() calls.

---

## Comment 10

> Username: reddwarf69  
> Created at: 2018-11-22 16:34:39 UTC  
> Updated at: 2018-11-23 11:09:06 UTC  
> Url: https://github.com/boostorg/process/issues/57#issuecomment-441079778  

>     1. Yeah, it means all children of the group. Rephrasing might be nice.  
  
FWIW I was now ready to open a different issue about this, since I was testing with boost 1.68. But I see you have fixed it already for 1.69 in https://github.com/boostorg/process/commit/eea73753b572ec5df69b2a624cbacf7569fde835 and it uses a quite similar thing to what I have just mentioned.  
  
But while eea73753b572ec5df69b2a624cbacf7569fde835 is the best that can be done in a standard way. Using `PR_SET_CHILD_SUBREAPER` if available would also make `boost::process::group::wait()` more deterministic. Without `PR_SET_CHILD_SUBREAPER`, `Wait for the process group to exit` in `boost::process::group::wait()` certainly means `all children of the group`, but not `all the processes in the group` (some processes can become children of PID 1, not of you) which is what I guess most people will expect to mean.
