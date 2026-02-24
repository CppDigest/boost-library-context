# #836 - Geometry is top 1 with 40G of testing artifacts [Closed]

> Username: Kojoley  
> Created at: 2021-03-28 01:41:42 UTC  
> Updated at: 2021-06-03 14:23:00 UTC  
> Closed at: 2021-05-12 11:15:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/836  

I was doing `boost-repo\status>..\b2 toolset=msvc-14.2 address-model=64 link=shared variant=debug` and was impressed with artifacts size, of total 119G, geometry is 40G.  
  
top 10:  
  
lib            | artifacts  
---------------|-----------  
geometry       | ~ 41 204 M  
math           | ~ 10 474 M  
test           | ~  6 139 M  
spirit         | ~  6 061 M  
thread         | ~  3 671 M  
multiprecision | ~  3 141 M  
hana           | ~  3 131 M  
numeric        | ~  3 089 M  
range          | ~  2 923 M  
phoenix        | ~  2 665 M  
  
geometry folder:  
folder     | size  
-----------|-----------  
test       | ~ 23 743 M  
index      | ~ 16 695 M  
extensions | ~783 803 K

---

## Comment 1

> Username: awulkiew  
> Created at: 2021-03-28 12:00:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/836#issuecomment-808886902  

Yes, AFAIR the library has the greatest number of lines of code of all Boost libraries and 94% test coverage.  
  
Do you think that something is wrong?

---

## Comment 2

> Username: Kojoley  
> Created at: 2021-03-28 14:18:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/836#issuecomment-808903262  

> Yes, AFAIR the library has the greatest number of lines of code of all Boost libraries  
  
That is impressive, I did not know that, but I am a little bit skeptical that it is 30% of the whole Boost code or same as 9 most massive libraries combined.  
  
> 94% test coverage  
  
Well, in my experience, test coverage for template code says literally nothing. When I tried collecting coverage statistics for Spirit it also said a number close to yours, but I know for sure it is just not.  
  
> Do you think that something is wrong?  
  
Marking some symbols (like TMP only stuff) to not have debug information should help a lot, I seen libc++ are doing that, but I do not know how to find most impactful symbols. Probably, forced optimization on functions will also help. I bet your users would be grateful for that too.  
  
top 10 objects file (whole Boost, but it is all from geometry, so I cut the filename prefix):  
  
size     | object  
---------|-------------------------------------------------------  
473072 K | test\cs_undefined\csundef_relops2.test\...\csundef_relops2.obj  
453233 K | test\cs_undefined\csundef_setops2.test\...\csundef_setops2.obj  
217125 K | test\cs_undefined\csundef_index.test\...\csundef_index.obj  
212077 K | test\algorithms\set_operations\difference\algorithms_difference_multi_spike.test\...\difference_multi_spike.obj  
194539 K | index\test\rtree\generated\b3d\rtree_drst_que_b3d.test\...\rtree_drst_que_b3d.obj  
191665 K | index\test\rtree\generated\b2d\rtree_drst_que_b2d.test\...\rtree_drst_que_b2d.obj  
188703 K | test\algorithms\set_operations\difference\algorithms_difference_multi.test\...\difference_multi.obj  
184698 K | test\algorithms\algorithms_is_valid.test\...\is_valid.obj  
177108 K | index\test\rtree\generated\b3d\rtree_rst_que_b3d.test\...\rtree_rst_que_b3d.obj  
174434 K | index\test\rtree\generated\b2d\rtree_rst_que_b2d.test\...\rtree_rst_que_b2d.obj

---

## Comment 3

> Username: barendgehrels  
> Created at: 2021-05-12 08:39:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/836#issuecomment-839584958  

It might be that we are top 1, but some library has to be number 1...  
We have many tests, and many tests use multiple types, this results in a lot of binaries with a large size.  
Can we close this issue?

---

## Comment 4

> Username: awulkiew  
> Created at: 2021-05-12 10:41:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/836#issuecomment-839668974  

@barendgehrels I think so. I'm against making the code even more complex by disabling debugging symbols in some of its parts. If the purpose is having smaller test objects then one could pass corresponding compiler flag and disable them altogether for the whole library.

---

## Comment 5

> Username: barendgehrels  
> Created at: 2021-05-12 11:15:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/836#issuecomment-839688976  

All right, agreed, closing.

---

## Comment 6

> Username: Kojoley  
> Created at: 2021-05-12 16:12:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/836#issuecomment-839908513  

> It might be that we are top 1, but some library has to be number 1...  
  
It is 4 times more than the top 2 library, and it is 30% of the whole Boost.  
  
Thanks for trying to do nothing.
