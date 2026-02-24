# #343 - Future of Beast [Closed]

> Username: rberlich  
> Created at: 2017-05-06 23:09:17 UTC  
> Updated at: 2017-07-08 05:42:18 UTC  
> Closed at: 2017-05-24 22:53:54 UTC  
> Url: https://github.com/boostorg/beast/issues/343  

Hi Vinnie, in advance apologies for this question:  as far as I understand, Beast is scheduled for a review in Boost for this summer. From what I can see, about 50% of "Boost-candidates" are rejected. What future would you envisage for Beast, should this happen ? I'd be quite interested in making Beast a permanent feature of my application, which only has Boost as an external dependency, but am concerned that this might be a dead end. Again sorry and in any case thanks for the large amount of work you put into this project! Beet

---

## Comment 1

> Username: mjcaisse  
> Created at: 2017-05-07 00:29:25 UTC  
> Url: https://github.com/boostorg/beast/issues/343#issuecomment-299674310  

Obviously I can't answer for Vinnie on the Beast front; however, I would like to point out that nearly all submissions to Boost are rejected at first. We sometimes use nicer language at the end of the review -- "accepted with conditions". That is just another way of saying, "the library is rejected as-is".  
  
Some libraries take a long time to develop into a Boost thing. Initial rejection should probably be expected, though I doubt it will be in the vein of "this library doesn't belong in Boost". I am slightly biased (as the review manager for Beast), but it seems to me the type of library that would be good in Boost.  
  
I want to encourage you to participate in the review when it comes up. Boost is a community ran and driven organization. We need community involvement and reviews from individuals who think the technology is important and want to use it. Users are the best reviewers!  
  
(Now to see what Vinnie has to say)

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-05-07 04:45:59 UTC  
> Updated at: 2017-05-07 04:48:38 UTC  
> Url: https://github.com/boostorg/beast/issues/343#issuecomment-299682226  

Well that subject "Future of Beast" sure got my attention!  
  
You can be completely comfortable relying on Beast in your application, I'm going to continue refining the library and making it great. HTTP is pretty controversial, my expectations for the first Boost review are realistic. That is to say, I very much doubt it will be unconditionally accepted. But I will get incredibly valuable feedback on what people want to see and I will get right to work on addressing everyone's issues and resubmitting it again in 2017. I will be at the CppCon in Bellevue, Washington this year doing a one hour presentation on Beast.  
  
As @mjcaisse said, participating in the Boost review process is a win-win. Stakeholders get to weigh in, and I get to hear from users on how the library can be made better.  
  
But you don't have to wait until the formal review to contribute. I have just pushed an update to the README.md at the root of the repository, explaining that the best way to help is to perform code reviews (there are some links to resources teaching folks how to give great reviews, see https://github.com/vinniefalco/Beast/blob/master/README.md).  
  
What I am saying is that I need **your** (@rberlich) feedback as code is being changed and interfaces are being designed and updated! User feedback goes a _long_ way towards helping me shape the code that ultimately lands in everyone's applications. Currently there is quite a backlog of reviews since I left my previous employer to work on Beast full time. **1.0.0-b37** is in the pull request queue, I have already pushed a **1.0.0-b38**, and I'm adding to **1.0.0-b39**. You can see the **b37** pull request here: https://github.com/vinniefalco/Beast/pull/341  
  
And more code is coming! This will go into review after **b37** is merged:  
https://github.com/vinniefalco/Beast/commits/b38  
  
This is the branch I'm working on:  
https://github.com/vinniefalco/Beast/commits/b39  
  
I've been working furiously to beat this library into shape by July 1st when the formal review starts. I welcome everyone to comment on these pull requests, observe the development of new branches as they take place, and offer their thoughts on any subject no matter how big or how small. And I welcome questions about how the code works or why certain decisions were made, they really help me get a better perspective of what other people are seeing and how I can make things even better.  
  
tl;dr; I'm 100% invested in making sure this library gets into Boost and satisfies the needs of its users over time.

---

## Comment 3

> Username: rberlich  
> Created at: 2017-05-07 11:19:04 UTC  
> Url: https://github.com/boostorg/beast/issues/343#issuecomment-299699130  

@Michael and Vinnie:  Let me stress that I fully appreciate and understand the difficulties involved in creating a great (Open Source) library, and that I am full of respect for people who do. I also appreciate the role of Boost (being a user since 1.36 IIRC). It has always had the role of "filling the gaps" in the C++ standard and being the forerunner of new techniques, and since C++11 the focus has shifted to new areas. Boost.Beast would be a perfect example for a library that would fill a huge, gaping hole in the post C++11-world, beyond more low-level networking capabilities (the domain of ASIO in Boost). I come from an HPC background, and my own application is an (Open Source) library for distributed parametric optimization (think: creating more fuel-efficient cars using simulations). It is targeted at parallel and distributed environments (with evaluation-times for each candidate solution ranging from seconds to hours), and I have currently users running it on clusters with 400 Client nodes per server (and up to 10 servers) in particle physics. They are asking for 1000 clients, and I find myself spending far too much time on the networking and scalability part, while I'd love to concentrate on the optimization algorithms instead (some of which are in a bad shape). Being able to rely on a mature, peer-reviewed Websocket library under a Boost license (a key requirement I have) would be a huge benefit for my library. Hopefully this clarifies my (admittedly very direct) question, which was in no way meant as a criticism. I'll make sure to participate in the review and will try to "spread the word" in communities that I know are interested.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-05-07 12:39:04 UTC  
> Url: https://github.com/boostorg/beast/issues/343#issuecomment-299703754  

>Hopefully this clarifies my (admittedly very direct) question, which was in no way meant as a criticism.  
  
Its a good question and an important question I think, because C++ has been in sore need of network building blocks. We've been behind on this front for decades: no standardized sockets, no good asynchronous model, and the other networking libraries that are out there leave something to be desired (see http://vinniefalco.github.io/beast/beast/design/websocketpp.html).  
  
Its understandable to want to verify that Beast is not a "mirage in the desert." Getting asynchronous networking code right is a **hard** problem. I've been at it for quite a few years and I still have more learning to do. People need libraries that handle these details for them so they can focus on their domain specifics rather than getting bogged down on networking. Beast makes for a great beachhead, but even so we still need other things built on top of Beast such as a generalized server framework and utilities to perform common HTTP tasks. Some of this I will write myself as Beast example programs which users can copy (such as the WebSocket echo server), others I hope that people will write (using Beast).  
  
>I'll make sure to participate in the review and will try to "spread the word" in communities that I know are interested.  
  
Anything helps! And don't forget you can always leave comments in open pull requests such as this one:  
https://github.com/vinniefalco/Beast/pull/341  
  
Thank you for your interest in Beast!

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-05-24 22:53:54 UTC  
> Url: https://github.com/boostorg/beast/issues/343#issuecomment-303874750  

It looks like everything has been addressed here, so I'm going to go ahead and close it. But feel free to open a new issue any time you'd like!

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-07-08 05:42:18 UTC  
> Url: https://github.com/boostorg/beast/issues/343#issuecomment-313835774  

Quoting for posterity:  
  
> I would like to point out that nearly all submissions to Boost are rejected at first.
