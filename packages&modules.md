# Go Packages & Modules — Notes

---

## 1. What is a Package?

A package is a collection of Go files in the same directory that share the same package name. Used to separate functionality.

```go
myapp/
  main.go       → package main
  utils/
    helper.go   → package utils
    math.go     → package utils
```

---

## 2. package main vs other packages

```go
package main  // entry point, must have func main()
package utils // reusable package, no func main() needed
```

- `package main` → compiler calls `func main()` first → starting point of program
- `package utils` → reusable functionality, no entry point

---

## 3. Exported vs Unexported Identifiers

```go
package utils

func Add(a, b int) int { ... }    // ✅ Exported → Capital letter → visible outside package
func helper(a, b int) int { ... } // ❌ Unexported → small letter → only within package
```

> Rule: Capital letter = exported, small letter = unexported

---

## 4. What is a Module?

```
Package → collection of Go files in same directory
Module  → collection of packages with a go.mod file
```

```
myapp/          ← module (has go.mod)
  go.mod
  main.go       ← package main
  utils/        ← package utils
    helper.go
  db/           ← package db
    connect.go
```

---

## 5. go mod init

```bash
go mod init github.com/thejaswini/myapp
```

- Creates `go.mod` file
- Contains module name, Go version, dependencies
- URL format ensures **uniqueness** across all developers

```
github.com/thejaswini/myapp  ← unique! ✅
github.com/john/myapp        ← different module! ✅
```

---

## 6. go.mod file

```
module github.com/thejaswini/myapp

go 1.21

require (
    github.com/gin-gonic/gin v1.9.1
    github.com/go-redis/redis v8.0.0
)
```

Contains:
- Module name
- Go version
- Dependencies and their versions

---

## 7. go.sum file

```
go.sum = checksum file
→ stores cryptographic hash of each dependency
→ ensures nobody tampered with the package!
```

```
go.mod  → WHAT dependencies and which VERSION
go.sum  → VERIFY dependencies weren't tampered with
```

---

## 8. go mod tidy

```bash
go mod tidy
```

Does two things:
1. **Adds** missing dependencies → packages used in code but not in go.mod
2. **Removes** unused dependencies → packages in go.mod but not used in code

> Think of it like cleaning your room — adds what's needed, removes what's not!

---

## 9. go get vs go install

```bash
# go get → adds library to your project (import in code)
go get github.com/gin-gonic/gin
→ adds gin to go.mod ✅

# go install → installs a CLI tool globally (run in terminal)
go install github.com/air-lang/air@latest
→ installs 'air' as a command you can run anywhere
```

```
go get     → library you IMPORT in code
go install → tool you RUN in terminal
```

---

## 10. Importing Packages

```go
import "fmt"                       // standard library → Go installation folder
import "github.com/gin-gonic/gin"  // external → go.mod + downloaded from GitHub
import "./utils"                   // local → current directory
```

### Import Alias
```go
import myformat "fmt"

myformat.Println("hello")  // same as fmt.Println ✅
```

Useful when two packages have the same name:
```go
import (
    "github.com/thejaswini/db"
    mydb "github.com/john/db"  // alias to avoid conflict!
)
```

### Blank Import
```go
import _ "github.com/lib/pq"  // runs init() only, can't use directly
```

Used for **side effects** — package registers itself via `init()` without being called directly.

---

## 11. init() function

```go
package db

func init() {
    connectToDatabase()  // runs automatically when package is imported!
}
```

- Runs **automatically** when package is imported
- Runs **before** `main()`
- A package can have **multiple** `init()` functions

**Order of execution:**
```
import package → init() runs → main() runs
```

---

## Summary

```
Package      → collection of files in same directory
Module       → collection of packages with go.mod
go mod init  → creates go.mod file
go mod tidy  → sync go.mod with actual imports
go.mod       → what dependencies and versions
go.sum       → verify dependencies not tampered
go get       → add library to project
go install   → install CLI tool globally
Capital      → exported (visible outside package)
small        → unexported (only within package)
init()       → runs automatically on import
_ import     → run init() only, no direct use
```