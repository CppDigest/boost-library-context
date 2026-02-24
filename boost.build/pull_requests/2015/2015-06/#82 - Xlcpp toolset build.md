# #82 Xlcpp toolset build [Merged]

> Username: camorton2  
> Created at: 2015-06-02 18:04:22 UTC  
> Updated at: 2021-10-02 22:20:26 UTC  
> Merged at: 2015-07-22 17:19:23 UTC  
> Closed at: 2015-07-22 17:19:23 UTC  
> Url: https://github.com/boostorg/build/pull/82  

creating a new toolset 'xlcpp' for new IBM compilers based on clang, leaving old toolset 'vacpp' for use with previous IBM compilers not based on clang

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2015-06-02 18:23:10 UTC  
> Url: https://github.com/boostorg/build/pull/82#issuecomment-108043301  

On Tue, Jun 2, 2015 at 1:04 PM, Catherine Morton notifications@github.com  
wrote:  
  
> creating a new toolset 'xlcpp' for new IBM compilers based on clang,  
> leaving old toolset 'vacpp' for use with previous IBM compilers not based  
> on clang  
>   
> What does it mean for it to be "based on clang"? If it means that it's just  
> an extended clang it would be better to make it a sub toolset of the  
> existing clang toolset?

---

## Comment 2

> Username: camorton2  
> Created_at: 2015-06-02 18:33:55 UTC  
> Url: https://github.com/boostorg/build/pull/82#issuecomment-108046824  

We use the clang front end, but its still an 'xlC' compiler

---

## Comment 3

> Username: camorton2  
> Created_at: 2015-06-02 18:45:47 UTC  
> Url: https://github.com/boostorg/build/pull/82#issuecomment-108050866  

I decided to setup a new toolset mainly because we don't support all of clang's features so I might have to change some things as we investigate test failures.

---

## Comment 4

> Username: grafikrobot  
> Created_at: 2015-06-02 18:53:29 UTC  
> Url: https://github.com/boostorg/build/pull/82#issuecomment-108053742  

I guess I'm OK with this.. But I'll let Volodya take a look since it's a  
significant enough addition & change. Also I'll add some separate line  
comments for some minor edits.

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2015-06-02 19:03:28 UTC  
> Updated_at: 2015-06-02 20:26:01 UTC  
> Url: https://github.com/boostorg/build/pull/82#discussion_r31558803  

You should update the copyrights to reflect the true authors, and dates.

---

## Comment 6

> Username: camorton2  
> Created_at: 2015-06-02 20:04:23 UTC  
> Updated_at: 2015-06-02 20:26:01 UTC  
> Url: https://github.com/boostorg/build/pull/82#discussion_r31564678  

Can I just remove those lines?   I notice clang.hpp has no copyright other than  
  
# Distributed under the Boost Software License, Version 1.0.  
  
# (See accompanying file LICENSE_1_0.txt  
  
# or copy at http://www.boost.org/LICENSE_1_0.txt)  
  
I just copied and updated from vacpp.hpp and I have no idea who the original author is for that file.

---

## Comment 7

> Username: grafikrobot  
> Created_at: 2015-06-02 20:14:13 UTC  
> Updated_at: 2015-06-02 20:26:01 UTC  
> Url: https://github.com/boostorg/build/pull/82#discussion_r31565648  

It needs to have some for of copyright.. To keep lawyers happy. If it's a mostly copied file, just keep the original copyrights and add a line for yourself to reflect your contribution.

---

## Comment 8

> Username: camorton2  
> Created_at: 2015-07-22 13:10:44 UTC  
> Url: https://github.com/boostorg/build/pull/82#issuecomment-123714841  

Is there any further status on this request?  We would like to start using the new toolset for regular runs.

---

## Comment 9

> Username: eldiener  
> Created_at: 2015-07-22 16:08:36 UTC  
> Url: https://github.com/boostorg/build/pull/82#issuecomment-123774069  

Is it possible to test this toolset out on Linux ? I applied the PR on Boost build on Windows but I could see from your changes it is not run there. But after running booststrap.bat to sync up Boost build versions,  I was able to run Boost build with my normal Windows toolsets ( gcc, clang, and vc++ ) without any problems. But to really test that this toolset actually works on Linux with the IBM compiler using clang it would be best if I could test it with the appropriate compiler. Is there a developer ( free ) version of the compiler I can test with on the IBM website ?

---

## Comment 10

> Username: camorton2  
> Created_at: 2015-07-22 17:01:02 UTC  
> Url: https://github.com/boostorg/build/pull/82#issuecomment-123792290  

I tested it on linux with xlC

---

## Comment 11

> Username: eldiener  
> Created_at: 2015-07-22 17:58:26 UTC  
> Url: https://github.com/boostorg/build/pull/82#issuecomment-123809141  

My point is that, while you can test it, if the developers of Boost libraries cannot test the compiler none of the Boost libraries are going to pay any attention to the compiler. If IBM expects Boost library developers to pay for a license of xlc I think they are in for a rude awakening. And without being able to test the compiler I highly doubt that any Boost library developer is going to approve of a library change because of xlc, whether you say that the change is necessary or not. Of course I could be wrong about other Boost developers and we do try to support compilers which are very difficult to be able to test with.  
  
I talked online to someone from IBM about getting a free license for Boost developers to test the compiler on Linux. The answer I received is that while the person i was talking to online dealt with licenses he/she did not deal with any free licenses, if they exist. It was suggested to by the person I was chatting with online that I contact you as an IBM developer. If you are not an employee of IBM I do apologize.

---

## Comment 12

> Username: camorton2  
> Created_at: 2015-07-22 20:49:10 UTC  
> Url: https://github.com/boostorg/build/pull/82#issuecomment-123857561  

In the recent past we have done regular testing and published the results on the trunk summary and we would like to start publishing our test results again with regular runs.  We have successfully submitted changes in the past (there's already a vacpp toolset), so I was hoping we could at least try rather than having to ship a separate patch when changes are required.  
Changes in the past have been submitted by Chris Cambly or Michael Wong.

---

## Comment 13

> Username: eldiener  
> Created_at: 2015-07-22 21:17:06 UTC  
> Url: https://github.com/boostorg/build/pull/82#issuecomment-123867069  

What do you mean by "submitted changes in the past" ? Do you mean you have created patches or pull requests against Boost libraries ?

---

## Comment 14

> Username: camorton2  
> Created_at: 2015-07-22 22:20:58 UTC  
> Url: https://github.com/boostorg/build/pull/82#issuecomment-123887379  

We have done both.   Traditionally we maintain patches for our customers to download and apply to a specific version of boost, but we have also created pull requests to get some changes put back into boost trunk.

---

## Comment 15

> Username: eldiener  
> Created_at: 2015-07-22 22:47:38 UTC  
> Url: https://github.com/boostorg/build/pull/82#issuecomment-123895887  

What I am saying is that if you provide pull requests on some Boost library, let's call it  'XXX', in order for that library to work correctly with your IBM compiler it is only natural for the maintainer of library XXX  to want to test your pull request with your IBM compiler before merging the pull request with the rest of XXX's code. Can you understand that ? If your IBM compiler is not available for the maintainer of XXX he must take it on spec that your pull request is correct. With all due respect to your own programming abilities, as well as those of your fellow IBM developers, Chris Cambly or Michael Wong, Boost developers do not in general like to rely on others saying that some change to their library is correct without being able to test that this is so. This is why I would like to suggest to you, as a representative of IBM, that it would be advantageous to your IBM compilers if Boost maintainers were able to obtain a free version of your compilers, purely for non-commerical use in testing their libraries, in order to know that any changes to their library made to accomodate your compilers are validated by testing them out.

---

## Comment 16

> Username: camorton2  
> Created_at: 2015-07-22 22:59:14 UTC  
> Url: https://github.com/boostorg/build/pull/82#issuecomment-123898619  

Sure I understand, its a debate that has been going on for some time.

---

## Comment 17

> Username: eldiener  
> Created_at: 2015-07-22 23:10:13 UTC  
> Url: https://github.com/boostorg/build/pull/82#issuecomment-123900757  

Then do not be surprised, or take it personally, if you submit a pull request or bug report for some Boost library pertaining to a change you want made to accommodate your IBM compiler(s), if the PR or bug report is ignored because it is difficult or onerous for the maintainer of that library to test the change. I do understand for xlcpp that you allow a 60-day trial, but that is not convenient for a Boost library developer to continually have to use, even if it were possible to do so.  
  
Nonetheless thanks for contributing regression tests to Boost for your IBM compilers.

---

## Comment 18

> Username: camorton2  
> Created_at: 2015-07-23 16:09:28 UTC  
> Url: https://github.com/boostorg/build/pull/82#issuecomment-124153636  

I've pointed out this discussion to Michael Wong.   I believe he was involved with similar discussions in the past and might provide some insight if any progress was made.

---
