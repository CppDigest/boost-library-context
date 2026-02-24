# #66 - Add visualization debug option to the docca build [Open]

> Username: evanlenz  
> Created at: 2021-03-29 18:03:02 UTC  
> Updated at: 2022-01-04 16:05:33 UTC  
> Url: https://github.com/boostorg/docca/issues/66  

I am hoping to eventually get a visualization debug option working on the docca build using [xslt-visualizer](https://github.com/evanlenz/xslt-visualizer) (which itself has some pending optimizations that will affect the output, so it's not quite ready yet). [Here's a basic demo](http://xmlportfolio.com/xslt-visualizer-demo/). [Here is some documentation](https://github.com/evanlenz/beast/blob/saxon-xsl/doc_visualized/README.md) that provides an overview in relation to docca. And [here's a sample output](http://xmlportfolio.com/forVinnie/libs/beast/doc_visualized/html/beast/ref/boost__beast__buffer_bytes.html) for Beast from a while back. The "doc_visualized" build was using bash scripts and got removed from the docca project. I would eventually like to make it bjam-friendly and get it back into docca as a build option. Once I get the latest xslt-visualizer changes checked in, we should be good to explore that.

---

## Comment 1

> Username: evanlenz  
> Created at: 2021-03-29 18:06:47 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-809592804  

One plan could be for me to first get the bash scripts working again, share them in a branch, and then ask for help with porting them to bjam as a new build option.

---

## Comment 2

> Username: evanlenz  
> Created at: 2021-03-29 18:37:11 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-809616988  

Here's the blocking xslt-visualizer issue whose solution is pending: https://github.com/evanlenz/xslt-visualizer/issues/5

---

## Comment 3

> Username: grisumbras  
> Created at: 2021-03-30 13:05:32 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-810218640  

Where can I see the bash scripts for this build mode?

---

## Comment 4

> Username: evanlenz  
> Created at: 2021-03-31 08:28:59 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-810881810  

@grisumbras I've been trying to get them working again, banging my head against the wall on this one remaining issue. Once I have it solved, I'll point you to a branch with the visualization build, along with some pointers.

---

## Comment 5

> Username: evanlenz  
> Created at: 2021-04-01 09:28:35 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-811781407  

@grisumbras Thanks for your patience. It seems that my approach was exploiting [an apparent Saxon bug](https://saxonica.plan.io/issues/4959). I came up with a workaround though and now have the bash scripts working again. See the above pull request.  
  
The overall approach gets the job done, but it's messy in that 1) it relies on the output from a regular build, 2) it does a lot of the stuff that bjam already does (copying files to get them in the right folder for the build), and 3) it uses bjam at the end (to run the xsltproc step). Refer to the [README](https://github.com/evanlenz/docca/blob/issue66-crappy-starter-bash-scripts-for-visualization-build/example_visualized/README.md) for an overview. For my testing, I have been repeatedly running [clean-build.sh](https://github.com/evanlenz/docca/blob/issue66-crappy-starter-bash-scripts-for-visualization-build/example_visualized/clean-build.sh).  
  
For now, maybe we can skip the partial build support, unless you're really ambitious. The smaller size of the "example" project (compared to, say, Beast) has helped mitigate the problem (of longer wait times for visualization builds).  
  
Among the challenges you may encounter is needing to enhance the saxonhe widget in bjam to support a couple different types of stylesheet parameters (with and without a question mark at the beginning). (Scroll down to the "Command line parameters" section at the bottom of [this page of Saxon's documentation](https://www.saxonica.com/documentation/#!using-xsl/commandline).) Also, how to add a JVM option (Xmx, to increase the heap size). You may see I am sometimes passing in a Saxon option called -threads. Unfortunately, that feature may not be available in the free product (Saxon-HE). So you can disregard those for now.  
  
My bash scripts now work correctly before and after the bjam changes you made.  
  
I don't anticipate wanting the "example_visualized" folder anymore. Instead, if we could just have a build option that, when enabled, simply augments the existing build results with the visualization features, that would be ideal. For that reason, I will likely remove the green "[doc_build_html]" link in the visualized build, as it would be redundant:  
![image](https://user-images.githubusercontent.com/604974/113269912-c8b0b500-928d-11eb-9c32-c9bca5307946.png)  
  
The DEBUG=yes option encapsulated in stage2-params.xml is what effects the colored links you see above. These represent the entry point for inspecting the stages of processing that the page went through. I will post another lengthy comment pulled from an email I sent @vinniefalco after I first put this together. It provides a little more orientation.

---

## Comment 6

> Username: evanlenz  
> Created at: 2021-04-01 09:49:07 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-811793100  

![[Page type: member] [doc_build_html] [doxygen_page_xml]---stage1_visualized-->[docca_page_xml]---stage2_visualized-->[quickbook_result]](https://user-images.githubusercontent.com/604974/113269912-c8b0b500-928d-11eb-9c32-c9bca5307946.png)  
  
The red [Page type: ...] text shows the page type. It is always one of three types: **compound**, **member**, or **overload-list**.  
  
The green [doc_build_html] text is a link to the corresponding file in the regular doc build. This is basically just the same file but without the colorful DEBUG info. As I said, we'll probably delete it.  
  
The alternating blue and pink links depict the processing pipeline (phases 2 and 3 in [the sequence described here](https://github.com/evanlenz/docca/blob/issue66-crappy-starter-bash-scripts-for-visualization-build/example_visualized/README.md)):  
  
* The [doxygen_page_xml] link shows the extracted XML (using extract-xml-pages.xsl) for this specific page.  
* The stage1_visualized link shows the visualization of stage1.xsl, applied to doxygen_page_xml and producing docca_page_xml.  
* The [docca_page_xml] link shows the normalized XML format tailored to what we want to display on the screen.  
* The stage2_visualized link shows the visualization of stage2.xsl, applied to docca_page_xml and producing quickbook_result.  
* The [quickbook_result] link shows the fragment of QuickBook content that represents this page (and that will get assembled with the rest before eventually being converted to HTML).  
  
The blue links (data) are very useful, even without using the pink links (transformation visualizations). For debugging, they allow you to immediately divide and conquer, narrowing down your code investigation based on where in the pipeline the problem first appeared. And if you are having trouble figuring out why the code is or isn't doing something you expected, the visualizations are handy for that. The blue links will work any time you run the visualized build, even if you have visualizations turned off (e.g. to speed up the build).  
  
When you open a visualization page, be sure to drag the horizontal slider at the top of the page as described in the basic [xslt-visualizer demo](http://xmlportfolio.com/xslt-visualizer-demo/).

---

## Comment 7

> Username: evanlenz  
> Created at: 2021-04-01 09:50:16 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-811793704  

Finally, relating back to the build, the essence of the visualization build option is that we run all the same transformations as in the non-visualization build but using XSLT stylesheets that have been pre-processed ("trace-enabled"). Once we have done that, we just need to render the trace-data (that gets created as a side effect) to the HTML visualization pages. Thus, there is a three step process:  
  
1. Trace-enable the XSLT code  
2. Run the trace-enabled code to generate the page + trace data  
3. Render the trace data to the HTML visualization view  
  
This rudimentary sequence is also described in the [xslt-visualizer README](https://github.com/evanlenz/xslt-visualizer/blob/master/README.md).

---

## Comment 8

> Username: evanlenz  
> Created at: 2021-08-03 06:03:47 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-891557842  

I demo'd the xslt-visualizer in use with docca this past Saturday as part of a Balisage conference talk (on visualizing musical transformations). People were impressed. I'd love to get it properly integrated into the docca build as a bjam option. @grisumbras, hope you don't mind I assigned you. If you do have any free time to work on this, let me know if you need more info from me. I'd be more than happy to consult on it.

---

## Comment 9

> Username: grisumbras  
> Created at: 2021-08-03 06:23:58 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-891569077  

I've already started working on this. I think, I understand what I need to do, but due to the number of build steps docca has, this will take some time. Also, it will likely require a patch to b2's saxon module.

---

## Comment 10

> Username: evanlenz  
> Created at: 2021-08-03 06:43:16 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-891579139  

That all makes sense and is great news. Thanks for the update!

---

## Comment 11

> Username: evanlenz  
> Created at: 2021-10-14 21:25:49 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-943746625  

@grisumbras How's the progress on this? Need any help from me?

---

## Comment 12

> Username: vinniefalco  
> Created at: 2021-10-19 18:22:00 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-946984955  

What's going on here?

---

## Comment 13

> Username: grisumbras  
> Created at: 2021-10-19 18:39:09 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-947003964  

I'm hitting weird issues. My current theory is that Boost's Jamroot is negatively affecting things it shouldn't have.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2021-10-19 23:24:31 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-947180824  

Could cmake help?

---

## Comment 15

> Username: grisumbras  
> Created at: 2021-10-22 10:58:36 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-949518161  

I'm getting a Saxon error:  
```  
saxonhe.saxonhe ../../../bin.v2/tools/docca/example/_reference-dir-vis/stage1_visualized/code-trace-enabled/stage1.xsl  
Error at char 6 in expression in xsl:copy-of/@select on line 48 column 39 of trace-enable.xsl:  
  Failed to create output file  
  file:/home/grisumbras/dev/cppal/boost/bin.v2/tools/docca/example/_reference-dir-vis/stage1_visualized/code-trace-enabled/stage1.xsl.modules/base-stage1.xsl. Caused by java.io.IOException: Э[x42d]т[x442]о[x43e] н[x43d]е[x435] к[x43a]а[x430]т[x442]а[x430]л[x43b]о[x43e]г[x433]  
     invoked by xsl:for-each at file:/home/grisumbras/dev/cppal/boost/tools/docca/example/../../../bin.v2/tools/docca/example/_reference-dir-vis/xsl/trace-enable.xsl#46  
  In template rule with match="/" on line 39 of trace-enable.xsl  
     invoked by xsl:next-match at file:/home/grisumbras/dev/cppal/boost/tools/docca/example/../../../bin.v2/tools/docca/example/_reference-dir-vis/xsl/trace-enable.xsl#34  
  In template rule with match="/" on line 29 of trace-enable.xsl  
  Failed to create output file file:/home/grisumbras/dev/cppal/boost/bin.v2/tools/docca/example/_reference-dir-vis/stage1_visualized/code-trace-enabled/stage1.xsl.modules/base-stage1.xsl  
  
    "java" -jar "/home/grisumbras/dev/saxonhe/saxon-he-10.3.jar" -o:"../../../bin.v2/tools/docca/example/_reference-dir-vis/stage1_visualized/code-trace-enabled/stage1.xsl" -s:"../../../bin.v2/tools/docca/example/_reference-dir-vis/stage1.xsl" -xsl:"../../../bin.v2/tools/docca/example/_reference-dir-vis/xsl/trace-enable.xsl"  
```  
  
`Э[x42d]т[x442]о[x43e] н[x43d]е[x435] к[x43a]а[x430]т[x442]а[x430]л[x43b]о[x43e]г[x433]` means "This is not a directory" and refers to `/home/grisumbras/dev/cppal/boost/bin.v2/tools/docca/example/_reference-dir-vis/stage1_visualized/code-trace-enabled/stage1.xsl.modules` which is created as a regular file. What can be the source of this problem? One thing I've noticed is that the way Saxon is invoked in the scripts is different from the way it is invoked in b2. The scripts use  
```  
java -cp "$CLASSPATH" net.sf.saxon.Transform  
```  
whereas  b2 `saxonhe` module uses  
```  
java -jar "/home/grisumbras/dev/saxonhe/saxon-he-10.3.jar"  
```

---

## Comment 16

> Username: evanlenz  
> Created at: 2021-10-22 12:47:49 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-949599841  

I haven't been able to reproduce this yet using the bash scripts. I tried switching the bash script to using the -jar method. No success yet; that might be a red herring. I'm going to be gone all day but will follow up again when I can. If there's anything you can provide to help me reproduce it, that would help. Thanks for all your work on this! Sorry I can't dive in more until tonight.

---

## Comment 17

> Username: evanlenz  
> Created at: 2021-10-27 00:50:39 UTC  
> Updated at: 2021-10-27 00:54:03 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-952443557  

@grisumbras ~~It looks like you are not passing the same parameters to Saxon as in this bash script for trace-enabling stage1:~~  
~~https://github.com/evanlenz/docca/blob/issue66-crappy-starter-bash-scripts-for-visualization-build/example_visualized/trace-stage1.sh~~  
  
~~Sorry if I misled you previously. The above branch on my docca fork has the bash scripts on which to base the b2 integration.~~  
  
EDIT: Sorry, I was confusing steps. The error shows you're doing it right for the prepare.sh script. (I was confusing it with the trace.sh script.)  
  
Here are some follow-up questions to be taken in order:  
  
- What is the contents of the regular file at stage1.xsl.modules (if anything)?  
- Are you using the latest xslt-visualizer master branch?  
- Do you have a WIP branch you could share with your code so I can try to reproduce the problem?  
- Are you using Saxon's -s and -o output options to process an entire input directory (and output the results to the output directory)? Are you calling run-trace.xsl (as in the linked bash script above)?  
  
Let me know if you have any questions!

---

## Comment 18

> Username: evanlenz  
> Created at: 2021-10-27 00:56:59 UTC  
> Updated at: 2021-10-27 00:57:18 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-952446644  

I edited my comment above, because I misunderstood something, so please re-read. :-)  
  
Here is another thing to try. Make the following changes and try running again and let me see the resulting file at testOutputForEvan.xml.  
  
```diff  
diff --git a/xsl/trace-enable.xsl b/xsl/trace-enable.xsl  
index 946777c..79f0472 100644  
--- a/xsl/trace-enable.xsl  
+++ b/xsl/trace-enable.xsl  
@@ -27,6 +27,9 @@  
  
   <!-- Set some tunnel parameters (can't be global variables, because current-output-uri() is cleared when evaluating those) -->  
   <xsl:template match="/" priority="1">  
+    <xsl:result-document href="testOutputForEvan.xml">  
+      <xsl:sequence select="$all-results"/>  
+    </xsl:result-document>  
     <xsl:variable name="output-stylesheet-file-name" select="tokenize(current-output-uri(),'/')[last()]"/>  
     <!-- This will just be .modules if the base output URI was not set.  
          That should be okay, but it's best to set it (e.g. using Saxon's -o flag) -->  
@@ -43,16 +46,20 @@  
     <xsl:call-template name="top-module"/>  
  
     <!-- Output trace-enabled versions of all the original XSLT modules -->  
+    <!--  
     <xsl:for-each select="$all-results/result-docs/trace:result-document[not(@href = preceding-sibling::trace:result-document/@href)]">  
       <xsl:result-document href="{$output-dir}/{@href}">  
         <xsl:copy-of select="node()"/>  
       </xsl:result-document>  
     </xsl:for-each>  
+    -->  
  
     <!-- Output the rule tree -->  
+    <!--  
     <xsl:result-document href="{$output-dir}/rule-tree.xml">  
       <xsl:sequence select="$all-results/result-docs/rule-tree"/>  
     </xsl:result-document>  
+    -->  
  
   </xsl:template>  
```

---

## Comment 19

> Username: evanlenz  
> Created at: 2021-11-01 05:36:01 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-955947528  

@grisumbras Any luck with my suggestions? I can help troubleshoot if you provide more info or help me reproduce.

---

## Comment 20

> Username: grisumbras  
> Created at: 2021-11-03 14:12:56 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-959189965  

1. The contents of `stage1.xsl.modules` are  
```XSL  
 <xsl:stylesheet xmlns:trace="http://lenzconsulting.com/tracexslt" xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" version="3.0" exclude-result-prefixes="xs" expand-text="yes">  
  
  <xsl:import href="base-stage1.xsl"/>  
  <xsl:import href="base-config.xsl"/>  
  
  <!-- Custom config module copied from project-specific directory -->  
  <xsl:include href="custom-overrides.xsl"/>  
  
</xsl:stylesheet>  
```  
  
2. I think I do use xslt-visualizer from latest master (commit 7c6af536c4610e6b4ffb351662961e13e576834d)  
  
3. #116  
When testing I recommend running `b2 .//_reference-dir-vis/stage1_visualized/code-trace-enabled/stage1.xsl --docca-visualize -d+2` from `example` directory. `-d+2` tells b2 to output actual build commands.  
  
4. I don't reach `run-trace.xsl` step. The problem occurs on the `trace-enable.xsl` step. The only difference in flags is the one I've mentioned previously (`-jar` rather than `-cp "$CLASSPATH"`).  
  
5. Contents of `testOutputForEvan.xml` https://gist.github.com/grisumbras/a10187ed94ee4dfebf14053592e2e1c5

---

## Comment 21

> Username: vinniefalco  
> Created at: 2021-11-03 16:14:30 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-959602847  

"crappy" is non-sterile

---

## Comment 22

> Username: evanlenz  
> Created at: 2021-11-04 02:31:36 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-960387439  

@grisumbras Thanks! This is what I was looking for in the gist you sent me. It happened to be on the very first line:  
  
```xml  
<trace:result-document href="">  
```  
  
I believe this is what caused what was supposed to be a directory to be written as a regular file. I will dig into this further and see if I can figure out what's going on (though I haven't reproduced the error yet, see below).  
  
I tested the build the way you suggested and it produced output to the screen but it finished suspiciously fast like nothing happened. Here's the output I saw: https://gist.github.com/evanlenz/720d712a7e9c1ee896b5c16c755fb93f

---

## Comment 23

> Username: evanlenz  
> Created at: 2021-11-04 02:43:12 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-960398517  

Ah, my bad, never mind. I still had my temporary change in the xslt-visualizer. Now I've reproduced and can investigate further.

---

## Comment 24

> Username: evanlenz  
> Created at: 2021-11-05 00:13:04 UTC  
> Updated at: 2021-11-05 00:13:54 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-961529014  

I confirmed that the `href=""` in that test output should be `href="stage1.xsl"`, though I haven't yet figured out why it's not. @grisumbras How do I tell what version of Saxon you're using? My first thought was that it *could* be related to a discrepancy (or bug fix) between Saxon versions. I'd like to first rule that out and then keep digging.

---

## Comment 25

> Username: grisumbras  
> Created at: 2021-11-08 06:38:36 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-962853750  

I was absolutely sure I've posted my versions of Saxon and Java, but apparently I didn't hit "send". Anyways:  
```  
Saxon-HE 10.3J from Saxonica  
  
openjdk 11.0.11 2021-04-20  
OpenJDK Runtime Environment (build 11.0.11+9-Ubuntu-0ubuntu2)  
OpenJDK 64-Bit Server VM (build 11.0.11+9-Ubuntu-0ubuntu2, mixed mode, sharing)  
```

---

## Comment 26

> Username: evanlenz  
> Created at: 2021-11-08 16:17:48 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-963318798  

Thanks! That turns out to be a red herring, since I realized that I'm running Saxon-HE 9.9 and I'm getting the same error. So I'll keep digging...

---

## Comment 27

> Username: evanlenz  
> Created at: 2021-11-10 02:17:52 UTC  
> Updated at: 2021-11-10 02:19:02 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-964719865  

@grisumbras I finally got to the bottom of this issue. Please update the xslt-visualizer submodule to the latest commit and let me know if that solves the problem: https://github.com/evanlenz/xslt-visualizer/commit/e28b494a240944d4010e180b9ce085a89236474f  
  
You may need to delete that regular file (stage1.xsl.modules) if it doesn't get cleaned up automatically.

---

## Comment 28

> Username: grisumbras  
> Created at: 2021-11-10 13:29:12 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-965138449  

The update works.

---

## Comment 29

> Username: evanlenz  
> Created at: 2022-01-04 16:05:33 UTC  
> Url: https://github.com/boostorg/docca/issues/66#issuecomment-1004936592  

@grisumbras I was glad to hear the update worked. Did that unblock you or did you run into any other issues? I'm wondering how close we are to making things work. I'm still quite happy to help troubleshoot issues. Thanks!
