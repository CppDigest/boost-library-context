# #5 [b2] support more than 64 parallel jobs (-j) [Merged]

> Username: jivancic  
> Created at: 2014-01-14 15:35:19 UTC  
> Updated at: 2021-10-02 22:35:43 UTC  
> Merged at: 2014-07-02 14:55:03 UTC  
> Closed at: 2014-07-02 14:55:03 UTC  
> Url: https://github.com/boostorg/build/pull/5  

Add support for more than 64 jobs on Windows. The current limitation was imposed (inherited) by WaitForMultipleObjects Windows API which does not allow waiting on more than 64 handles. This patch works around this issue in a way suggested by Microsoft (WFMO API documentation, remarks section). General idea is to create a thread to WFMO on subset of handles and then to use (outer) WFMO to wait for thread handles.  
  
There are some complications because WFMO is used with a timeout. To implement this, I used an event which notifies WFMO threads to abandon current wait. This way, once the result has been found or on timeout, all threads can be interrupted and joined.  
  
This patch does not change b2 behavior in case -j is 64 or less.

---
