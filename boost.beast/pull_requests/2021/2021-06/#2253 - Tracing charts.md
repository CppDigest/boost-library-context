# #2253 Tracing charts [Closed]

> Username: sdarwin  
> Created at: 2021-06-03 17:30:51 UTC  
> Updated at: 2021-06-10 19:46:26 UTC  
> Closed at: 2021-06-10 19:46:25 UTC  
> Url: https://github.com/boostorg/beast/pull/2253  

Testing out a new profiler. Don't merge.  
  
Some time ago, @madmongo1 suggested implementing the charts explained here:  
https://aras-p.info/blog/2019/01/16/time-trace-timeline-flame-chart-profiler-for-Clang/  
  
This has been configured with a jenkins job.    Include -ftime-trace flag:  
  
```  
using clang :   : clang++ : <stdlib>"libc++" <cxxflags>"-ftime-trace -Wno-c99-extensions" ;  
```  
  
And then compile:  
  
```  
./b2 --user-config=./user-config.jam \  
  toolset=clang \  
  asio.mode=dflt,nodep,nots,ts,nodep-nots,nodep-ts \  
  variant=release \  
  cxxstd=14 \  
  -j`grep processor /proc/cpuinfo | wc -l` \  
  -q \  
  libs/$REPONAME/example libs/$REPONAME/test  
```  
  
which generates tracing data in .json format for many source files.  
  
trace2html from https://chromium.googlesource.com/catapult is run on each .json file to generate a corresponding .html file.  
All the resulting pages are uploaded to Amazon S3.  
puppeteer (a nodejs module) takes a snapshot of one of the charts.  
Jenkins then posts the snapshot image and hyperlink to the charts in the pull request.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-06-03 18:14:40 UTC  
> Updated_at: 2021-06-03 21:01:31 UTC  
> Url: https://github.com/boostorg/beast/pull/2253#issuecomment-854078417  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2253?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2253](https://codecov.io/gh/boostorg/beast/pull/2253?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1235a3e) into [develop](https://codecov.io/gh/boostorg/beast/commit/30abfdb9f789534bc920ff9f09b7cf213243e989?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (30abfdb) will **decrease** coverage by `0.00%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2253/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2253?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2253      +/-   ##  
===========================================  
- Coverage    95.09%   95.08%   -0.01%       
===========================================  
  Files          153      153                
  Lines        11977    11977                
===========================================  
- Hits         11389    11388       -1       
- Misses         588      589       +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2253?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2253/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `96.42% <0.00%> (-0.90%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2253?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2253?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [30abfdb...1235a3e](https://codecov.io/gh/boostorg/beast/pull/2253?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2021-06-03 20:30:53 UTC  
> Url: https://github.com/boostorg/beast/pull/2253#issuecomment-854158303  

![pullrequest](https://2253.beast.tracing.cppalliance.org/pullrequest-d414fe09.png)  
Timeline tracing charts: [https://2253.beast.tracing.cppalliance.org/index.html](https://2253.beast.tracing.cppalliance.org/index.html)

---

## Comment 3

> Username: sdarwin  
> Created_at: 2021-06-10 19:46:25 UTC  
> Url: https://github.com/boostorg/beast/pull/2253#issuecomment-858981042  

- Both the .json and .html versions of the tracing files will be uploaded to S3.  If you'd like to view one or the other, switch the URL in the browser window (from .html to .json)  
  
- A lifecycle rule on AWS S3 will delete files after 6 months.   
  
- Around 1000 files are created, at a total size of approximately 9GB, which is fairly large.  Enough to crash Jenkins, until I increased heap memory settings.  
  
  
  
Testing complete for now. Closing pull request.

---
