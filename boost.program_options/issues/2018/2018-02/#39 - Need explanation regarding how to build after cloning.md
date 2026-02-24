# #39 - Need explanation regarding how to build after cloning [Open]

> Username: eyalroz  
> Created at: 2018-02-09 22:09:25 UTC  
> Updated at: 2018-09-13 04:04:18 UTC  
> Url: https://github.com/boostorg/program_options/issues/39  

Building program_options as part of a boost distribution is easy. But if I clone this repository and want to build the latest version - how do I do that?  
  
Either a README.md should explain this, or if you don't want to "pollute" the hierarchy with that file - at least a Wiki page here on GitHub, or a title link to somewhere explaining how to do it.

---

## Comment 1

> Username: eyalroz  
> Created at: 2018-02-16 21:37:45 UTC  
> Url: https://github.com/boostorg/program_options/issues/39#issuecomment-366365436  

ping.

---

## Comment 2

> Username: jeking3  
> Created at: 2018-03-06 14:27:52 UTC  
> Url: https://github.com/boostorg/program_options/issues/39#issuecomment-370798269  

program_options depends on a number of other boost libraries and are released together; none of the boost libraries are meant for individual upgrade relative to the others they depend on.  It is a versioned suite; so I would recommend that you build the suite.  
  
You could check out the boost superproject and submodules, then refresh libs/program_options to the latest master or develop, and attempt to build it from there.  If there are no interface changes in dependent modules then it might work.

---

## Comment 3

> Username: eyalroz  
> Created at: 2018-03-06 15:33:36 UTC  
> Url: https://github.com/boostorg/program_options/issues/39#issuecomment-370820658  

@jeking3 : I do realize this, but a person who "just" wants `program_options` and visits its GitHub page does not automatically know this, nor does s/he know how to do a clone-everything-then-arrange-it-so-only-a-submodule-is-forked. Which is why I suggest putting some instructions to that effect in `README.md`, or at least a link.

---

## Comment 4

> Username: jeking3  
> Created at: 2018-03-07 11:50:45 UTC  
> Url: https://github.com/boostorg/program_options/issues/39#issuecomment-371114612  

Here's a list of the complete dependencies boost::program_options has (direct AND indirect):  
```  
$ python tools/boostdep/depinst/depinst.py program_options --include example  
Installing module function  
Installing module core  
Installing module static_assert  
Installing module tokenizer  
Installing module iterator  
Installing module timer  
Installing module bind  
Installing module detail  
Installing module optional  
Installing module throw_exception  
Installing module type_traits  
Installing module smart_ptr  
Installing module regex  
Installing module lexical_cast  
Installing module config  
Installing module any  
Installing module preprocessor  
Installing module move  
Installing module system  
Installing module type_index  
Installing module concept_check  
Installing module math  
Installing module io  
Installing module array  
Installing module utility  
Installing module container  
Installing module predef  
Installing module numeric/conversion  
Installing module mpl  
Installing module conversion  
Installing module assert  
Installing module fusion  
Installing module integer  
Installing module function_types  
Installing module chrono  
Installing module range  
Installing module typeof  
Installing module container_hash  
Installing module intrusive  
Installing module tuple  
Installing module winapi  
Installing module ratio  
Installing module algorithm  
Installing module lambda  
Installing module atomic  
Installing module unordered  
Installing module rational  
Installing module exception  
```  
  
So certainly, you could extract the library by itself and then try to build it, but you would need all of these just to resolve the include chains.  Therefore I'll go back to what I said before:  
  
    none of the boost libraries are meant for individual upgrade relative to the others they depend on. It   
    is a versioned suite  
  
If you want to build the develop branch you should do so as part of the entire suite.  Downloading and building the suite has documentation.  As such, I would recommend closing this issue.

---

## Comment 5

> Username: mossheim  
> Created at: 2018-09-13 04:04:18 UTC  
> Url: https://github.com/boostorg/program_options/issues/39#issuecomment-420876462  

FWIW, I think all of what you have just said in this last comment should be in the readme for this project; speaking as another person who was interested in contributing to your library, I had to come here to find it out. A brief "contributing" section with a link or two would be golden.
