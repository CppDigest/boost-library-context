# #164 - On boost 1.73.0 for linux, I am unable to use group calls like terminate or wait. [Open]

> Username: kannon92  
> Created at: 2020-06-18 17:21:47 UTC  
> Updated at: 2020-08-24 14:06:51 UTC  
> Url: https://github.com/boostorg/process/issues/164  

Hello, I have noticed on linux I am unable to use group.terminate() or group.wait() with boost 1.73.0.  This does work on windows.     
~~~  
void TestGroupWait(const std::string& cmd1, const std::string& cmd2)  
{  
	bp::ipstream outStream; //reading pipe-stream  
	bp::ipstream errStream;  
	std::error_code ec;  
	std::error_code ec2;  
  
	boost::process::group grp;  
  
	bp::child childProc(cmd1, bp::std_out > outStream, bp::std_err > errStream, ec);  
	bp::child ChildProc2(cmd2, ec2);  
	grp.add(childProc, ec);  
	grp.add(ChildProc2, ec2);  
	grp.wait();  
	childProc.exit_code();  
	ChildProc2.exit_code();  
}  
void TestGroupKill(const std::string& cmd1, const std::string& cmd2)  
{  
	bp::ipstream outStream; //reading pipe-stream  
	bp::ipstream errStream;  
	std::error_code ec;  
	std::error_code ec2;  
  
	boost::process::group grp;  
  
	bp::child childProc(cmd1, bp::std_out > outStream, bp::std_err > errStream, ec);  
	bp::child ChildProc2(cmd2, ec2);  
	grp.add(childProc, ec);  
	grp.add(ChildProc2, ec2);  
	grp.terminate();  
}  
~~~  
  
When I call grp.terminate I get an exception:    
  
terminate called after throwing an instance of 'boost::process::process_error'  
  what():  killpg(2) failed in terminate: Invalid argument  
  
Its a similar error for grp.wait().    
  
If I put out the native_handle on group, I see that linux is returning -1 for the group handle.  I think that is why it is throwing.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2020-08-24 14:06:51 UTC  
> Url: https://github.com/boostorg/process/issues/164#issuecomment-679148679  

Yes, this is a bit weird on linux. You need to pass the group into the child constructor for the first child, i.e.   
  
```cpp  
bp::child childProc(cmd1, bp::std_out > outStream, bp::std_err > errStream, ec, grp);  
```
