# #48 - Friends should not appear unless private members are configured to be shown [Closed]

> Username: sdkrystian  
> Created at: 2020-10-21 15:58:29 UTC  
> Updated at: 2020-11-17 19:46:45 UTC  
> Closed at: 2020-11-17 19:46:45 UTC  
> Url: https://github.com/boostorg/docca/issues/48  

Private friend classes first declared outside of a class should not be emitted within the documentation,

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-10-21 16:00:51 UTC  
> Url: https://github.com/boostorg/docca/issues/48#issuecomment-713681658  

![image](https://user-images.githubusercontent.com/1503976/96746327-e9d6b680-137b-11eb-800e-da2486f76a04.png)

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-10-21 16:01:11 UTC  
> Url: https://github.com/boostorg/docca/issues/48#issuecomment-713681884  

The problem is that the friend appears in the class page even though it is in a detail namespace

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-10-21 16:02:19 UTC  
> Url: https://github.com/boostorg/docca/issues/48#issuecomment-713682599  

we should probably only show Friends if the configuration for showing private members is set

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-10-21 16:51:34 UTC  
> Url: https://github.com/boostorg/docca/issues/48#issuecomment-713713219  

There's another problem as well, the pages for the members which aren't supposed to be emitted in the first place are missing:  
```  
xslt-xsltproc-dir.windows example\html\json_HTML.manifest  
Error: no ID for constraint linkend: "docca.ref.example__issue_48.Y".  
Error: no ID for constraint linkend: "docca.ref.example__issue_48.g".  
...updated 24 targets...  
```  
  
This problem will be hidden once the original defect is fixed, but the problem will remain. For example you could turn on "show private members" in config.xsl and the links for Y and g will be broken.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-10-21 16:52:09 UTC  
> Url: https://github.com/boostorg/docca/issues/48#issuecomment-713713563  

Test case is merged   
https://github.com/boostorg/docca/commit/01b721239465031b4419311b70d6f60eea091e98

---

## Comment 6

> Username: evanlenz  
> Created at: 2020-11-02 05:26:56 UTC  
> Url: https://github.com/boostorg/docca/issues/48#issuecomment-720244179  

Are there any circumstances where we would want to show the "Friends" table when $include-private-members is _disabled_?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2020-11-02 17:52:27 UTC  
> Url: https://github.com/boostorg/docca/issues/48#issuecomment-720628068  

Yes, I think we always want to show the Friends table when a class has friends, because those friends are public and can be called, even if the "friend" declaration itself is in the "private" section. Access-control (public, protected, private keywords) does not affect friendship. Try making a separate Doxygen config file and emitting the HTML docs instead and see what Doxygen does. It uses a completely different set of parsing code for emitting HTML than it does for XML.
