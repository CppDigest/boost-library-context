# #665 Add cross_product example to documentation [Merged]

> Username: adityamohan29  
> Created at: 2020-02-08 06:44:41 UTC  
> Updated at: 2020-03-16 13:43:26 UTC  
> Merged at: 2020-03-16 13:43:26 UTC  
> Closed at: 2020-03-16 13:43:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/665  

Proposed above is the example of a cross product arithmetic operation involving different point concepts as operands. This aims to aid the user in understanding the concept more clearly when the user goes through the documentation.  
  
### References  
  
- #658

---

## Review 1 [Commented]

> Username: vissarion  
> Created_at: 2020-02-10 11:49:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/665#pullrequestreview-355874029  

📁 doc/src/examples/arithmetic/cross_product

```diff
   1 |+ 
```

> Username: vissarion  
> Created_at: 2020-02-10 11:45:15 UTC  
> Updated_at: 2020-03-16 13:01:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/665#discussion_r377014000  

please remove empty line

---

📁 doc/src/examples/arithmetic/cross_product

```diff
  45 |+      return 0;
  46 |+ 
  47 |+ }
```

> Username: vissarion  
> Created_at: 2020-02-10 11:47:02 UTC  
> Updated_at: 2020-03-16 13:01:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/665#discussion_r377014770  

please add the result of your example as in other BG examples

---

📁 doc/src/examples/arithmetic/cross_product

```diff
  30 |+      r1 = bg::cross_product(p1,p2);
  31 |+ 
  32 |+      std::cout << "Cross Product 1: "<< r1.get<0>() << std::endl;
```

> Username: vissarion  
> Created_at: 2020-02-10 11:49:20 UTC  
> Updated_at: 2020-03-16 13:01:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/665#discussion_r377015844  

in this case cross product is just the determinant. I do not know if this is the best case for example since it is a bit singular but in any case please add a comment about second coordinate of r1 that is undefined


---

## Review 2 [Commented]

> Username: vissarion  
> Created_at: 2020-02-10 13:34:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/665#pullrequestreview-355937754  

📁 doc/src/examples/arithmetic/cross_product

```diff
  11 |+ // Calculate the cross product of two points
  12 |+ 
  13 |+ 
```

> Username: vissarion  
> Created_at: 2020-02-10 13:34:03 UTC  
> Updated_at: 2020-03-16 13:01:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/665#discussion_r377063983  

you have to add doxygen commands in this file for the documentation to be generated. Have a look at how e.g. algorithms are documented. You may also consult this https://github.com/boostorg/geometry/wiki/Generating-and-Improving-Documentation


---

## Comment 3

> Username: adityamohan29  
> Created_at: 2020-02-12 12:34:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/665#issuecomment-585184914  

@vissarion Kindly verify the changes made.

---

## Comment 4

> Username: vissarion  
> Created_at: 2020-02-12 14:42:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/665#issuecomment-585237545  

Please verify that you can produce the documentation locally and the result is as expected i.e. you see your example under the `cross_product` documentation.  
  
You can also have a look at PR https://github.com/boostorg/geometry/pull/667

---

## Comment 5

> Username: adityamohan29  
> Created_at: 2020-02-29 10:23:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/665#issuecomment-592929924  

> Please verify that you can produce the documentation locally and the result is as expected i.e. you see your example under the `cross_product` documentation.  
>   
> You can also have a look at PR #667  
  
I apologize for closing this pull request prematurely. Ithought it was successful.   
If by "verifying that you can produce the documentation locally" you mean that if I navigate to geometry/doc/src/examples/ I can see the folder and file that I created ie, arithmetic/cross_product in my branch, then yes it is visible.

---

## Comment 6

> Username: digu-007  
> Created_at: 2020-02-29 10:54:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/665#issuecomment-592932460  

> Please verify that you can produce the documentation locally  
  
It means that you need to run `python make_qbk.py` and check locally in your system if the required `HTML` file is generated or not.  
  
You can take help from [readme.txt](https://github.com/boostorg/geometry/blob/develop/doc/readme.txt).

---

## Review 7 [Changes requested]

> Username: mloskot  
> Created_at: 2020-03-05 11:10:04 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/665#pullrequestreview-369481859  

@spacewafer `cross_product` should be named `cross_product.cpp`, no?  
  
FYI, next time you update your PR, then documentation and `doc/src/examples` will be built and verified with GitHub Actions.

---

## Comment 8

> Username: adityamohan29  
> Created_at: 2020-03-06 04:35:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/665#issuecomment-595595900  

![Screenshot from 2020-03-06 15-33-27](https://user-images.githubusercontent.com/32217637/76053041-eaa80d00-5f91-11ea-8241-749f1576f472.png)  
  
I know this might've been inquired about quite a few times but I couldn't get a proper solution to this error. Could someone help me out with this, please?

---

## Comment 9

> Username: mloskot  
> Created_at: 2020-03-06 08:49:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/665#issuecomment-595666044  

@spacewafer I have no idea, but even if I wanted to try to help you and ask around, I can't copy anything. Please, paste plain text next time (unless it is geometry drawing ;))

---

## Comment 10

> Username: digu-007  
> Created_at: 2020-03-06 08:52:42 UTC  
> Updated_at: 2020-03-06 08:54:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/665#issuecomment-595667063  

@spacewafer Refer to [this comment](https://github.com/boostorg/geometry/pull/667#issuecomment-584798757), seems like we had a similar problem.  
  
You can take help from [readme.txt](https://github.com/boostorg/geometry/blob/develop/doc/readme.txt) as well.

---

## Comment 11

> Username: mloskot  
> Created_at: 2020-03-06 08:54:41 UTC  
> Updated_at: 2020-03-06 11:14:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/665#issuecomment-595667789  

@digu-007 I completely forgot about similarity of the two errors. Thanks for recalling it.  
@spacewafer Please, try to re-do the setup as I explained in that comment @digu-007 linked.  
  
UPDATE: I asked about the problem on the lists https://lists.boost.org/boost-build/2020/03/30297.php and https://lists.boost.org/Archives/boost/2020/03/248331.php

---

## Comment 12

> Username: mloskot  
> Created_at: 2020-03-06 18:58:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/665#issuecomment-595911150  

@spacewafer Likely, you are using old `b2` installed from your Linux distro packages instead of `b2` built in the current Boost superproject tree, see https://lists.boost.org/Archives/boost/2020/03/248336.php Try running `~/boost/b2` instead.

---

## Comment 13

> Username: adityamohan29  
> Created_at: 2020-03-07 03:53:02 UTC  
> Updated_at: 2020-03-07 05:14:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/665#issuecomment-596042958  

Ahh yes, this must be it. Anyway I'll look into it in a while. I've got my  
university exams going on right now which will get over in three days. I'll  
get back to working on this on the 10th. Hope this would be fine.  
  
  
On Sat, 7 Mar, 2020, 12:28 AM Mateusz Łoskot, <notifications@github.com>  
wrote:  
  
> @spacewafer <https://github.com/spacewafer> Likely, you are using old b2  
> installed from your Linux distro packages instead of b2 built in the  
> current Boost superproject tree, see  
> https://lists.boost.org/Archives/boost/2020/03/248336.php Try running  
> ~/boost/b2 instead.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/geometry/pull/665?email_source=notifications&email_token=AHVZUJONXHE6RF753E7E353RGFBTVA5CNFSM4KRYV6F2YY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGOEOCOD3Q#issuecomment-595911150>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AHVZUJINT2YNOH4TPR4UFHDRGFBTVANCNFSM4KRYV6FQ>  
> .  
>

---

## Comment 14

> Username: mloskot  
> Created_at: 2020-03-07 09:08:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/665#issuecomment-596065088  

@spacewafer Sure, it's your PR, your pace. Good luck

---

## Comment 15

> Username: adityamohan29  
> Created_at: 2020-03-11 12:20:00 UTC  
> Updated_at: 2020-03-11 12:20:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/665#issuecomment-597600886  

I have solved the error that I encountered while executing the `b2` command. As @mloskot correctly pointed out, I had to execute `~/boost/b2` instead. I executed the `make_python.qbk` following that and everything seems fine.   
  
However, since I'm essentially creating these examples in a new folder "arithmetic" instead of the traditional "algorithms", do I have to make separate files in `doc/generated` for cross_product.qbk and dot_product.qbk? Right now all of these are encapsulated in arithmetic.qbk.

---

## Comment 16

> Username: digu-007  
> Created_at: 2020-03-11 12:41:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/665#issuecomment-597609639  

> However, since I'm essentially creating these examples in a new folder "arithmetic" instead of the traditional "algorithms"  
  
You are generating documentation for `cross_product`, it is supposed to be generated at `arithmetic`. Check this [page](https://www.boost.org/doc/libs/1_72_0/libs/geometry/doc/html/geometry/reference.html).  
  
Take a reference from how I did this, #667.

---

## Comment 17

> Username: adityamohan29  
> Created_at: 2020-03-11 15:08:30 UTC  
> Updated_at: 2020-03-12 03:13:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/665#issuecomment-597689106  

I'm encountering a few problems with this:  
I'm getting the following error after I updated the quickref.xml file after incorporating the necessary changes.  
![Screenshot from 2020-03-12 02-06-36](https://user-images.githubusercontent.com/32217637/76431900-3ca8c280-63d8-11ea-85af-ead98cfa019d.png)  
  
This is where I've updated the quickref.xml file   
![Screenshot from 2020-03-12 02-06-27](https://user-images.githubusercontent.com/32217637/76431996-5e09ae80-63d8-11ea-9906-9086b5f71f88.png)

---

## Comment 18

> Username: mloskot  
> Created_at: 2020-03-11 22:09:32 UTC  
> Updated_at: 2020-03-11 22:30:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/665#issuecomment-597902527  

@spacewafer I can not reproduce your problem when building docs for this PR as it is now.  
  
Could you clean the docs intermediate files, everything and try again? First, stash or commit all your changes!  
  
```  
$ cd libs/geometry  
$ git clean -xffd  
$ ../../b2 variant=release doc/src/docutils/tools/doxygen_xml2qbk  
  
# Put doxygen_xml2qbk somewhere in the PATH  
  
$ cd doc  
$ python make_qbk.py  
```  
  
By the way, you can see the complete sequence of commands in [.github/workflows/documentation.yml](https://github.com/boostorg/geometry/blob/develop/.github/workflows/documentation.yml).  
  
-----  
  
UPDATE: Well, I've completely forgotten that you should be able to do all, build documentation tools and generate `.qbk` and `.html` files, with just this  
  
```  
cd libs/geometry  
../../b2 doc  
```  
  
which I think should still work.

---

## Comment 19

> Username: adityamohan29  
> Created_at: 2020-03-12 21:26:47 UTC  
> Updated_at: 2020-03-13 04:18:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/665#issuecomment-598425640  

I've made all the necessary changes and produced the documentation locally and successfully. Since I encountered quite a few blocks on the way, I'd like to list out the steps I had taken that can help future contributions in order to provide examples for documentation:-  
  
1. Install Doxygen  
2. Download Quickbook and make the user-config.jam file in your home directory  
3. `cd boost/libs/geometry/doc`  
2. run ../../../b2 (or alternately /home/<username>/boost/b2)  
3. Export the `doxygen_xml2qbk` file(which will mostly be in boost/dist/bin) to your path (execute `export PATH=$PATH:/home/<username>/boost/dist/bin` )  
4. Create the required example file with appropriate Doxygen commands in `boost/libs/geometry/doc/src/examples` in the appropriate folder. (Make sure it compiles and runs properly)  
5. Refer your example in the .hpp file of the targeted algorithm (in `boost/libs/geometry/include/boost/geometry`)  
6. (Optional) In boost/libs/geometry/doc/reference include a .qbk file in the appropriate folder.  
7. Mention your example in the `imports.qbk` file  
8. Run `python make_qbk.py` in `boost/libs/geometry/doc`  
9. Load the html file from `boost/libs/geometry/doc/html/index.html`

---

## Comment 20

> Username: mloskot  
> Created_at: 2020-03-12 22:58:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/665#issuecomment-598468591  

@spacewafer   
Just two comments to your workflow outline:  
  
> 3. `cd boost/libs/geometry/doc`  
> 2. run ../../b2  
  
You mean, `../../../b2` as the program is in `boost/b2`.  
  
> 3. Export the b2 file(which will mostly be in boost/dist/bin) to your path (execute `export PATH=$PATH:/home/<username>/boost/dist/bin` )  
  
After you run `./bootstrap.sh`, you should get the `b2` coped to Boost root directory.

---

## Review 21 [Changes requested]

> Username: mloskot  
> Created_at: 2020-03-12 23:31:04 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/665#pullrequestreview-373963199  

I think this is good to merge as soon as the one tweak moving the example to the right overload is applied.  
  
There are some other tweaks that could be added, but I can do it myself later. For example, I'd add this   
  
```  
\qbk{distinguish,with different vector types}  
```  
  
to comment of   
  
```cpp  
template <typename ResultP, typename P1, typename P2>  
inline ResultP cross_product(P1 const& p1, P2 const& p2)  
```  
  
This will distinguish the two overloads in the table of content of the `reference/arithmetic.html`:  
  
![image](https://user-images.githubusercontent.com/80741/76576246-db5d1e00-64c1-11ea-89ba-2a20abd3aad6.png)  
  
This will also require to shuffle definitions of the two `cross_product`  overloads, swapping their places so the one marked with `\qbk{distinguish,with different vector types}` occurs last.  
That I can do myself after this PR is merged.

📁 include/boost/geometry/arithmetic/cross_product.hpp

```diff
  95 |+ 
  96 |+ \qbk{[heading Examples]}
  97 |+ \qbk{[cross_product] [cross_product_output]}
```

> Username: mloskot  
> Created_at: 2020-03-12 23:27:51 UTC  
> Updated_at: 2020-03-16 13:01:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/665#discussion_r391957074  

AFAICT, your ` doc/src/examples/arithmetic/cross_product.cpp` passes homogeneous vector types, that is both arguments and returntype is `P`, it calls this function  
  
```cpp  
template <typename P>  
inline P cross_product(P const& p1, P const& p2)  
```  
  
I'd move these two lines pulling the example in to near that function, near line 119.

> Username: adityamohan29  
> Created_at: 2020-03-13 06:01:53 UTC  
> Updated_at: 2020-03-16 13:01:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/665#discussion_r392043945  

I've moved the lines.


---

## Comment 22

> Username: adityamohan29  
> Created_at: 2020-03-13 04:19:33 UTC  
> Updated_at: 2020-03-13 04:19:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/665#issuecomment-598540489  

> @spacewafer  
> Just two comments to your workflow outline:  
>   
> > 1. `cd boost/libs/geometry/doc`  
> > 2. run ../../b2  
>   
> You mean, `../../../b2` as the program is in `boost/b2`.  
>   
Yes, I'm sorry about that. I've updated the comment now.  
  
> > 1. Export the b2 file(which will mostly be in boost/dist/bin) to your path (execute `export PATH=$PATH:/home/<username>/boost/dist/bin` )  
>   
> After you run `./bootstrap.sh`, you should get the `b2` coped to Boost root directory.  
  
I meant the doxygen_xml2qbk file, not b2 , my bad. I've update it.

---

## Review 23 [Approved]

> Username: mloskot  
> Created_at: 2020-03-13 08:24:44 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/665#pullrequestreview-374117380  

This looks good to me and ready to merge.  
  
Once merged, I am ready to apply any tweaks and improvements if necessary.  
  
@vissarion since you also reviewed it previously, do you have any requests, any objects to merge?

---

## Review 24 [Commented]

> Username: vissarion  
> Created_at: 2020-03-13 08:51:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/665#pullrequestreview-374131942  

Looks ok to me, thanks!

---
