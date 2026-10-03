# JSON in Go

## 1. What is JSON?

`JSON = JavaScript Object Notation`

JSON is a data format used to represent and exchange data between different systems and applications.

Example:

```json
{
  "name": "Tejaswini",
  "age": 29,
  "isEmployed": true
}
```

JSON is:

- Not a database
- Not a programming language
- A data representation/exchange format

Common flow:

```text
Frontend
   ↓
  JSON
   ↓
Backend
```

And:

```text
Backend
   ↓
  JSON
   ↓
Frontend
```

---

## 2. JSON Building Blocks

### Object

`{}` represents an object.

```json
{
  "name": "Tejaswini",
  "age": 29
}
```

An object contains key-value pairs:

- `"name"` → `"Tejaswini"`
- `"age"` → `29`

### String

Strings are inside double quotes.

```json
"name": "Tejaswini"
```

`"Tejaswini"` is a string.

### Number

Numbers do not have quotes.

```json
"age": 29
```

This is a number.

Whereas:

```json
"age": "29"
```

is a string.

### Boolean

JSON supports:

- `true`
- `false`

Example:

```json
"isEmployed": true
```

### Null

`null` represents no value.

```json
"middleName": null
```

### Array

`[]` represents an array or list.

```json
"skills": ["Go", "SQL", "Redis"]
```

Array elements have indexes:

- Go → 0
- SQL → 1
- Redis → 2

Arrays can also contain objects:

```json
{
  "employees": [
    {
      "name": "Tejaswini",
      "age": 29
    },
    {
      "name": "Rahul",
      "age": 30
    }
  ]
}
```

---

## 3. Nested Objects

An object can contain another object.

```json
{
  "name": "Tejaswini",
  "address": {
    "city": "Bengaluru",
    "country": "India"
  }
}
```

Here:

```text
address
   ↓
Object
   ↓
city
country
```

Important:

- `"address"` = key
- `{ "city": "...", "country": "..." }` = value of `address`, which is another object

This is called a nested object.

You can have multiple levels:

```json
{
  "person": {
    "address": {
      "location": {
        "city": "Bengaluru"
      }
    }
  }
}
```

---

## 4. Go Struct → JSON

For this we use:

```go
encoding/json
```

Example:

```go
type Person struct {
    Name       string
    Age        int
    IsEmployed bool
}
```

Create a Go object:

```go
person := Person{
    Name:       "Tejaswini",
    Age:        29,
    IsEmployed: true,
}
```

---

## 5. json.Marshal()

### Meaning

Go Struct → JSON bytes

Example:

```go
data, err := json.Marshal(person)
```

`data` is:

```go
[]byte
```

To print it as a string:

```go
fmt.Println(string(data))
```

Output:

```json
{"Name":"Tejaswini","Age":29,"IsEmployed":true}
```

Flow:

```text
Go Struct
    ↓
json.Marshal()
    ↓
[]byte
    ↓
string(data)
```

---

## 6. JSON Tags

Suppose:

```go
type Person struct {
    Name       string `json:"name"`
    Age        int    `json:"age"`
    IsEmployed bool   `json:"is_employed"`
}
```

Tags tell Go which JSON key corresponds to each struct field.

Mapping:

| Go field      | JSON key            |
| ------------- | ------------------- |
| `Name`        | `"name"`           |
| `Age`         | `"age"`            |
| `IsEmployed`  | `"is_employed"`    |

So:

```go
Name string `json:"name"`
```

can produce:

```json
"name": "Tejaswini"
```

Go field names and JSON keys do not need to be the same.

Example:

```go
type Person struct {
    Firname string `json:"firdt_name"`
}
```

External JSON:

```json
{
  "firdt_name": "Tejaswini"
}
```

is completely acceptable.

Mapping:

```text
Firname  ←→  "firdt_name"
```

---

## 7. JSON → Go Struct

For this we use:

```go
json.Unmarshal()
```

### Meaning

JSON bytes → Go Struct

Example:

```go
jsonData := []byte(`{
    "name": "Tejaswini",
    "age": 29,
    "is_employed": true
}`)
```

Struct:

```go
type Person struct {
    Name       string `json:"name"`
    Age        int    `json:"age"`
    IsEmployed bool   `json:"is_employed"`
}
```

Create variable:

```go
var person Person
```

Then:

```go
e := json.Unmarshal(jsonData, &person)
```

After this:

```go
person.Name       = "Tejaswini"
person.Age        = 29
person.IsEmployed = true
```

### Why `&person`?

Because `Unmarshal` has to modify/fill the `person` variable.

So we give its address:

```go
&person
```

Think:

```text
JSON
 ↓
Unmarshal
 ↓
&person
 ↓
fill values into person
```

---

## 8. Marshal vs Unmarshal

Very important:

```text
Marshal:
Go Struct
    ↓
   JSON
```

```text
Unmarshal:
JSON
    ↓
Go Struct
```

Remember:

- `Marshal = Go → JSON`
- `Unmarshal = JSON → Go`

---

## 9. Encoder

`Encoder` is useful when we want to directly encode JSON into a writer/destination.

```go
encoder := json.NewEncoder(writer)
err := encoder.Encode(person)
```

### First line

```go
encoder := json.NewEncoder(writer)
```

Means: create an `Encoder` associated with this writer.

```text
writer
   ↓
NewEncoder(writer)
   ↓
encoder
```

### Second line

```go
encoder.Encode(person)
```

Means: convert `person` into JSON and write it directly to the writer.

```text
person
   ↓
Encode(person)
   ↓
JSON
   ↓
writer
```

Simple meaning:

- `writer` = where to write
- `encoder` = helper to write JSON
- `person` = the data to convert

---

## 10. Decoder

Decoder is the reverse of Encoder.

```go
decoder := json.NewDecoder(reader)
err := decoder.Decode(&person)
```

### Reader

`reader` tells us where the JSON data is coming from.

Examples can be:

- HTTP request body
- File
- Buffer

### First line

```go
decoder := json.NewDecoder(reader)
```

Means: create a `Decoder` that reads JSON from this reader.

### Second line

```go
decoder.Decode(&person)
```

Means: read JSON from the reader and fill the decoded values into `person`.

```text
reader
   ↓
JSON
   ↓
decoder.Decode(&person)
   ↓
Go Struct
```

Again, `&person` is used because `Decoder` has to modify/fill the variable.

---

## 11. Encoder vs Decoder

### Encoder

```text
Go object
   ↓
JSON
   ↓
Writer
```

### Decoder

```text
Reader
   ↓
JSON
   ↓
Go object
```

---

## 12. Marshal/Unmarshal vs Encoder/Decoder

Basic distinction:

- `Marshal`: Go → JSON bytes
- `Unmarshal`: JSON bytes → Go

Whereas:

- `Encoder`: Go → Writer/Stream
- `Decoder`: Reader/Stream → Go

For REST APIs, `Encoder` and `Decoder` are especially useful because HTTP request/response bodies behave like streams.

---

## 13. `omitempty`

In JSON tags, `omitempty` is used to skip fields with zero values.

```go
type Person struct {
    Name       string `json:"name"`
    Age        int    `json:"age,omitempty"`
    MiddleName string `json:"middle_name,omitempty"`
}
```

Suppose:

```go
person := Person{
    Name: "Tejaswini",
}
```

Then:

```text
Age        = 0
MiddleName = ""
```

These are zero values.

### Without `omitempty`

```json
{
  "name": "Tejaswini",
  "age": 0,
  "middle_name": ""
}
```

### With `omitempty`

```json
{
  "name": "Tejaswini"
}
```

### Meaning

If the field has its zero value, omit it from the JSON output.

Common zero values:

- `string` → `""`
- `int` → `0`
- `bool` → `false`
- pointer → `nil`












{
  "employee_id": 101,
  "name": "Tejaswini",
  "is_active": true,
  "email": "tejaswini@example.com",
  "skills": ["Go", "SQL", "Redis"],
  "address": {
    "city": "Bengaluru",
    "country": "India"
  },
  "manager": {
    "name": "Rahul",
    "email": "rahul@example.com"
  },
  "phone": null
}


Employee:

{
    employee:101
    name:Tejaswini
    is_active:true
    email:tejaswini@example.com
    skills:["Go", "SQL", "Redis"]
    address:{city:Bengaluru,country:India}
    manager:{name:Rahul,email:rahul@example.com}
    phone: null
}

{
    Employee int `json:"employee_id"`
    Name string `json:"name"`
    IsActive bool `json:"is_active"`
    Email string `json:"email"`
    Skills []string `json:"skills"`
    ManagerObj Manager `json:"manager"`
    Phone *string `json:"phone,omitempty"`
}
type Manager struct{
    Name string `json:"name"`
    Email string `json:"email`
}