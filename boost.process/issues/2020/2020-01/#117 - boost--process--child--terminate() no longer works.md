# #117 - boost::process::child::terminate() no longer works [Open]

> Username: firewave  
> Created at: 2020-01-20 14:54:47 UTC  
> Updated at: 2020-05-21 09:08:33 UTC  
> Url: https://github.com/boostorg/process/issues/117  

This test will work with Boost 1.70+, but starting with 1.71 the process will still exist although `running()` says otherwise...  
  
```  
TEST(Process, test)  
{  
    boost::process::child c(boost::process::search_path("sleep"), "60");  
    EXPECT_TRUE(c.running());  
    const pid_t pid = c.id();  
    c.terminate();  
    c.wait();  
    EXPECT_FALSE(c.running());  
}  
```  
  
Adding `EXPECT_NE(0, kill(pid, 0));` to the test will highlight the issue.  
Also using `kill(pid, SIGKILL)` explicitly in place of `terminate()` will get rid of the process.  
  
The issue happens on CentOS 7, ubuntu 16.04 and ubuntu 19.10.

---

## Comment 1

> Username: jonesmz  
> Created at: 2020-05-21 09:08:32 UTC  
> Url: https://github.com/boostorg/process/issues/117#issuecomment-631975818  

@firewave   
  
Can you make a PR that enhances the existing unit tests to reproduce this?
