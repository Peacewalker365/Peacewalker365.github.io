# Golang Note

# Golang

why golang

c/c++ fast but have to carry historical burdens, slow compile

java kinda same

python, easy to use but slow.

None of these are developed toward to the best performance in terms of concurrency



- Strong and staticaaly typed
- excellent comm.
- Key features
  - simplicity
  - fast compile times
  - garbage collected
  - compile to standaline binaries(all dependencies are in there)
  - built-in concurrency

```go
package main // have to specify the package name

import (
	"fmt"
)

func main() {
	fmt.Println("Hello, playground")
}

```



## Set up the env

if you don't install it in the default route, you may want to set the GOROOT and GOPATH in the bash file.

GOROOT is where you golang are installed.

GOPATH is where your files and binaries are located.

But you can set up 2 GOPATH, the first one is going to be used for `go get` to hold all files or 3rd party go libs, while both are searched for source code. So it helps us to set up the workspace.

In a workspace, we have src for sourec code, we have bin for binaries, and pkg for intermediate binaries liked some 3rd party libs you want to integrated into your project.



## Complile

```go
go run src/github.com/PceWlkr/fisrtapp/Main.go

go build github.com/PceWlkr/firstapp // use the package addr. it will look into it if there is a main.go
./firstapp

go install github.com/PceWlkr/firstapp
./bin/firstapp
```

#### Notice there might be some changes since the build and install are not working for me with the package path.



## Variables

```go
package main

import(
				"fmt"
)

var i int 42 // you have to use this syntax if you declare it on package level

var I int 54 // upper case var declared at package level is exposed to the ouside of the package while lower case vars declared at package level stays in the package scope

var (
			str string = "my name"
  		num int = 3
)

func main(){
  
  var o int // sometime you don't want a init, and this is in the block scope
  o = 42
  // or
  var j float32 = 27.
  // or
  k := 99
  i := 1 // error: you cannot delcare the same var in the same scope for twice
  i = 1 // this is OK
  var i int = 22 // This is also OK, but the package level i is hidden now, we can only the local i here
  var p int = 0; // this will throw a error, you cannot just declare it without using in your code
  
  fmt.Println(i)
  fmt.Printf("%v, %T, %T", j, j, k) // will print 27, float32, float64
}


```



```go
var i int = 42
var j float32
j = float32(i)


var i int = 42
var j string
j = string(i) // the j here will be *
							// since it will look up the unicode for 42 and it's *

j = strconv.Itoa(i) // we need to use this to convert int to string you want


```




