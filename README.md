Readme
======

A personal experiment to evaluate the extent of [go]'s cross compilation
capabilities in the context of the [nix] package manager reproducible
environment.

[go]: https://golang.org/
[nix]: https://nixos.org/guides/how-nix-works.html

## Static build targetting host platform


### Dynamically linked to libc

Required nix env:

```bash
$ cd ./hello-dynamic
$ nix-shell -I nixpkgs=../nixpkgs_root -p libcap go gcc
# ..
```

```bash
$ go build hello.go

$ ./hello
hello world

$ ldd ./hello
        not a dynamic executable
```

This is because go create static binaries by default as long as
`cgo` is not used. See below references for more details.


### Statically linked to lib (or no libc?)

Required nix env:

```bash
$ cd ./hello-static
$ nix-shell -I nixpkgs=../nixpkgs_root -p libcap go gcc glibc.static
# ..
```

Then, build and run:

```bash
$ go build -ldflags "-s -w -linkmode external -extldflags -static" hello.go 

$ ./hello 
hello world
```

See that effectively does not require any dynamic libs.

```bash
$ ldd ./hello
        not a dynamic executable
```

## References

 -  [Go - NixOS Wiki](https://nixos.wiki/wiki/Go)

 -  [Statically compiling Go programs](https://www.arp242.net/static-go.html)

     >  Go creates static binaries by default unless you use cgo to call C code,
     >  in which case it will create a dynamically linked library. Turns out
     >  that using cgo is more common than many people assume as the os/user and
     >  net packages use cgo, so importing either (directly or indirectly) will
     >  result in a dynamic binary.
