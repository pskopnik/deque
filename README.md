# genny-deque

This repository contains a generics-enabled version of
[gammazero/deque][gammazero/deque], an extremely fast ring-buffer deque
([double-ended queue][wikipedia-deque]) implementation.

By using [cheekybits/genny][genny], concrete deque implementations for most data
types can be generated. The generated implementation uses the concrete data type
in all method signatures as well as for the underlying storage (a slice:
`[]YourType`). Static (compile-time) type-safety avoids panics at runtime and
leads to simpler calling code as well as editor typeahead support. More compiler
optimisations can be applied, improving performance and efficiency.

[genny]: https://github.com/cheekybits/genny
[gammazero/deque]: https://github.com/gammazero/deque
[wikipedia-deque]: https://en.wikipedia.org/wiki/Double-ended_queue

## Generating Deque Implementations

**Prerequisites:**

 1. [cheekybits/genny][genny] is installed and the `genny` binary is available
    in the PATH.

    ```bash
    go get github.com/cheekybits/genny
    ```
 2. This genny-deque repository has been cloned to a `genny-deque`
    sub-directory.

    ```bash
    git clone https://github.com/pskopnik/genny-deque.git
    ```

All the following commands generate a deque implementation which stores elements
of type `MyJobType`:

```bash
# Print implementation to stdout
genny -in=genny-deque/deque.go gen "ValueType=MyJobType"
# Write implementation to file
genny -in=genny-deque/deque.go -out=myjobtypedeque_gen.go gen "ValueType=MyJobType"
# Customise package
genny -in=genny-deque/deque.go -out=myjobtypedeque_gen.go -pkg=mypkgname gen "ValueType=MyJobType"
```

## Integrating into a Project's Repository

genny-deque can be integrated cleanly into a project by leveraging the [`go
generate`][go-generate] mechanism. The mechanism allows generating deque
implementations for custom types using standard tooling, thus providing a clear
interface for developers. It also makes the generating process more easily
repeatable.

To achieve repeatability, the generic genny-deque code must be included in the
project's repository at a fixed version. The recommended way to achieve this is
by adding a git submodule.

```bash
# Init is only required to be run once for a repository
git submodule init
# Clone genny-deque and add as a submodule to the containing repository
git submodule add https://github.com/pskopnik/genny-deque.git
```

To update the generic genny-deque code, the submodule

```bash
# Change into the submodule repository
cd genny-deque
# Pull the latest commits
git pull
# Change back into the containing repository
cd ..
# Add the change (new commit) to the git index
git add genny-deque
```

Whenever changes to the submodules have been pushed, other contributors must
update their local submodules after pulling. See the [Pro Git Book: Submodules
section][git-submodules] for more information on how to work with submodules.

```bash
git submodule update
```

To integrate with `go generate`, the generating command has to be included in a
Go source file using a special comment. The comment can be added to any Go file
in the module. Recommended is either the file containing the custom type to be
stored by the deque or a file solely containing the comment. For more
information on `go generate` see the related [blog post][go-generate].

For example, to create a deque implementation storing elements of type
`MyJobType`, the following file `myjobtypedeque.go` can be created:

```golang
package mypkgname

//go:generate genny -in=genny-deque/deque.go -out=myjobtypedeque_gen.go -pkg=mypkgname gen "ValueType=MyJobType"
```

To generate the deque implementation, run `go generate`:

```bash
go generate
```

[go-generate]: https://blog.golang.org/generate
[git-submodules]: https://git-scm.com/book/en/v2/Git-Tools-Submodules

## License

genny-deque is licensed under the MIT License (see `LICENSE`).
[gammazero/deque][gammazero/deque] is licensed under the MIT License as well
(see `LICENSE.deque`).

## Contributing

You are welcome to contribute to genny-deque through its [GitHub][genny-deque]
repository, either by creating an issue, or by proposing code changes through
the Pull Request system.

Changes to the deque implementation should be proposed on the
[gammazero/deque][gammazero/deque] repository. Feel free to also create an issue
in this repository for tracking the status of the contribution.

[genny-deque]: https://github.com/pskopnik/genny-deque
