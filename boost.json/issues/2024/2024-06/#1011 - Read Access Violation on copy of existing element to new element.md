# #1011 - Read Access Violation on copy of existing element to new element [Closed]

> Username: streetjimmothy  
> Created at: 2024-06-12 17:42:10 UTC  
> Updated at: 2024-10-09 13:44:44 UTC  
> Closed at: 2024-10-09 13:44:44 UTC  
> Url: https://github.com/boostorg/json/issues/1011  

### Version of Boost  
1_84  
  
### Steps necessary to reproduce the problem  
Expected Behaviour: assigning an existing property on a JSON to a key that does not exist should work the same as when the assignment is made to an existing key, or when any other value is assigned to a key that does not exist.  
Actual Behaviour: read access violation   
Case 1:  
object["existing_key"] = object["old_key"];	//works  
Case 2:  
object["new_key"] = "new_value";			//works  
Case 3:  
object["new_key"] = object["old_key"];		//read access violation   
  
### Call stack & Error details:  
Exception thrown: read access violation.  
**this** was 0xFFFFFFFFFFFFFFFF.  
boost_json-vc143-mt-gd-x64-1_84.dll!boost::container::pmr::memory_resource::is_equal(const boost::container::pmr::memory_resource & other) Line 59  
boost_json-vc143-mt-gd-x64-1_84.dll!boost::container::pmr::operator==(const boost::container::pmr::memory_resource & a, const boost::container::pmr::memory_resource & b) Line 66  
boost_json-vc143-mt-gd-x64-1_84.dll!boost::json::value::swap(boost::json::value & other) Line 390  
boost_json-vc143-mt-gd-x64-1_84.dll!boost::json::value::operator=(const boost::json::value & other) Line 268

---

## Comment 1

> Username: streetjimmothy  
> Created at: 2024-06-12 17:45:42 UTC  
> Url: https://github.com/boostorg/json/issues/1011#issuecomment-2163593440  

As I was writing the above, I discovered that you don't get the error if _any_ new element is assigned prior to the copy.  
This works:  
object["new_key"] = "new_value";   
object["another_new_key"] = object["existing_key"];  
This does not:  
object["another_new_key"] = object["existing_key"]; //read access violation  
object["new_key"] = "new_value";

---

## Comment 2

> Username: grisumbras  
> Created at: 2024-06-12 17:51:35 UTC  
> Updated at: 2024-06-12 17:59:21 UTC  
> Url: https://github.com/boostorg/json/issues/1011#issuecomment-2163603533  

Please provide a minimal complete verifiable example, so that I can attempt to reproduce the error. As is, I tried locally, and the error you are describing doesn't manifest.

---

## Comment 3

> Username: streetjimmothy  
> Created at: 2024-06-12 18:04:09 UTC  
> Url: https://github.com/boostorg/json/issues/1011#issuecomment-2163623903  

Thanks for the quick response; I'll whip one up in the next few days

---

## Comment 4

> Username: grisumbras  
> Created at: 2024-07-26 07:22:57 UTC  
> Url: https://github.com/boostorg/json/issues/1011#issuecomment-2252133564  

Any updates?

---

## Comment 5

> Username: grisumbras  
> Created at: 2024-10-09 13:44:44 UTC  
> Url: https://github.com/boostorg/json/issues/1011#issuecomment-2402394100  

Since no updates followed, I'm closing this.
