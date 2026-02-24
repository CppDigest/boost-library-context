# #396 - Very Long Directory Path [Closed]

> Username: teeks99  
> Created at: 2017-05-01 17:46:22 UTC  
> Updated at: 2020-07-20 09:13:29 UTC  
> Closed at: 2020-07-18 18:33:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/396  

In the geometry documentation there is a file with a 196 character path (from the root of a boost install):  
.\libs\geometry\doc\html\geometry\reference\spatial_indexes\boost__geometry__index__rtree\rtree_parameters_type_const____indexable_getter_const____value_equal_const____allocator_type_const___.html  
  
This isn't a problem in many installs. However on windows where there is a maximum path length limit of 256 characters, this doesn't leave a lot of breathing room for environments that don't put their boost install very near to the root of their drive.   
  
Could this path please be trimmed (<150 chars would be ideal) so that it isn't an issue?

---

## Comment 1

> Username: teeks99  
> Created at: 2017-05-01 17:56:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/396#issuecomment-298387513  

I did the initial look at the 1.60 version, and only noticed that single very long name. However in the 1.63 version there are a large number of files inside geometry with long paths, mostly within the doc\doxy\doxygen_output\html_by_doxygen directories, could these be shortened to doc\doxy\html? (and the xml output to doc\doxy\xml?)  
  
Also some of this seems to bleed over into doc\index\html_by_doxygen and doc\index\xml.

---

## Comment 2

> Username: mloskot  
> Created at: 2017-05-01 19:25:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/396#issuecomment-298409172  

Those files should not be included in release packages.  It was due to incomplete cleanup in docs generation process. It has been improved for 1.64. See #387

---

## Comment 3

> Username: garyfurnish  
> Created at: 2017-08-23 05:25:45 UTC  
> Updated at: 2017-08-23 05:30:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/396#issuecomment-324224675  

Something to consider is that I think this is also in practice an issue with running tests on windows.  This error doesn't seem to happen if I run it from the root directory...   
```  
d:\boost\libs\geometry\test>b2.exe address-model=64 -j8 > out.txt  
failed to write output file '..\..\..\bin.v2\libs\geometry\test\algorithms\relational_operations\disjoint\algorithms_disjoint_point_box_geometry.test\msvc-14.1\debug\address-model-64\asynch-exceptions-on\threading-multi\algorithms_disjoint_point_box_geometry.exe.rsp'!  
```

---

## Comment 4

> Username: mloskot  
> Created at: 2020-02-27 11:37:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/396#issuecomment-591926281  

@garyfurnish Yes, it is a common problem. The `b2 --abbreviate-paths` should help to workaround this.  
  
@teeks99 I'm closing this. Feel free to reopen if there is still a problem.

---

## Comment 5

> Username: mloskot  
> Created at: 2020-07-13 05:50:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/396#issuecomment-657372870  

Hmm, looks like the long paths issue is hitting the docs again, @teeks99 reported https://lists.boost.org/boost-users/2020/07/90536.php  
  
/cc @awulkiew

---

## Comment 6

> Username: teeks99  
> Created at: 2020-07-13 22:31:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/396#issuecomment-657825942  

so the original file that had the issue re-appeared. did the previous change for 1.64 get broken somehow?

---

## Comment 7

> Username: awulkiew  
> Created at: 2020-07-14 00:54:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/396#issuecomment-657904255  

Are you sure that the same file is causing problems? My guess is that it is caused by a similar file, also generated for `rtree` constructor, but a different one.  
  
Recently several new `rtree` constructors were added (https://github.com/boostorg/geometry/commit/10b0c372164a2162cb90b8bde9cbd71d3c16998d), some of them taking many arguments. The tool automatically generating the documentation puts all of the parameter types in the name of a file. So my guess is that the path is probably something like: `libs/geometry/doc/html/geometry/reference/spatial_indexes/boost__geometry__index__rtree/rtree_iterator__iterator__parameters_type_const____indexable_getter_const____value_equal_const____allocator_type_const____PackAlloc_const___.html`  
  
We should probably modify our utility `doxygen_xml2qbk` to generate shorter paths. This will not be backward compatible. So the link on the page "show the latest version of this file" will not work.

---

## Comment 8

> Username: teeks99  
> Created at: 2020-07-14 11:24:37 UTC  
> Updated at: 2020-07-14 11:27:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/396#issuecomment-658125772  

Here's what boost_1_74_0_b1_rc1 looks like:  
  
```  
$ ls boost_1_74_0/libs/geometry/doc/html/geometry/reference/spatial_indexes/boost__geometry__index__rtree/              
begin__.html  
bounds__.html  
clear__.html  
count_valueorindexable_const___.html  
empty__.html  
end__.html  
get_allocator__.html  
indexable_get__.html  
insert_convertibleorrange_const___.html  
insert_iterator__iterator_.html  
insert_value_type_const___.html  
operator__rtree_const___.html  
operator__rtree____.html  
parameters__.html  
qbegin_predicates_const___.html  
qend__.html  
query_predicates_const____outiter_.html  
remove_convertibleorrange_const___.html  
remove_iterator__iterator_.html  
remove_value_type_const___.html  
_rtree__.html  
rtree__.html  
rtree_iterator__iterator__allocator_type_const___.html  
rtree_iterator__iterator__allocator_type_const____packalloc_const___.html  
rtree_iterator__iterator_.html  
rtree_iterator__iterator__parameters_type_const____indexable_getter_const____value_equal_const____allocator_type_const____packalloc_const___.html  
rtree_parameters_type_const____indexable_getter_const____value_equal_const____allocator_type_const___.html  
rtree_range_const____allocator_type_const___.html  
rtree_range_const____allocator_type_const____packalloc_const___.html  
rtree_range_const___.html  
rtree_range_const____parameters_type_const____indexable_getter_const____value_equal_const____allocator_type_const____packalloc_const___.html  
rtree_rtree_____allocator_type_const___.html  
rtree_rtree_const____allocator_type_const___.html  
rtree_rtree_const___.html  
rtree_rtree____.html  
size__.html  
swap_rtree___.html  
value_eq__.html  
  
```  
  
vs. boost_1_73_0  
  
```  
$ ls boost_1_73_0/libs/geometry/doc/html/geometry/reference/spatial_indexes/boost__geometry__index__rtree/                                                                                                       
begin__.html  
bounds__.html  
clear__.html  
count_valueorindexable_const___.html  
empty__.html  
end__.html  
get_allocator__.html  
indexable_get__.html  
insert_convertibleorrange_const___.html  
insert_iterator__iterator_.html  
insert_value_type_const___.html  
operator__rtree_const___.html  
operator__rtree____.html  
parameters__.html  
qbegin_predicates_const___.html  
qend__.html  
query_predicates_const____outiter_.html  
remove_convertibleorrange_const___.html  
remove_iterator__iterator_.html  
remove_value_type_const___.html  
_rtree__.html  
rtree__.html  
rtree_iterator__iterator_.html  
rtree_parameters_type_const____indexable_getter_const____value_equal_const____allocator_type_const___.html  
rtree_range_const___.html  
rtree_rtree_____allocator_type_const___.html  
rtree_rtree_const____allocator_type_const___.html  
rtree_rtree_const___.html  
rtree_rtree____.html  
size__.html  
swap_rtree___.html  
value_eq__.html  
```

---

## Comment 9

> Username: mclow  
> Created at: 2020-07-14 18:40:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/396#issuecomment-658346103  

Just an observation: The new files (with very long names) have no backwards compatibility requirements (because they are *new*). So if someone were to rename them (somehow), and fix up the links in the generated docs, then we'd be golden.  
(Obviously this would have to be done via some jam settings, since we don't want a one-off thing, but can we get doxygen to generate short file names for only some files?)

---

## Comment 10

> Username: awulkiew  
> Created at: 2020-07-14 22:02:46 UTC  
> Updated at: 2020-07-14 22:03:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/396#issuecomment-658435996  

FYI, these names are generated automatically by quickbook when section name is full name of a function/ctor with parameters, etc. and there is no section id.  
  
I have a patch for our tool ready which sets section ids explicitly and the result is this list:  
```  
126rtree.html  
begin.html  
bounds.html  
clear.html  
count_val_cr.html  
empty.html  
end.html  
get_allocator.html  
indexable_get.html  
insert_con_cr.html  
insert_ite_ite.html  
insert_val_cr.html  
operator61_rtr_cr.html  
operator61_rtr_rr.html  
parameters.html  
qbegin_pre_cr.html  
qend.html  
query_pre_cr_out.html  
remove_con_cr.html  
remove_ite_ite.html  
remove_val_cr.html  
rtree.html  
rtree_ite_ite.html  
rtree_ite_ite_all_cr.html  
rtree_ite_ite_all_cr_pac_cr.html  
rtree_ite_ite_par_cr_ind_cr_val_cr_all_cr_pac_cr.html  
rtree_par_cr_ind_cr_val_cr_all_cr.html  
rtree_ran_cr.html  
rtree_ran_cr_all_cr.html  
rtree_ran_cr_all_cr_pac_cr.html  
rtree_ran_cr_par_cr_ind_cr_val_cr_all_cr_pac_cr.html  
rtree_rtr_cr.html  
rtree_rtr_cr_all_cr.html  
rtree_rtr_rr.html  
rtree_rtr_rr_all_cr.html  
size.html  
swap_rtr_r.html  
value_eq.html  
```  
  
Now, I could push it as it is and break all of the links in *Spatial Index* part of the reference or do this only for names longer than X. So the question is which option would be preferable? In case of the latter what name length would satisfy you?

---

## Comment 11

> Username: mclow  
> Created at: 2020-07-14 22:37:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/396#issuecomment-658447359  

Which are the new names in 1.74.0?

---

## Comment 12

> Username: mclow  
> Created at: 2020-07-14 22:49:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/396#issuecomment-658450638  

Looks like:  
```  
rtree_iterator__iterator__allocator_type_const___.html  
rtree_iterator__iterator__allocator_type_const____packalloc_const___.html  
rtree_iterator__iterator__parameters_type_const____indexable_getter_const____value_equal_const____allocator_type_const____packalloc_const___.html  
rtree_range_const____allocator_type_const___.html  
rtree_range_const____allocator_type_const____packalloc_const___.html  
rtree_range_const____parameters_type_const____indexable_getter_const____value_equal_const____allocator_type_const____packalloc_const___.html  
```  
  
Those can be made shorter w/o breaking any links.  
This one, though:  
`rtree_parameters_type_const____indexable_getter_const____value_equal_const____allocator_type_const___` is a problem

---

## Comment 13

> Username: awulkiew  
> Created at: 2020-07-15 00:08:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/396#issuecomment-658472463  

I'm fine with 1 non-working link. Are there any other ideas?

---

## Comment 14

> Username: mclow  
> Created at: 2020-07-15 00:13:33 UTC  
> Updated at: 2020-07-15 00:13:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/396#issuecomment-658473801  

it's your library :-) (That's not a criticism of your opinion, just a statement that you are (one of) the owners of Boost.Geometry)

---

## Comment 15

> Username: awulkiew  
> Created at: 2020-07-15 11:48:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/396#issuecomment-658721649  

@barendgehrels @mloskot @vissarion opinions please. :)

---

## Comment 16

> Username: awulkiew  
> Created at: 2020-07-15 11:52:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/396#issuecomment-658723277  

@teeks99 What file length would be ok for you? Are the names generated for constructors the only ones that are too long?

---

## Comment 17

> Username: vissarion  
> Created at: 2020-07-15 12:17:13 UTC  
> Updated at: 2020-07-15 12:17:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/396#issuecomment-658733443  

>Now, I could push it as it is and break all of the links in Spatial Index part of the reference or do this only for names longer than X. So the question is which option would be preferable? In case of the latter what name length would satisfy you?  
  
I am OK with both options. The one that does the job only for the longer filenames seems a bit preferable since it is less aggressive.

---

## Comment 18

> Username: teeks99  
> Created at: 2020-07-15 12:48:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/396#issuecomment-658746840  

To be clear, it isn't a filename length limitation, it is a full path limitation. On windows the limit has historically been 256 chars, this is being relaxed but not all tools support longer paths yet.   
  
The above directory (102 chars):  
`boost_1_73_0/libs/geometry/doc/html/geometry/reference/spatial_indexes/boost__geometry__index__rtree/`  
  
Plus the file (146 chars):  
`rtree_iterator__iterator__parameters_type_const____indexable_getter_const____value_equal_const____allocator_type_const____packalloc_const___.html`  
  
Is still under this, but it was close enough that my `D:\RB\build-msvc-10.0` (19 chars + `D:\`) was over the limit. Users may reasonably decide to install boost into `C:\Program Files\Boost\` (21 chars), so I think we should limit paths to 256 - 21 = 235 chars.  Shorter would be better.  
  
Clearly shortening the very long filenames would help a lot, but there's also some redundancy in the directory path. Can we take the `geometry` inside `html` out? How about the `boost__geometery__index` part of the end directory? I realize that these would break the websites "go to the current version" feature...how critical is that?

---

## Comment 19

> Username: awulkiew  
> Created at: 2020-07-15 16:04:55 UTC  
> Updated at: 2020-07-15 16:06:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/396#issuecomment-658853176  

> How about the `boost__geometery__index`  
  
This is also an option. Actually I've already implemented it locally to generate `b_g_i_...` instead. Removing the namespaces entirely is also an option which would probably work for now but in general could cause naming conflicts. But anyway, changing this would break backward compatibility for the rtree and its members, observers and parameters.  
  
There is also an option of shortening `spatial_indexes` to `index`. This would break backward compatibility for all spatial-index-related pages.  
  
> Can we take the `geometry` inside `html` out?  
  
This is generated by xsl/quickbook and can probably be set in Jamfile, maybe? I don't know. It may be related to some other setting like sections nesting. Anyway, this would break backward compatibility for the whole documentation.  
  
> I realize that these would break the websites "go to the current version" feature...how critical is that?  
  
I don't know. This wouldn't be the first library where this was done. I face this issue quite often with other libraries. It's simply annoying but I'm not totally against it.  
  
I simply doubt that all of the above is really needed in practice, AFAIU you only have problems with the paths containing these long filenames, correct? So these ones for sure:  
```  
rtree_iterator__iterator__parameters_type_const____indexable_getter_const____value_equal_const____allocator_type_const____packalloc_const___.html  
rtree_parameters_type_const____indexable_getter_const____value_equal_const____allocator_type_const___.html  
rtree_range_const____parameters_type_const____indexable_getter_const____value_equal_const____allocator_type_const____packalloc_const___.html  
```  
and these maybe, but not really:  
```  
rtree_iterator__iterator__allocator_type_const___.html  
rtree_iterator__iterator__allocator_type_const____packalloc_const___.html  
rtree_range_const____allocator_type_const___.html  
rtree_range_const____allocator_type_const____packalloc_const___.html  
rtree_rtree_____allocator_type_const___.html  
rtree_rtree_const____allocator_type_const___.html  
```  
correct?

---

## Comment 20

> Username: teeks99  
> Created at: 2020-07-15 17:29:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/396#issuecomment-658900746  

Yeah, fixing those would correct my issue and preserve backwards compatibility.   
  
Beyond that it comes to generally supporting boost in deeper directory structures vs. backwards compatibility.

---

## Comment 21

> Username: awulkiew  
> Created at: 2020-07-16 22:21:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/396#issuecomment-659706869  

The fix is ready. As I mentioned above I've chosen the compromise solution. One link is not backward compatible. Currently the max filename length is set to 59 (can be changed with program option in python script). If there are additional problems in the future we'll apply more severe solution. The filenames for ctors are:  
```  
rtree__.html  
rtree_ite_ite_all_cr_pac_cr.html  
rtree_ite_ite_par_cr_ind_cr_val_cr_all_cr_pac_cr.html  
rtree_iterator__iterator_.html  
rtree_iterator__iterator__allocator_type_const___.html  
rtree_par_cr_ind_cr_val_cr_all_cr.html  
rtree_ran_cr_all_cr_pac_cr.html  
rtree_ran_cr_par_cr_ind_cr_val_cr_all_cr_pac_cr.html  
rtree_range_const___.html  
rtree_range_const____allocator_type_const___.html  
rtree_rtree____.html  
rtree_rtree_____allocator_type_const___.html  
rtree_rtree_const___.html  
rtree_rtree_const____allocator_type_const___.html  
```

---

## Comment 22

> Username: mloskot  
> Created at: 2020-07-20 09:13:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/396#issuecomment-660906966  

@awulkiew Thanks for the fix!
