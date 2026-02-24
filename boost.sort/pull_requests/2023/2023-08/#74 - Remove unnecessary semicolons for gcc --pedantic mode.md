# #74 Remove unnecessary semicolons for gcc --pedantic mode [Merged]

> Username: nigels-com  
> Created at: 2023-08-26 07:02:17 UTC  
> Updated at: 2024-08-31 18:50:21 UTC  
> Merged at: 2024-08-31 18:50:21 UTC  
> Closed at: 2024-08-31 18:50:21 UTC  
> Url: https://github.com/boostorg/sort/pull/74  

Quite interested in using block_indirect_sort for a current project that concerns rather large arrays.  
  
However our organisation has some conventions (policies?) about compiler flags which includes gcc --pedantic.  
And since boost::sort is templated we'd need to touch various modules to relax the compiler flags in various ways.  
  
```  
$ g++ --version  
g++ (Ubuntu 11.4.0-1ubuntu1~22.04) 11.4.0  
Copyright (C) 2021 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
```  
  
```  
$ g++ test_block_indirect_sort.cpp -I $(pwd)/../include --pedantic  
In file included from /home/nigels/dev/sort/test/../include/boost/sort/insert_sort/insert_sort.hpp:21,  
                 from /home/nigels/dev/sort/test/../include/boost/sort/spinsort/spinsort.hpp:25,  
                 from /home/nigels/dev/sort/test/../include/boost/sort/sort.hpp:17,  
                 from test_block_indirect_sort.cpp:22:  
/home/nigels/dev/sort/test/../include/boost/sort/common/util/traits.hpp:115:6: warning: extra ‘;’ [-Wpedantic]  
  115 |     };  
      |      ^  
      |      -  
In file included from /home/nigels/dev/sort/test/../include/boost/sort/block_indirect_sort/blk_detail/backbone.hpp:25,  
                 from /home/nigels/dev/sort/test/../include/boost/sort/block_indirect_sort/blk_detail/merge_blocks.hpp:22,  
                 from /home/nigels/dev/sort/test/../include/boost/sort/block_indirect_sort/block_indirect_sort.hpp:22,  
                 from /home/nigels/dev/sort/test/../include/boost/sort/sort.hpp:20,  
                 from test_block_indirect_sort.cpp:22:  
/home/nigels/dev/sort/test/../include/boost/sort/common/stack_cnc.hpp:102:6: warning: extra ‘;’ [-Wpedantic]  
  102 |     };  
      |      ^  
      |      -  
/home/nigels/dev/sort/test/../include/boost/sort/common/stack_cnc.hpp:135:6: warning: extra ‘;’ [-Wpedantic]  
  135 |     };  
      |      ^  
      |      -  
```  
  
So I took a bit of a quick look at the block_indirect_sort and what it might require to make `gcc --pedantic` happy.  
In my understanding semicolons are needed after a lambda function, struct or class, but not for if/while/for blocks, functions or namespaces.  
  
It does seem that other code linters may well also fuss over this and while the extra semicolons seem harmless to me, they're not what I'm used to looking at, to be honest.  
  
So as a conversation starter with boost::sort maintainers, is this sort of change something likely to be accepted as a contribution?  I'm open to covering all the headers, but this initial effort seemed like enough to reduce some tooling friction for block_indirect_sort.

---

## Comment 1

> Username: nigels-com  
> Created_at: 2023-08-26 07:05:51 UTC  
> Url: https://github.com/boostorg/sort/pull/74#issuecomment-1694205147  

Just to mention, the other two more obvious issues are `-Wshadow` and `-fno-operator-names` which raise a similar question.  Is there an inclination to appease these compiler flags?

---

## Comment 2

> Username: fjtapia  
> Created_at: 2023-09-04 18:01:14 UTC  
> Url: https://github.com/boostorg/sort/pull/74#issuecomment-1705585811  

Sorry for the delay in replying. I've been away on vacation out of my house.  
  
I have been looking for the error that appears in continuous integration  
when I upload a file.  
It's very strange:  
  
  
   1.  The version of the OS and the compiler where it fails are Ubuntu  
   16.04 and gcc 5.4. The current code has been in the repository for several  
   years with minimal modifications and no problems have ever appeared. Why do  
   they appear now?  
   2. I have tested it with the latest versions of GCC 13.2 and CLANG 16.0.  
   The latest versions are usually better at catching bugs and problems. They  
   haven't found anything. I have compiled it in debug mode and in release  
   mode, and I have executed them. Nothing has appeared  
   3. I have done massive stress tests running thousands of cases with  
   everything I could think of. They haven't found anything  
   4.  The static code analyzers, including the GCC analyzer option, have  
   not found anything.  
   5. I have downloaded an image of Xubuntu 16.04 with gcc 5.4. I have  
   compiled and run it and nothing has appeared.  
  
  
When I solve all this, I will recompile everything with the pedantic option  
to eliminate the extra semicolons and any warnings that appear. When I have  
it, I'll upload everything to the repository  
  
If anyone has any ideas or suggestions, they are welcome, because I don't  
know where to look.  
Thanks in advance  
  
Francisco  
  
El lun, 4 sept 2023 a las 13:13, spreadsort ***@***.***>)  
escribió:  
  
> Assigned #74 <https://github.com/boostorg/sort/pull/74> to @fjtapia  
> <https://github.com/fjtapia>.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/pull/74#event-10272789120>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GAFVHNHNT7ZFEP4TJLXYWZVVANCNFSM6AAAAAA37LLC74>  
> .  
> You are receiving this because you were assigned.Message ID:  
> ***@***.***>  
>

---

## Comment 3

> Username: nigels-com  
> Created_at: 2023-09-04 20:52:12 UTC  
> Url: https://github.com/boostorg/sort/pull/74#issuecomment-1705691961  

Hello @fjtapia.  Thanks for block_indirect_sort, it's doing a great job for what we need.  
  
I'm not familiar with the github CI, but it looks like it's Mac OS X that is failing to build, not Ubuntu.  
My guess is that the CI needs to be migrated to a newer version of Mac OS X, one that is supported by github.  
I do happen to have an Intel Mac I can try for this, if that's helpful.  
  
![Screenshot_20230905_064502](https://github.com/boostorg/sort/assets/545677/49f47652-f619-47bf-be79-f5e14a96653c)

---

## Comment 4

> Username: nigels-com  
> Created_at: 2023-09-04 20:53:53 UTC  
> Url: https://github.com/boostorg/sort/pull/74#issuecomment-1705692767  

And to note that macOS Catalina (version 10.15) went out of support in November 2022.  
https://en.wikipedia.org/wiki/MacOS_Catalina

---

## Comment 5

> Username: fjtapia  
> Created_at: 2023-09-04 22:44:11 UTC  
> Url: https://github.com/boostorg/sort/pull/74#issuecomment-1705746538  

Thanks, Nigel for your message. I've been paranoid for several days looking  
for where the error could be.  
  
About block_indirect_sort (BIS), I am writing a new version that  
significantly reduces the number of comparisons needed, and that  
parallelizes the few parts that in the current version are done with 1  
thread. Thinking especially about very large data sets, with a large number  
of threads.  
  
I have also devised and implemented a new parallel stable algorithm, which  
does not yet have a name. It is based on fixed-length blocks like BIS, and  
the memory it uses is similar to this one.  
In the Sort library, we have the sample_sort algorithm which is an  
excellent stable parallel algorithm, but it requires additional memory of  
the same size as the data. This new algorithm takes about half as much. I  
hope it's quick, but until I do the final tests I won't know for sure.  
  
To compete with BIS, I have devised another parallel non-stable algorithm,  
using completely different methods. It doesn't have a name yet, but I hope  
it's as fast or faster than BIS.  
  
I'm rewriting all the code from scratch to make it smaller, simpler,  
clearer, and much easier to understand for anyone who tries it.  
  
Some time ago I devised an algorithm to sort data with many machines, like  
Hadoop's Terasort. The few algorithms I have been able to see are based on  
sample_sort, which needs additional memory the same size as the data, so  
machines can use at most 40% or 45% of the memory for the data.  
My algorithm is based on block_indirect_sort, so machines can use 90% or  
95% of memory for data. Machines and networks can be heterogeneous, and  
data moves only once through the network to reach its final destination.  
  
When I asked several friends about it, they told me that this is not a  
problem, that just adding more machines is enough. This may be logical if  
you want to order 10 terabytes, but if what you have to order is 10  
petabytes, the difference begins to be significant.  
  
This, together with the rapid creation of indexes ordered by different  
concepts, allows many Big Data operations to be accelerated, reducing the  
number of machines needed.  
  
But this algorithm is something I can't do by myself. Right now my team is  
me, my workplace is a table in my house and my HW is the computer that is  
on my table. I know the problems of a machine and imagine them, but the  
problems of a cluster of computers are beyond my reach. A multitude of  
problems may arise that do not even cross my mind right now.  
  
When I have it ready if the results are as expected, I will propose it to  
be part of the Sort library, and I will announce it to you in the email.  
  
Greetings, and thanks again for your message.  
  
Francisco  
  
El lun, 4 sept 2023 a las 22:54, Nigel Stewart ***@***.***>)  
escribió:  
  
> And to note that macOS Catalina (version 10.15) went out of support in  
> November 2022.  
> https://en.wikipedia.org/wiki/MacOS_Catalina  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/pull/74#issuecomment-1705692767>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GD6O7HDELCZ36OIY73XYY5WZANCNFSM6AAAAAA37LLC74>  
> .  
> You are receiving this because you were mentioned.Message ID:  
> ***@***.***>  
>

---

## Comment 6

> Username: nigels-com  
> Created_at: 2023-09-04 22:59:21 UTC  
> Url: https://github.com/boostorg/sort/pull/74#issuecomment-1705752645  

Francisco,  
  
I have some familiarity with orchestrating larger scale compute using AWS EC2 (elastic Linux instances) and S3 (storage).  
So the good news is that you wouldn't need to build your own cluster, and there is a fair variety of instances available such as tiny ARM and multi-core with plentiful RAM.  Usage charges can add up, however.  
  
Our situation is somewhat memory constrained, the block-based aspect of block_indirect_sort makes it a winner for our application, but if we knew we had the headroom, would be happy to go faster still.  
  
-- Nigel

---

## Comment 7

> Username: fjtapia  
> Created_at: 2023-09-06 15:34:47 UTC  
> Url: https://github.com/boostorg/sort/pull/74#issuecomment-1708623544  

Changes of the pedantic option uploaded  
  
El mar, 5 sept 2023 a las 0:59, Nigel Stewart ***@***.***>)  
escribió:  
  
> Francisco,  
>  
> I have some familiarity with orchestrating larger scale compute using AWS  
> EC2 (elastic Linux instances) and S3 (storage).  
> So the good news is that you wouldn't need to build your own cluster, and  
> there is a fair variety of instances available such as tiny ARM and  
> multi-core with plentiful RAM. Usage charges can add up, however.  
>  
> Our situation is somewhat memory constrained, the block-based aspect of  
> block_indirect_sort makes it a winner for our application, but if we knew  
> we had the headroom, would be happy to go faster still.  
>  
> -- Nigel  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/pull/74#issuecomment-1705752645>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GBFQZYLESFQHAKR2BDXYZMNHANCNFSM6AAAAAA37LLC74>  
> .  
> You are receiving this because you were mentioned.Message ID:  
> ***@***.***>  
>

---

## Comment 8

> Username: nigels-com  
> Created_at: 2023-10-23 23:29:53 UTC  
> Url: https://github.com/boostorg/sort/pull/74#issuecomment-1776210219  

Francisco,  
  
I reviewed all these trivial changes again just now.  
Is there anything further needed to have these merged in?  
  
-- Nigel Stewart

---

## Comment 9

> Username: nigels-com  
> Created_at: 2024-08-25 03:27:50 UTC  
> Url: https://github.com/boostorg/sort/pull/74#issuecomment-2308638658  

Re-based again just now.

---
