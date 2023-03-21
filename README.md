# Coditory - Go Errors
[![GitHub release](https://img.shields.io/github/v/release/coditory/go-errors.svg)](https://github.com/coditory/go-errors/releases)
[![Go Reference](https://pkg.go.dev/badge/github.com/coditory/go-errors.svg)](https://pkg.go.dev/github.com/coditory/go-errors)
[![Go Report Card](https://goreportcard.com/badge/github.com/coditory/go-errors)](https://goreportcard.com/report/github.com/coditory/go-errors)
[![Build Status](https://github.com/coditory/go-errors/workflows/Build/badge.svg?branch=main)](https://github.com/coditory/go-errors/actions?query=workflow%3ABuild+branch%3Amain)
[![Coverage](https://codecov.io/gh/coditory/go-errors/branch/main/graph/badge.svg?token=EPRs5LiPje)](https://codecov.io/gh/coditory/go-errors)

**🚧 This library as under heavy development until release of version `1.x.x` 🚧**

> Wrapper for Go errors that prints error causes with theis stack traces.

- Prints stacks traces from all of the causes
- Shortens file paths and function names for readability
- Supports and exports `errors.Is`, `errors.As`, `errors.Unwrap`

# Getting started

## Installation
Get the dependency with:
```sh
go get github.com/coditory/go-errors
```

and import it in the project:
```go
import "github.com/coditory/go-errors"
```

The exported package is `errors`, basic usage:
```go
import "github.com/coditory/go-errors"

func main() {
    err := foo()
    fmt.Println("Error with stack trace:")
    fmt.Println(errors.Format(err))

    stderr := fmt.Errorf("std error")
    fmt.Println("Go std error:")
    fmt.Println(errors.Format(stderr))
}

func foo() error {
    err := bar()
    return errors.Wrap(err, "foo failed")
}

func bar() error {
    return errors.New("bar failed")
}
```

Output:

```
Error with stack trace:
foo failed
    ./samples.go:34 main.foo
    ./samples.go:10 main.main
    go1.20.2/rc/runtime/proc.go:250 runtime.main
    go1.20.2/rc/runtime/asm_amd64.s:1598 runtime.goexit
caused by: bar failed
    ./samples.go:38 main.bar
    ./samples.go:33 main.foo
    ./samples.go:10 main.main
    go1.20.2/rc/runtime/proc.go:250 runtime.main
    go1.20.2/rc/runtime/asm_amd64.s:1598 runtime.goexit

Go std error:
std error
```

## Verbosity levels

Errors can be formatted with different verbosity levels with:

```go
fmt.Println(errors.Formatv(err), verbosity)

...or by changing the global verbosity level:
errors.Config.Verbosity = 4
```

The default verbosity level is 4.

Verbosity level samples generated with `go run ./samples`:
```
>>> Verbosity: 0
foo failed

>>> Verbosity: 1
foo failed
caused by: bar failed

>>> Verbosity: 2
foo failed
    main.foo:34
    main.main:10
    runtime.main:250
    runtime.goexit:1598
caused by: bar failed
    main.bar:38
    main.foo:33
    main.main:10
    runtime.main:250
    runtime.goexit:1598

>>> Verbosity: 3
foo failed
    ./samples.go:34
    ./samples.go:10
    go1.20.2/rc/runtime/proc.go:250
    go1.20.2/rc/runtime/asm_amd64.s:1598
caused by: bar failed
    ./samples.go:38
    ./samples.go:33
    ./samples.go:10
    go1.20.2/rc/runtime/proc.go:250
    go1.20.2/rc/runtime/asm_amd64.s:1598

>>> Verbosity: 4 (DEFAULT)
foo failed
    ./samples.go:34 main.foo
    ./samples.go:10 main.main
    go1.20.2/rc/runtime/proc.go:250 runtime.main
    go1.20.2/rc/runtime/asm_amd64.s:1598 runtime.goexit
caused by: bar failed
    ./samples.go:38 main.bar
    ./samples.go:33 main.foo
    ./samples.go:10 main.main
    go1.20.2/rc/runtime/proc.go:250 runtime.main
    go1.20.2/rc/runtime/asm_amd64.s:1598 runtime.goexit

>>> Verbosity: 5
foo failed
    ./samples.go:34
        main.foo
    ./samples.go:10
        main.main
    go1.20.2/rc/runtime/proc.go:250
        runtime.main
    go1.20.2/rc/runtime/asm_amd64.s:1598
        runtime.goexit
caused by: bar failed
    ./samples.go:38
        main.bar
    ./samples.go:33
        main.foo
    ./samples.go:10
        main.main
    go1.20.2/rc/runtime/proc.go:250
        runtime.main
    go1.20.2/rc/runtime/asm_amd64.s:1598
        runtime.goexit

>>> Verbosity: 6
foo failed
    /Users/mendlik/Development/go/go-errors/samples/samples.go:34
        main.foo
    /Users/mendlik/Development/go/go-errors/samples/samples.go:10
        main.main
    /Users/mendlik/.sdkvm/sdk/go/1.20.2/src/runtime/proc.go:250
        runtime.main
    /Users/mendlik/.sdkvm/sdk/go/1.20.2/src/runtime/asm_amd64.s:1598
        runtime.goexit
caused by: bar failed
    /Users/mendlik/Development/go/go-errors/samples/samples.go:38
        main.bar
    /Users/mendlik/Development/go/go-errors/samples/samples.go:33
        main.foo
    /Users/mendlik/Development/go/go-errors/samples/samples.go:10
        main.main
    /Users/mendlik/.sdkvm/sdk/go/1.20.2/src/runtime/proc.go:250
        runtime.main
    /Users/mendlik/.sdkvm/sdk/go/1.20.2/src/runtime/asm_amd64.s:1598
        runtime.goexit
```
