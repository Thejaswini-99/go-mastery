
Arrays:

Arrays is a fixed size collection of elements and all the elements are stored sequentially 

one dimensional array:
var arr := [4]int{1,2,3,4}

Two Dimensional array:
arr := [2][4]int{
		{1, 2, 3, 4},
		{5, 6, 7, 8},
	}

-->Elements in the arrays can be accesed using the indexes 
---> the capacity and length of the array are same 

Slice:

Slice is also a collection of elements but the size is flexible
var s = []string{"Go", "TypeScript"}

-->slice consists of the pointer that points to the underlying array
---> in this capacity is the number of the elemnst it can hold

some built-in functions in the go :

copy: The copy() function copies elements from one slice to another. 	e := copy(s2, s1). where s1 and s2 are slices

append: The Append() function used to add the elemnts in the end of the slice. s2 := append(s1, "e", "f") where s1 is the slice and s2 is the new slice and e and f are the elements



Map:

 A map is an unordered collection of key-value pairs. It maps keys to values. The keys are unique within a map while the values may not be.

 var m = make(map[string]int)

 there is built in function to dlelte the elemst in the map delete(mapname,keyname)

 var m = map[string]int{
    "a":0,
    "b":1,
 }