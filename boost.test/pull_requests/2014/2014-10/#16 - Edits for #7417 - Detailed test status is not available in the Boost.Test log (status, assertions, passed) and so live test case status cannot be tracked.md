# #16 Edits for #7417  - Detailed test status is not available in the Boost.Test log (status, assertions, passed) and so live test case status cannot be tracked [Closed]

> Username: ja11sop  
> Created at: 2014-10-18 00:32:08 UTC  
> Updated at: 2017-03-18 13:42:06 UTC  
> Closed at: 2017-03-18 13:42:05 UTC  
> Url: https://github.com/boostorg/test/pull/16  

Pull request for https://svn.boost.org/trac/boost/ticket/7417. Detailed test status is not available in the Boost.Test log (status, assertions, passed) and so live test case status cannot be tracked, or processed by other tools such as https://github.com/ja11sop/cuppa. Please see the referenced ticket #7417 for full details.

---

## Comment 1

> Username: ja11sop  
> Created_at: 2015-01-11 19:42:19 UTC  
> Url: https://github.com/boostorg/test/pull/16#issuecomment-69507949  

To make this pull request easier to understand I'm adding an expanded description here from the original trac ticket.  
  
Currently with Boost.Test it is not possible to use the test output to show detailed live test progress. By detailed I mean, test status, number of passed assertions and total number of assertions. This is because:  
1. Only basic status information is provided through`stdout` (the Boost.Test log) as tests are proceeding. _You only know whether a test has started or a test has finished_.  
2. Detailed information is accessible by accessing the test report information captured as part of the Boost.Test report functionality which is typical sent to `stderr`.  
3. **However** Report data is **only output when all tests are complete** therefore this information cannot be used for live reporting. In other words a solution is not to listen on both `stdout` and `stderr`   
  
The attached patch _adds additional test status information to the information sent to_ `stdout` - the Boost.Test log.  
  
This allows arbitrary test tools to be developed that interpret the test output for live test progress reporting. One such tool is [cuppa](https://pypi.python.org/pypi/cuppa) which reads this additional info and prints live test progress to the console.  
  
Example output for a test suite with two test cases (one with assertions and one without) is:  
  
```  
Running 2 test cases...  
(0): Entering test suite "Master Test Suite"  
my_test.cpp(211): Entering test suite "my_test_suite"  
my_test.cpp(213): Entering test case "test_case_1"  
Leaving test case "test_case_1"; testing time: 0.000124946s wall, 0.000000000s user + 0.000000000s system = 0.000000000s CPU (n/a%). Test case passed. 8 assertions out of 8 passed.  
my_test.cpp(214): Entering test case "test_case_2"  
Test case test_case_2 did not check any assertions  
Leaving test case "test_case_2"; testing time: 0.000129975s wall, 0.000000000s user + 0.000000000s system = 0.000000000s CPU (n/a%). Test case passed.  
Leaving test suite "my_test_suite". Test suite passed. 8 assertions out of 8 passed. 2 test cases out of 2 passed.  
Leaving test suite "Master Test Suite". Test suite passed. 8 assertions out of 8 passed. 2 test cases out of 2 passed.  
```  
  
Compare this with the current output, **which has no test result details**:  
  
```  
Running 2 test cases...  
(0): Entering test suite "Master Test Suite"  
my_test.cpp(211): Entering test suite "my_test_suite"  
my_test.cpp(213): Entering test case "test_case_1"  
Leaving test case "test_case_1"; testing time: 0.000124946s wall, 0.000000000s user + 0.000000000s system = 0.000000000s CPU (n/a%).  
my_test.cpp(214): Entering test case "test_case_2"  
Test case test_case_2 did not check any assertions  
Leaving test case "test_case_2"; testing time: 0.000129975s wall, 0.000000000s user + 0.000000000s system = 0.000000000s CPU (n/a%).  
Leaving test suite "my_test_suite".   
Leaving test suite "Master Test Suite".  
```  
  
These changes have no impact on existing tests and simply provide additional information that is already known, but not displayed.

---

## Comment 2

> Username: rogeeff  
> Created_at: 2015-01-12 20:09:51 UTC  
> Url: https://github.com/boostorg/test/pull/16#issuecomment-69636661  

This change needs to be discussed. There are number of things to consider. Here some thoughts on the subject:  
1. This is not going to be part of the first release with new docs  
2. I think output you propose is too verbose.   
3. Main audience for the HRF is human. Auto tools should use XML. Is there any reason why they don't?  
4. Overall I'd rather came up with new color coded HRF output. We only really need green PASSED and RED failed for test units. Human are not interested in other details until the very end (this is along the lines with what gtest does).  
5. XML output can be as verbose as we want and does not need coloring  
6. We already have --color option. We should combine it with new modification for the output

---

## Comment 3

> Username: ja11sop  
> Created_at: 2015-01-12 22:53:50 UTC  
> Url: https://github.com/boostorg/test/pull/16#issuecomment-69663348  

Hi Gennadiy, Thanks for looking at this. Let me try to address your points.  
  
All this information is already available in the test reports that we receive at the end. We do not have this available while tests are being processed. XML is not suitable for processing on the fly while tests are being run. The HRF output provides this opportunity.  
  
This use-case is particularly important for long running tests. That 1 out of 2,000,000 assertions failed as opposed to 1,900,000 assertions failed is important to our developers as much as if it just passed or failed.  
  
I see two concerns here:  
  
First making the output generally useful on the fly to direct users. Pretty and coloured output has value here. I don't have any opinions on that as we always use tools to interpret the output. Either inside an IDE like Codeblocks, or using something like Scons via cuppa. XML cannot be used in those cases (and not on the fly besides anyway). It is this second case interests me the most and the subject of my pull request.  
  
The second case is focused on providing all test information on completion as a single event that can be interpreted by human or script (we use simple regexes). Another very important use case for this is when running an application in diagnostic mode and making use of boost test output (and test tools in the code). That's the subject of my final pull request #17.  
  
I believe I have adopted a minimalistic approach to this by adding a "test status" section to the `Leaving test <type>". More less the basic format is:  
  
```  
Test <type> <result>. <x> out of <y> <test_event> passed.  
```  
  
In my mind this information should have been then in the first place, however I accept that some may find that verbose, as indeed some may find the Boost.Timer output verbose. If there is a firm belief that it is _too_ verbose then my preference here would be to allow the suppression of the information through a runtime option (or conversely make it available by specifying an option so have it not shown by default). For example `--verbose_hrf_output`.  
  
To get the output that has just enough info to track the tests, with the patch applied I specify this on the command line:  
  
```  
--log_format=hrf --log_level=test_suite  
```  
  
so adding an extra `--verbose_hrf_output` (or similar option) is no problem. Re-writing the regexes and processing logic would be a lot more problematic.  
  
Having said that I'd be interested in how you might make the test status output less verbose without losing information. I am not sure there is much that can be removed. I need the information there and wanted to keep it somewhat human readable and so retained the basic phrasing such as `out of`, for example `4 out of 12 assertions passed`  
  
To summarise then - I want the information for processing on the fly. I see that as a separate effort to overhauling and prettifying the HRF output. I have a similar issue with cuppa. In that case I allow both 'raw' output coloured, prettified output. When being used from within an IDE raw output is desired so the IDE can regex match against the output.  
  
Fyi the attached image shows some sample output from cuppa that is making use of the additional information in the HRF output. Colour aside you can see the additional assertion and test case counts. With test failures you get something similar but with some red. To be clear though this is not about creating pretty output - this is about processing and tracking test progress on the fly. It so happens we also prettify it.  
  
![boost_test](https://cloud.githubusercontent.com/assets/933977/5712677/77f17af8-9aac-11e4-9cde-e80b23302be7.png)

---

## Comment 4

> Username: rogeeff  
> Created_at: 2015-01-12 23:44:27 UTC  
> Url: https://github.com/boostorg/test/pull/16#issuecomment-69669800  

I still believe that human and automated runner need two different formats.  
What if we come up with new log format: DXML - dynamic XML, where each  
message produced is going to be complete standalone XML message? This  
format can include as much information as one needs.  
  
As a side note: I am against introduction of some obscure CLAs targeting  
some specific use cases. I'd rather recognize more generic need and  
implement it.  
  
Gennadiy  
  
On Mon, Jan 12, 2015 at 5:53 PM, ja11sop notifications@github.com wrote:  
  
> Hi Gennadiy, Thanks for looking at this. Let me try to address your points.  
>   
> All this information is already available in the test reports that we  
> receive at the end. We do not have this available while tests are being  
> processed. XML is not suitable for processing on the fly while tests are  
> being run. The HRF output provides this opportunity.  
>   
> This use-case is particularly important for long running tests. That 1 out  
> of 2,000,000 assertions failed as opposed to 1,900,000 assertions failed is  
> important to our developers as much as if it just passed or failed.  
>   
> I see two concerns here:  
>   
> First making the output generally useful on the fly to direct users.  
> Pretty and coloured output has value here. I don't have any opinions on  
> that as we always use tools to interpret the output. Either inside an IDE  
> like Codeblocks, or using something like Scons via cuppa. XML cannot be  
> used in those cases (and not on the fly besides anyway). It is this second  
> case interests me the most and the subject of my pull request.  
>   
> The second case is focused on providing all test information on completion  
> as a single event that can be interpreted by human or script (we use simple  
> regexes). Another very important use case for this is when running an  
> application in diagnostic mode and making use of boost test output (and  
> test tools in the code). That's the subject of my final pull request #17  
> https://github.com/boostorg/test/pull/17.  
>   
> I believe I have adopted a minimalistic approach to this by adding a "test  
> status" section to the `Leaving test ". More less the basic format is:  
>   
> Test <type> <result>. <x> out of <y> <test_event> passed.  
>   
> In my mind this information should have been then in the first place,  
> however I accept that some may find that verbose, as indeed some may find  
> the Boost.Timer output verbose. If there is a firm belief that it is _too_  
> verbose then my preference here would be to allow the suppression of the  
> information through a runtime option (or conversely make it available by  
> specifying an option so have it not shown by default). For example  
> --verbose_hrf_output.  
>   
> To get the output that has just enough info to track the tests, with the  
> patch applied I specify this on the command line:  
>   
> --log_format=hrf --log_level=test_suite  
>   
> so adding an extra --verbose_hrf_output (or similar option) is no  
> problem. Re-writing the regexes and processing logic would be a lot more  
> problematic.  
>   
> Having said that I'd be interested in how you might make the test status  
> output less verbose without losing information. I am not sure there is much  
> that can be removed. I need the information there and wanted to keep it  
> somewhat human readable and so retained the basic phrasing such as out of,  
> for example 4 out of 12 assertions passed  
>   
> To summarise then - I want the information for processing on the fly. I  
> see that as a separate effort to overhauling and prettifying the HRF  
> output. I have a similar issue with cuppa. In that case I allow both 'raw'  
> output coloured, prettified output. When being used from within an IDE raw  
> output is desired so the IDE can regex match against the output.  
>   
> Fyi the attached image shows some sample output from cuppa that is making  
> use of the additional information in the HRF output. Colour aside you can  
> see the additional assertion and test case counts. With test failures you  
> get something similar but with some red. To be clear though this is not  
> about creating pretty output - this is about processing and tracking test  
> progress on the fly. It so happens we also prettify it.  
>   
> [image: boost_test]  
> https://cloud.githubusercontent.com/assets/933977/5712677/77f17af8-9aac-11e4-9cde-e80b23302be7.png  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/test/pull/16#issuecomment-69663348.  
  
##   
  
Gennadiy Rozental

---

## Comment 5

> Username: ja11sop  
> Created_at: 2015-01-14 01:40:17 UTC  
> Url: https://github.com/boostorg/test/pull/16#issuecomment-69855644  

(Sorry for the slow reply, pretty busy atm)  
Ok, I can see how it would be advantageous to have a separate, richer format. For the sake of exposition lets call it something like MRF meaning something like machine readable format. My command-line simply becomes:  
  
```  
--log_format=mrf --log_level=test_suite  
```  
  
My requirements for such a format would be that it would be easily to handle with unix-like command-line processing tools and say work fine with regexes and so on. Given the likely tools I would really not want to have XML as the format, but I think line delimited JSON would work well. It offers the same capabilities but with much easier handling. JSON is also pretty common these days and would retain a degree of human readability. Some even use it in logs.  
  
To explore how this could work (and see if I can easily update my toolchain to handle it) I'll update my pull-request to use a new format as you've suggested and try JSON as the format. I'll do some experimentation first and if I envisage any issues I'll ask for advice. I think you are right to want to use some data specific formatting as opposed to relying on carefully formatted text. It is more extensible and easier to manage.  
  
Jamie

---

## Comment 6

> Username: rogeeff  
> Created_at: 2015-01-14 19:15:23 UTC  
> Url: https://github.com/boostorg/test/pull/16#issuecomment-69973266  

XML also machine readable format. We want to emphasise the dynamic nature  
of this one, where each message is independent (format wise).  
  
On xml vs. json I am not big fan of later, but I can see the attraction. I  
think we can support both.  
On Jan 13, 2015 8:40 PM, "ja11sop" notifications@github.com wrote:  
  
> (Sorry for the slow reply, pretty busy atm)  
> Ok, I can see how it would be advantageous to have a separate, richer  
> format. For the sake of exposition lets call it something like MRF meaning  
> something like machine readable format. My command-line simply becomes:  
>   
> --log_format=mrf --log_level=test_suite  
>   
> My requirements for such a format would be that it would be easily to  
> handle with unix-like command-line processing tools and say work fine with  
> regexes and so on. Given the likely tools I would really not want to have  
> XML as the format, but I think line delimited JSON would work well. It  
> offers the same capabilities but with much easier handling. JSON is also  
> pretty common these days and would retain a degree of human readability.  
> Some even use it in logs.  
>   
> To explore how this could work (and see if I can easily update my  
> toolchain to handle it) I'll update my pull-request to use a new format as  
> you've suggested and try JSON as the format. I'll do some experimentation  
> first and if I envisage any issues I'll ask for advice. I think you are  
> right to want to use some data specific formatting as opposed to relying on  
> carefully formatted text. It is more extensible and easier to manage.  
>   
> Jamie  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/test/pull/16#issuecomment-69855644.

---

## Comment 7

> Username: ja11sop  
> Created_at: 2015-01-14 21:29:44 UTC  
> Url: https://github.com/boostorg/test/pull/16#issuecomment-69995589  

Agreed, but quite apart from that I believe XML to be a poor choice for this. It doesn't work well with command-line tools, scripting or regex-like searches. It also usually requires heavyweight tools to handle it and is brittle to parse.

---

## Comment 8

> Username: ja11sop  
> Created_at: 2015-01-14 22:12:04 UTC  
> Url: https://github.com/boostorg/test/pull/16#issuecomment-70002743  

Gennadiy - my apologies, the message that appears in github is missing your last remark (which I just saw in the email), namely:  
  
> On xml vs. json I am not big fan of later, but I can see the attraction. I  
> think we can support both.   
  
I could live with that.

---

## Comment 9

> Username: raffienficiaud  
> Created_at: 2017-03-18 13:42:05 UTC  
> Url: https://github.com/boostorg/test/pull/16#issuecomment-287547118  

Please see my last comments in https://svn.boost.org/trac/boost/ticket/7417 : we can now add programmatically a dedicated logger along with the other loggers. This will let you develop the logger you want and include it in your tests. We also implemented a JUnit logger which is well supported by a large set of tools.  
  
All in all, I am in favor of closing this PR and if needed consider a new one.

---
