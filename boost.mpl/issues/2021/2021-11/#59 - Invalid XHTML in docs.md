# #59 - Invalid XHTML in docs [Closed]

> Username: jwakely  
> Created at: 2021-11-16 18:07:05 UTC  
> Updated at: 2025-07-03 04:17:46 UTC  
> Closed at: 2025-07-03 04:17:46 UTC  
> Url: https://github.com/boostorg/mpl/issues/59  

The docs contain things like:  
  
```xml  
<h1><a class="toc-backref" href="./categorized-index.html#id1612"><a class="subsection-title" href="#concepts" name="concepts">Concepts</a></a></h1>  
```  
  
This is invalid, you cannot nest an `<a>` element inside another `<a>` element.  
  
  
```xml  
<ul class="simple" id="value-part">  
<span id="key-part"></span><li>A <em>key</em> is a part of the element type used to identify and retrieve  
```  
  
This is invalid, you cannot have a `<span>` child of `<ul>`.  
  
```xml  
<pre class="literal-block">  
typedef <a href="./begin.html" class="identifier">begin</a>&lt;Sequence&gt;::type i<sub>1</sub>;  
```  
  
You cannot have `<sub>` inside `<pre>` (there are other cases like this with `<sup>` too).  
  
```xml  
<ul class="toc simple" id="outline">  
<li><a class="reference internal" href="./sequences.html" id="id1393">Sequences</a><ul class="toc" id="outline">  
<li><a class="reference internal" href="./concepts.html" id="id1394">Concepts</a><ul class="toc" id="outline">  
```  
  
You cannot have multiple elements with the same `id` attribute.  
  
```xml  
<table border="1" class="docutils table" id="base">  
<span id="second"></span><span id="first"></span><colgroup>  
<col width="39%" />  
<col width="61%" />  
</colgroup>  
<thead valign="bottom">  
```  
You can't have a `<span>` as a child of `<table>`.  
  
  
These validation errors cause problems for packagers, as invalid XML is a [potential security risk](https://cwe.mitre.org/data/definitions/112.html).  
  
Either don't pretend the files are XML (e.g. change the doctype to HTML 5), or validate them and fix the errors please.

---

## Comment 1

> Username: jeking3  
> Created at: 2022-05-01 21:26:48 UTC  
> Url: https://github.com/boostorg/mpl/issues/59#issuecomment-1114340859  

@jwakely did you resolve all the issues with your PR?

---

## Comment 2

> Username: jwakely  
> Created at: 2022-05-02 07:35:14 UTC  
> Url: https://github.com/boostorg/mpl/issues/59#issuecomment-1114579094  

I think I fixed all the problems at the time. There might be new ones introduced since then, I haven't checked.
