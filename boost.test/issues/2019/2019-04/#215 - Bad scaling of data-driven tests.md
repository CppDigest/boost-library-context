# #215 - Bad scaling of data-driven tests [Open]

> Username: mgaunard  
> Created at: 2019-04-01 14:12:46 UTC  
> Updated at: 2019-04-02 08:53:45 UTC  
> Url: https://github.com/boostorg/test/issues/215  

A data-driven test covering an xrange of 1 million numbers generates 1 million xUnit XML reports.  
This brings most tooling to a crawl, in particular Jenkins.  
  
Each data point should not generate a new test case.  
The documentation suggests that this should scale well and that it is valid to make tests like this.  
https://www.boost.org/doc/libs/1_69_0/libs/test/doc/html/boost_test/tests_organization/test_cases/test_case_generation.html

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-04-01 18:43:16 UTC  
> Url: https://github.com/boostorg/test/issues/215#issuecomment-478697152  

Aside from the implementation issues, a data point IMO should be a test case by itself. We can think however of an aggregation of the results for reporting.  
  
What would be your take on this?

---

## Comment 2

> Username: mgaunard  
> Created at: 2019-04-01 21:45:50 UTC  
> Updated at: 2019-04-01 21:46:56 UTC  
> Url: https://github.com/boostorg/test/issues/215#issuecomment-478759705  

I ended up writing an XSLT 2.0 transformation to fold all of these.  
  
A setting to get that result from Boost.Test out of the box would still be nice.

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2019-04-02 07:33:11 UTC  
> Url: https://github.com/boostorg/test/issues/215#issuecomment-478879531  

I agree.   
I also think that the junit parsers one can find in tools like Jenkins are not really designed for getting millions of test cases.  
  
Would you please share the XSLT transformation? maybe relevant for the documentation.

---

## Comment 4

> Username: mgaunard  
> Created at: 2019-04-02 08:53:14 UTC  
> Updated at: 2019-04-02 08:53:45 UTC  
> Url: https://github.com/boostorg/test/issues/215#issuecomment-478905233  

I wrote it at work so unfortunately I cannot share it without getting through some hoops.  
But with XSLT 2.0+ it's quite simple, I just grouped on TestCase whose name starts with "_" and that have the same file and line, summed up the time spent, and just moved up all messages (info, error, exception, context, etc.) that could have been inside.
