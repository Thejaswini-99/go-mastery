

Structs:

Structs also known as structures Structs are user-defined composite data types that group related fields of different data types into a single type.

type Person struct{
    Name string
    Age int
}

we can access the struct variables by using creating the object to it 
var pers1 Person
now we can assign like 
pers1.Name="thejaswini"                      
pers1.Age=25

OR

p := Person{
    Name: "Tejaswini",
    Age:  25,
}

if we want to access them we can do the same way like print(pers1.Name)
Passing Struct as Function Argument :-

PrintPerson(pers1)
func PrintPerson(pers Person){
    fmt.Println(pers.Name)
    fmt.Pritln(pers.Age)
}

Anonymous Structs:
    Anonymus structs are un-named ,temporary structures used for one-time purpose

     Person:= struct{
        Name string
        Age int
    }{
        Name:"Thejaswini",
        Age:"25",
    }


Nested Structs:
        Structs defined inside of another Structs

Ex:
    type Address struct{
        City string
    }
    type Person struct{
        Name string
        Age int
        Address Adress
    }

    you can access them using Person.Address.City


Struct Tags:
        Struct tags provide metadata used by packages like encoding/json.

Name string `json:"Name"`    


Zero Values: if we just intialise the struct the  values of the struct by default it would be " " for string and 0 for int


Interview Questions:
What is a struct?
Why use structs instead of maps?
What are zero values in a struct?
What are struct tags?
How do you initialize a struct?
Can a struct contain another struct?