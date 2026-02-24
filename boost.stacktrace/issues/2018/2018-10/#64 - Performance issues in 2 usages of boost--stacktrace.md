# #64 - Performance issues in 2 usages of boost::stacktrace [Closed]

> Username: dyadav7  
> Created at: 2018-10-25 05:29:35 UTC  
> Updated at: 2018-10-26 07:25:53 UTC  
> Closed at: 2018-10-26 07:25:18 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/64  

Hi  
  
I am seeing an performance issue in 2 usages of bookst::stacktrace.  
Source file:   backtrace.cpp  
Steps:             ./runme   (Compile the source file and run the executable to get time taken in dumping   
 stacktraces by different approaches)  
Attached :  fie.tar.gz containg abobe 2 files - backtrace.cpp and runme.  
  
The first approach below of using boost::stacktrace() below is 45 times faster.  
1)	cout << boost::stacktrace::stacktrace();  
takes : 0.02 Seconds  
2)	auto st = boost::stacktrace::stacktrace();  
for (auto it = st.begin(); it != st.end(); ++it) {  
                     cout << it->name() << " at "  << it->source_file() << ":" << it->source_line() << endl;  
       }  
      takes : 0.93 Seconds  
  
The real time is dumped by executing the above ‘runme’ script:  
bt_final_boost1 Time Taken (sec): 0.02  
bt_final_boost2 Time Taken (sec): 0.93  
  
Regards  
Deepak  
  
[file.tar.gz](https://github.com/boostorg/stacktrace/files/2513415/file.tar.gz)

---

## Comment 1

> Username: ArekPiekarz  
> Created at: 2018-10-25 20:11:36 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/64#issuecomment-433189025  

In the 2nd approach you are flushing the stream in each iteration of the loop by using "endl". Try changing it to "\n" or '\n' and see what happens.

---

## Comment 2

> Username: apolukhin  
> Created at: 2018-10-26 07:25:18 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/64#issuecomment-433313347  

The performance issue was recently fixed in https://github.com/boostorg/stacktrace/commit/4123beb4af6ff4e36769905b87c206da39190847  
  
Stacktrace in Boost 1.69 will be times faster.

---

## Comment 3

> Username: apolukhin  
> Created at: 2018-10-26 07:25:33 UTC  
> Updated at: 2018-10-26 07:25:53 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/64#issuecomment-433313414  

Many thanks for the bug report!
