# #259 - process_group for v2 [Open]

> Username: klemens-morgenstern  
> Created at: 2022-06-11 05:40:55 UTC  
> Updated at: 2025-04-11 15:51:24 UTC  
> Url: https://github.com/boostorg/process/issues/259  

See klemens-morgenstern/boost-process#216

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-06-11 06:29:56 UTC  
> Url: https://github.com/boostorg/process/issues/259#issuecomment-1152866942  

@kannon92 @emmenlau  
  
I think the correct solution here is a process-group. I haven't added that to v2 yet, because it's not trivial to do the async_wait, but it might solve your issues. I'll use the issue to spec out a potential process_group interface, and we'll see if it matches your requirements.  
  
The process group would have a lead process (which doesn't mean as much on windows, but it's the source of the ::gid on linux), so you could use it like this:  
  
Note that the process_group would be a console process group & job object on windows. so it can be SIGINT-ed.  
  
This is what I envision:  
  
```cpp  
asio::io_context ctx;  
v2::process_group proc_grp{ctx, "my_proc", {}};  
```  
  
Now this should address klemens-morgenstern/boost-process#216 already, since all sub process of `my_proc` would be in the same process-group, thus reaped when the process group is done.  
  
However, if one wanted to add more processes it could be done like so:  
  
```cpp  
pid_type pid = proc_grp.insert("other-proc", {});  
pid_type pid2 = proc_grp.insert("other-proc", {});  
  
  
proc_grp.interrupt();//send SIGINT to all subprocesses.  
  
// wait for one process to exit - also should have async_wait_one()  
auto [pid_exited, int] = proc_grp.wait_one();  
// wait for all processes to exit  
proc_grp.wait_all();.  
```

---

## Comment 2

> Username: emmenlau  
> Created at: 2022-06-11 07:44:47 UTC  
> Url: https://github.com/boostorg/process/issues/259#issuecomment-1152876180  

Dear @klemens-morgenstern , this sounds very promising! Do I understand correctly that as a default, all processes would start in the same process group? Or is that something that the user has to explicitly do? It may be helpful if all processes default to the same process group, so that unsuspecting users do not encounter problems of un-reaped processes on application end.  
  
I think this is perfectly in line with the behavior we need, and all the better if the process group can be extended with other processes later!

---

## Comment 3

> Username: egorpugin  
> Created at: 2022-06-11 13:07:08 UTC  
> Updated at: 2022-06-11 13:36:00 UTC  
> Url: https://github.com/boostorg/process/issues/259#issuecomment-1152925299  

@klemens-morgenstern  
  
Process group is also used for killing children when **current** process dies.  
Grep `job` here https://github.com/libuv/libuv/blob/v1.x/src/win/process.c#L63=  
  
Like when I spawn some build jobs, they all die on control-c with main process.  
So the interface could be also like  
```  
asio::io_context ctx;  
v2::process_group proc_grp{ctx}; // new proc group  
auto p1 = process{ctx, "some_proc", {}, proc_grp};  
auto p2 = process{ctx, "some_proc", {}, proc_grp};  
  
auto p3 = process{ctx, "some_proc", {}, v2::default_proc_grp}; // static var, this process' group/job  
```  
  
Upd.:  
As for waiting all job's processes to terminate, possible solution can be found here https://devblogs.microsoft.com/oldnewthing/20130405-00/?p=4743

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2022-06-11 15:59:19 UTC  
> Url: https://github.com/boostorg/process/issues/259#issuecomment-1152955508  

Oh ok, you think defaulting to a default group is a good idea? Shouldn't that be controlled by the parent process ? I.e. the grandparent of the started process?  
  
Egor what you proposed is similar to what v1 does with groups, but it doesn't work without some weird constructs, due to `waitpid` reaping the child process. I.e. if I already waited for a given process through the group it'll be removed from the porocess list and I get a `no child process error` e.g.:  
  
```cpp  
asio::io_context ctx;  
v2::process_group proc_grp{ctx}; // new proc group  
auto p1 = process{ctx, "some_proc", {}, proc_grp};  
  
proc_grp.wait_one();  
p1.wait(); // throws no such process.  
```  
  
Which is why I like the idea of emplacing the process into the group more. I would support a detach function (i.e. steal a process from a group) but I don't think the winapi supports that.

---

## Comment 5

> Username: egorpugin  
> Created at: 2022-06-11 17:32:06 UTC  
> Url: https://github.com/boostorg/process/issues/259#issuecomment-1152969812  

> Oh ok, you think defaulting to a default group is a good idea?  
  
I mean how to add new process to the current job with your proposed interface?  
  
Example (windows terms):  
```  
Current process does not belong to any job. I.e. parent (grandparent for our children) did not create any jobs etc.  
To kill all our children when we die, we need 1) create job, 2) add children handles to it.  
```  
  
How will this look like?  
  
---  
  
> Oh ok, you think defaulting to a default group is a good idea? Shouldn't that be controlled by the parent process ? I.e. the grandparent of the started process?  
  
On windows each process may be a part of several jobs. So, when grandparent dies, current process dies, so all children die. Looks ok.  
But I'm not sure how this works on posix.

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2022-06-12 05:40:03 UTC  
> Url: https://github.com/boostorg/process/issues/259#issuecomment-1153078053  

Interesting question , I know on linux you are by default in the same GID - is that not the case on windows?   
  
I thought you need to set`JOB_OBJECT_LIMIT_BREAKAWAY_OK ` explicitly to move to a different job, thus it would be same default? Am I misunderstanding something?

---

## Comment 7

> Username: egorpugin  
> Created at: 2022-06-12 10:26:14 UTC  
> Url: https://github.com/boostorg/process/issues/259#issuecomment-1153123428  

Probably since we have access to native handles, user can setup needed behavior on windows himself.

---

## Comment 8

> Username: emmenlau  
> Created at: 2022-06-12 10:40:54 UTC  
> Updated at: 2022-09-20 11:08:47 UTC  
> Url: https://github.com/boostorg/process/issues/259#issuecomment-1153125671  

> Probably since we have access to native handles, user can setup needed behavior on windows himself.  
  
It would be great if the "usually expected" behavior would be set up as simple as possible.  
  
For me, a good default would be that all processes end when the parent (that started them) ends. I think this would be a useful default for many users. Any other behavior seem it could be quite unexpected, and possibly hurtful to users.

---

## Comment 9

> Username: egorpugin  
> Created at: 2022-06-12 10:45:28 UTC  
> Url: https://github.com/boostorg/process/issues/259#issuecomment-1153126326  

> For me, a good default would be that all processes end when the parent (that started them) ends.   
  
libuv uses that.

---

## Comment 10

> Username: klemens-morgenstern  
> Created at: 2022-06-13 06:31:13 UTC  
> Url: https://github.com/boostorg/process/issues/259#issuecomment-1153525886  

But then you'd detached from the current process everytime. So if you process gets put in a group by it's caller, and then puts it's child processed in a different group, you'd get weird behaviour for other users. That's why I usually stick to the OS defaults.  
  
Have you guys only used groups to avoid zombies & terminating? Of what utility are `group.wait` functions?

---

## Comment 11

> Username: klemens-morgenstern  
> Created at: 2022-09-20 05:17:31 UTC  
> Url: https://github.com/boostorg/process/issues/259#issuecomment-1251851463  

I've been working trying to figure out how to make job-object on windows work, with little success. I think I'll tall it, and just provide pgid initializers for posix. The posix API is easy enough to use alone, and the windows job object api is to messy to be useful I fear.  
  
is anyone using the the windows stuff?

---

## Comment 12

> Username: kannon92  
> Created at: 2022-09-20 11:05:44 UTC  
> Url: https://github.com/boostorg/process/issues/259#issuecomment-1252193280  

> I've been working trying to figure out how to make job-object on windows work, with little success. I think I'll tall it, and just provide pgid initializers for posix. The posix API is easy enough to use alone, and the windows job object api is to messy to be useful I fear.  
>   
>   
>   
> is anyone using the the windows stuff?  
  
Sorry for not responding. I no longer work for the employer that was using this.  We were primarily interested in boost process because of windows and Linux support.  We were aiming to support this in both OS and did test it in both actually.    
  
So windows was used ;) our initial rollout of the project was completely focused on windows

---

## Comment 13

> Username: fsmoke  
> Created at: 2025-04-11 14:05:57 UTC  
> Url: https://github.com/boostorg/process/issues/259#issuecomment-2797016887  

What status of groups support in V2? last comment on 2022, now already 2025 - my team still cannot migrate to v2 due this issue

---

## Comment 14

> Username: klemens-morgenstern  
> Created at: 2025-04-11 15:51:23 UTC  
> Url: https://github.com/boostorg/process/issues/259#issuecomment-2797315250  

Not supported.
