# #342 - [engine] support per target env vars setup. [Open]

> Username: grafikrobot  
> Created at: 2018-09-26 07:36:24 UTC  
> Updated at: 2021-06-26 03:09:55 UTC  
> Url: https://github.com/boostorg/build/issues/342  

We often need to set, or clear, environment variables for invoking target actions. Add an engine feature to be able to set those per target variables:  
- Add special `ENVIRON` per target variable that is composed of a list of _name_ and _value_ pairs. The _value_ can be an empty string to indicate that the _name_ env variable should be unset.  
- When the action for a target runs the each _name_ and _value_ in the `ENVIRON` variable is set for the duration of the action invocation.

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-09-26 16:15:59 UTC  
> Url: https://github.com/boostorg/build/issues/342#issuecomment-424777999  

AMDG  
  
On 09/26/2018 01:36 AM, Rene Rivera wrote:  
> We often need to set, or clear, environment variables for invoking target actions. Add an engine feature to be able to set those per target variables:  
> - Add special `ENVIRON` per target variable that is composed of a list of _name_ and _value_ pairs. The _value_ can be an empty string to indicate that the _name_ env variable should be unset.  
  
Meaning "name1" "value1" "name2" "value2"  
  
For some uses "name1=value1", "name2=value2" may be more convenient.  
  
In particular, consider interaction with toolset.flags:  
  
 # Only works with name=value  
 flags xxx.yyy ENVIRON : <environ> ;  
 # This works either way, but the first appears more clear  
 # to someone reading the code.  
 flags xxx.yyy ENVIRON <threading>multi : THREADING=ON ;  
 flags xxx.yyy ENVIRON <threading>multi : THREADING ON ;  
  
and that `set` and `env` print the environment  
in NAME=VALUE format, which makes processing  
the msvc setup scripts more convenient if we  
use NAME=VALUE.  
  
> - When the action for a target runs the each _name_ and _value_ in the `ENVIRON` variable is set for the duration of the action invocation.  
>   
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: grafikrobot  
> Created at: 2018-09-26 16:31:23 UTC  
> Url: https://github.com/boostorg/build/issues/342#issuecomment-424783528  

Oh, yes, I can see how `NAME=VALUE` would be a better syntax. There's less corner cases to deal with in the native code side also. Although it does adds some string manipulations in the engine. But I thinks it's minimal enough for gain in convenience.

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-06-26 03:09:21 UTC  
> Url: https://github.com/boostorg/build/issues/342#issuecomment-868936411  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
