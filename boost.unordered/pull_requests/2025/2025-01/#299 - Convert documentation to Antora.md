# #299 Convert documentation to Antora [Merged]

> Username: cmazakas  
> Created at: 2025-01-03 17:49:45 UTC  
> Updated at: 2025-01-20 16:18:37 UTC  
> Merged at: 2025-01-20 16:18:32 UTC  
> Closed at: 2025-01-20 16:18:32 UTC  
> Url: https://github.com/boostorg/unordered/pull/299  

`build_antora.sh` is the hook used by the release scripts to builds the docs. Locally, all one needs to do is `npm install --dev && npx antora unordered-playbook.yml`

---

## Comment 1

> Username: joaquintides  
> Created_at: 2025-01-03 18:46:45 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2569667552  

`build_antora.sh` is empty?

---

## Comment 2

> Username: cmazakas  
> Created_at: 2025-01-03 18:50:06 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2569671307  

> `build_antora.sh` is empty?  
  
Ha. Alright, forgot to copy-paste the contents when I made this new branch. Will update the script.

---

## Comment 3

> Username: sdarwin  
> Created_at: 2025-01-03 19:08:20 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2569691576  

- When release scripts build a whole boost archive, they call this script as one of the steps:    
https://github.com/boostorg/website-v2-docs/blob/develop/libdoc.sh  
  
- Then, local docs uses `build_antora.sh` (if boostorg/url is the example)

---

## Comment 4

> Username: joaquintides  
> Created_at: 2025-01-03 23:30:53 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2569932134  

Would it make sense to have an equivalent `build_antora.bat` for Windows?

---

## Comment 5

> Username: cmazakas  
> Created_at: 2025-01-03 23:36:15 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2569934971  

> Would it make sense to have an equivalent `build_antora.bat` for Windows?  
  
Sure! I'll add that in too.

---

## Comment 6

> Username: cmazakas  
> Created_at: 2025-01-06 23:00:14 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2574076576  

@sdarwin Hmm, shouldn't we be getting doc previews for this? Did I silently break the bot that does this? I can obviously fix any of the issues that have arisen from this.

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2025-01-06 23:08:29 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2574084968  

An automated preview of the documentation is available at [https://299.unordered.prtest2.cppalliance.org/index.html](https://299.unordered.prtest2.cppalliance.org/index.html)

---

## Comment 8

> Username: sdarwin  
> Created_at: 2025-01-06 23:17:18 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2574093525  

Alright, a preview just posted. The job must be customized for `b2` or `antora`.

---

## Comment 9

> Username: joaquintides  
> Created_at: 2025-01-07 11:29:50 UTC  
> Updated_at: 2025-01-07 11:30:02 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2575055008  

I've gone through all the docs and this is a list of the broken internal links I've detected:  
  
* Introduction  
  * See the Equality Predicates and Hash Functions section for more details.  
* Basics of Hash Tables  
  * There is more information on hash functions and equality predicates in the next section.  
* Equality Predicates and Hash Functions  
  * For example, boost::unordered_map is declared as:  
* Regular Containers  
  * See the reference for more details on the rehash function.  
* Concurrent Containers  
  * Consult the references of boost::concurrent_node_set, boost::concurrent_node_map, boost::concurrent_flat_set and boost::concurrent_flat_map for the complete list of visitation-enabled operations. Links work, but the link for boost::concurrent_node map has concurrent_node_map.html#concurrent_flat_map when it should have concurrent_node_map.html#concurrent_node_map.  
* Data Structures  
  * Closed-addressing Containers: For more information on implementation rationale, read the corresponding section. Wrongly links to open-addressing section.  
* Reference  
  * stats. Should be more aptly named "Statistics"  
    * number of bucket groups accessed per operation  
    * These statistics can be used to determine if a given hash function can be marked as avalanching.  
  * using stats = stats-type;  
  * If hash_is_avalanching<Hash>::value is true  
  * Only available if statistics calculation is enabled. The links to statistics is broken. Happens everywhere in the reference.  
  * Deduction guides: links to iter-value_type broken

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2025-01-07 20:00:27 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2576120733  

An automated preview of the documentation is available at [https://299.unordered.prtest2.cppalliance.org/index.html](https://299.unordered.prtest2.cppalliance.org/index.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2025-01-07 22:55:26 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2576380563  

An automated preview of the documentation is available at [https://299.unordered.prtest2.cppalliance.org/index.html](https://299.unordered.prtest2.cppalliance.org/index.html)

---

## Comment 12

> Username: k3DW  
> Created_at: 2025-01-09 02:21:25 UTC  
> Updated_at: 2025-01-09 19:58:57 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2579046384  

I'm wondering about these links at the top right on desktop. On the preview they don't seem to do anything. Are they meant to do something later? Or can they be removed?  
![image](https://github.com/user-attachments/assets/512dd6dc-2630-48a2-8295-c97b7ea13138)  
  
  
I think the "Edit this Page" button is useful to direct anyone to GitHub if they see a mistake or improvement. That button should stay. But I don't see a need for the others.

---

## Comment 13

> Username: joaquintides  
> Created_at: 2025-01-09 17:38:24 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2580899256  

The links to the full-size images in this section are broken:  
  
https://299.unordered.prtest2.cppalliance.org/unordered/benchmarks.html#benchmarks_boostconcurrent_flatnode_map

---

## Comment 14

> Username: cmazakas  
> Created_at: 2025-01-09 19:59:56 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2581144745  

> I'm wondering about these links at the top right on desktop. On the preview they don't seem to do anything. Are they meant to do something later? Or can they be removed?   
> I think the "Edit this Page" button is useful to direct anyone to GitHub if they see a mistake or improvement. That button should stay. But I don't see a need for the others.  
  
This is an artifact of the default Antora UI bundle which should be solved when we use the release one pulled in during the `libs.playbook.yml` build here:  
https://github.com/boostorg/website-v2-docs/blob/develop/libs.playbook.yml  
  
Otherwise, yeah, we can fix this by building our own UI bundle.

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2025-01-09 20:25:34 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2581184271  

An automated preview of the documentation is available at [https://299.unordered.prtest2.cppalliance.org/index.html](https://299.unordered.prtest2.cppalliance.org/index.html)

---

## Comment 16

> Username: sdarwin  
> Created_at: 2025-01-10 15:35:53 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2582978376  

Hi @cmazakas , from comments in Slack, not sure if you are intending to switch the methodology back to using `b2` and the doc/html folder, while still using antora. And then, not the global playbook?  In that case it would create a new, hybrid type of docs. `linuxdocs.sh` would need to distinguish the situation.   A thought comes to mind, rename `build_antora.sh` to something distinct. Maybe `build_b2_antora.sh` or similar, so that `build_antora.sh`  has a certain meaning, and `build_b2_antora.sh?` another one, if such scripts are discovered in various repositories.

---

## Comment 17

> Username: joaquintides  
> Created_at: 2025-01-12 18:14:32 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2585856412  

Build setup:  
  
* `doc/index.html` redirect file needs be readjusted.  
* `preview.md` and `roadmap.md` seem to have sneaked in with no purpose.  
* `Jamfile.v2`: we would need a Windows version of `run-script`.  
* `build_antora.sh`: does it have execution permissions?  
* `build_antora.sh`: what's `npm ci` for?

---

## Comment 18

> Username: joaquintides  
> Created_at: 2025-01-13 12:08:07 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2586932586  

On mobile devices (tested with an iPhone 8):  
  
* Home --> TOC --> click on "Regular Containers": doesn't jump to the section. A further click is needed. Note the expanding icon on the top right corner appearing after the first click.  
<img src="https://github.com/user-attachments/assets/95dafd87-88ef-449d-b7e6-96052061e020" width="33%">  
<img src="https://github.com/user-attachments/assets/8806eba0-7319-4155-97a6-4c2fc6b82327" width="33%">  
  
* Contents menu: this menu appears OK on all sections (see example) except on the subsections in the reference, see `boost::unordered_map` for instance.  
<img src="https://github.com/user-attachments/assets/ada96359-fdbf-4610-840b-b4c30226e791" width="33%">  
<img src="https://github.com/user-attachments/assets/1d32d664-efd2-4115-87ee-32c84a58fdb5" width="33%">  
  
* `boost::concurrent_flat_map::try_emplace`: text abnormaly large. I haven't checked all the places where this occurs.  
<img src="https://github.com/user-attachments/assets/87f3189b-7c63-40f9-be53-f63297992e02" width="33%">

---

## Comment 19

> Username: sdarwin  
> Created_at: 2025-01-13 14:41:27 UTC  
> Updated_at: 2025-01-13 15:16:11 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2587287626  

> build_antora.sh: what's npm ci for?  
  
This is an interesting point. `npm ci` installs npm packages.     
  
However, the overall design is to have release-tools preinstall packages in the docker image so when `ci_boost_release.py` builds everything there is less risk of package failure, and it's faster.  
  
From that perspective, we would prefer to avoid:  
- each separate library installing packages  
- variation of package versions across boost libraries  
  
`build_antora.sh` was designed for a certain purpose, which is local testing. It has previously never been run with a full release bundle, ci_boost_release.py  
  
Potentially, there could be multiple scripts in unordered/doc/, for local builds, and ci_boost_release.

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2025-01-13 22:12:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2588330035  

An automated preview of the documentation is available at [https://299.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html](https://299.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html)

---

## Comment 21

> Username: sdarwin  
> Created_at: 2025-01-13 22:25:26 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2588350812  

Open to contributions: allow antora to function with git submodules:  
  
`https://github.com/isomorphic-git/isomorphic-git/issues/1647`

---

## Comment 22

> Username: cppalliance-bot  
> Created_at: 2025-01-14 19:01:39 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2590888965  

An automated preview of the documentation is available at [https://299.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html](https://299.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html)

---

## Comment 23

> Username: cmazakas  
> Created_at: 2025-01-14 20:10:00 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2591010409  

> Build setup:  
>   
> * `doc/index.html` redirect file needs be readjusted.  
> * `preview.md` and `roadmap.md` seem to have sneaked in with no purpose.  
> * `Jamfile.v2`: we would need a Windows version of `run-script`.  
> * `build_antora.sh`: does it have execution permissions?  
> * `build_antora.sh`: what's `npm ci` for?  
  
I've deleted `doc/index.html` and `preview.md` and `roadmap.md`. `doc/index.html` must be a relic of previous refactors, it doesn't seem necessary any longer. The main top-level repo's `index.html` now correctly redirects to: `<meta http-equiv="refresh" content="0; URL=doc/html/index.html">`  
  
I'm not sure if a Windows version of the script is a blocker for release but I'm hoping @sdarwin can shed some illumination there. As far as I'm aware, the docs generation is done purely via `b2 doc` in the boost-root in a docker container.   
  
For Windows, there's not much difference between `b2 libs/unordered/doc` and `npx antora unordered-playbook.yml`.  
  
The `npm ci` call is used to install all the dependencies required. It's a variation of `npm install --dev` but it requires a package-lock.json be present, which is idiomatic for ensuring consistency in regards to dep versions.

---

## Comment 24

> Username: cppalliance-bot  
> Created_at: 2025-01-14 20:16:26 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2591020651  

An automated preview of the documentation is available at [https://299.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html](https://299.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html)

---

## Comment 25

> Username: sdarwin  
> Created_at: 2025-01-14 20:59:25 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2591091601  

- The release process runs on Linux, and shouldn't be blocked by the absence of a Microsoft-Windows-specific script.      
   
- In general, there are some programs that run equally well on Linux and Windows without customization, so a windows version isn't necessary.   I don't know how that applies to the updates in this pull request though.  Compare to other boost libs.

---

## Comment 26

> Username: cppalliance-bot  
> Created_at: 2025-01-14 23:57:32 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2591341554  

An automated preview of the documentation is available at [https://299.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html](https://299.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html)

---

## Comment 27

> Username: cppalliance-bot  
> Created_at: 2025-01-15 00:38:18 UTC  
> Url: https://github.com/boostorg/unordered/pull/299#issuecomment-2591392406  

An automated preview of the documentation is available at [https://299.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html](https://299.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html)

---
