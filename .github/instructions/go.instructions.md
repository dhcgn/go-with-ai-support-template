---
applyTo: "**/*.go,**/*.mod"
---

Overview of the Latest Go Features

# Go Version 1.24

Url: https://go.dev/doc/go1.24

- **Generic Type Aliases**: Type aliases can now be parameterized, enhancing code reuse and clarity.

- **Improved Map Performance**: Adoption of the Swiss Table algorithm leads to faster map operations.

- **Weak Pointers**: Introduction of `weak.Pointer` allows references that don't prevent garbage collection.

- **Finalizer Enhancements**: `runtime.AddCleanup` enables cleanup functions to run when objects become unreachable.

- **Tool Dependency Management**: `tool` directives in `go.mod` streamline tracking of executable dependencies.

- **Enhanced `go` Command**:
  - `-tool` flag for `go get` adds tool directives.
  - `tool` meta-pattern simplifies bulk tool operations.
  - Executables from `go run` are now cached for faster execution.
  - `-json` flag for `go build` and `go install` outputs structured JSON.
  - `go test -json` includes build output in JSON format.
  - `GOAUTH` environment variable introduced for private module authentication.
  - `go build` embeds main module version info into binaries.
  - `GODEBUG=toolchaintrace=1` traces toolchain selection.

- **New Packages**:
  - `crypto/hkdf`: Implements HKDF as per RFC 5869.
  - `crypto/pbkdf2`: Adds PBKDF2 key derivation function.
  - `crypto/sha3`: Provides SHA-3 hash functions.
  - `crypto/mlkem`: Introduces post-quantum KEM support.
  - `weak`: Offers weak pointer functionality.

- **Benchmarking Enhancements**: `testing.B.Loop` simplifies writing benchmarks by handling loop iterations.

- **Standard Library Updates**:
  - `bytes.Lines` and `strings.Lines` return iterators over lines.
  - `encoding/json` can omit zero values during marshaling.

- **Security and Compliance**: Updates align with FIPS 140-3 standards for cryptographic modules.

- **Testing Improvements**:
  - `testing/synctest` package facilitates testing with synthetic time.
  - Enhanced `go vet` checks for test declaration errors and stricter `printf` validations.

- **WebAssembly Support**: Expanded capabilities for WebAssembly targets.

- **Runtime Optimizations**: Achieves a 2-3% reduction in CPU overhead through various enhancements.


# Go Version 1.23

Url: https://go.dev/doc/go1.23

## Language Enhancements

- **Range over functions**: `for-range` loops can now iterate over functions with the following signatures:
  - `func(func() bool)`
  - `func(func(K) bool)`
  - `func(func(K, V) bool)`
  This enables custom iterator patterns, allowing more flexible iteration over user-defined data structures. 

- **Preview of generic type aliases**: Introduces experimental support for defining type aliases with type parameters. This feature is currently limited to within a single package and requires the `GOEXPERIMENT=aliastypeparams` environment variable to be set during compilation. 

## New Standard Library Packages

- **`iter`**: Provides foundational types and functions for working with iterators:
  - `Seq[V any] func(func(V) bool)`
  - `Seq2[K, V any] func(func(K, V) bool)`
  These types facilitate the creation and consumption of iterator-based sequences. 

- **`unique`**: Offers facilities for canonicalizing comparable values, effectively enabling interning.
  - `Make[T comparable](v T) Handle[T]` returns a handle to a canonical copy of the value.
  - `Handle[T]` instances can be compared efficiently using pointer equality. 

- **`structs`**: Introduces types that modify struct properties, such as memory layout.
  - `HostLayout` indicates that a struct's layout conforms to host platform expectations, which is crucial when interfacing with certain system APIs. 

## Enhancements to Existing Packages

### `slices` Package

- Added functions that operate on iterators:
  - `All`, `Values`, `Backward`, `Collect`, `AppendSeq`, `Sorted`, `SortedFunc`, `SortedStableFunc`, `Chunk`
  These functions provide versatile ways to manipulate and traverse slices using iterator patterns. 

### `maps` Package

- Introduced functions for iterator-based map operations:
  - `All`, `Keys`, `Values`, `Insert`, `Collect`
  These additions enable more expressive and efficient map manipulations. 

## Runtime and Garbage Collection

- **Timer and Ticker Improvements**:
  - Timers and tickers that are no longer referenced become eligible for garbage collection immediately, even if their `Stop` methods haven't been called.
  - The channels associated with timers and tickers are now unbuffered, ensuring that no stale values are sent or received after a `Reset` or `Stop` call. 

- **GODEBUG Settings**:
  - Introduced the `asynctimerchan=1` setting to revert to the previous asynchronous channel behavior for timers and tickers.
  - This setting allows developers to opt out of the new timer semantics if needed. 

## Toolchain Updates

- **Go Telemetry**:
  - An opt-in system that collects anonymous usage statistics to help the Go team understand toolchain usage and performance.
  - Controlled via the `go telemetry` command. 

- **`go` Command Enhancements**:
  - `go env -changed`: Displays environment settings that differ from the defaults.
  - `go mod tidy -diff`: Shows the changes that would be made by `go mod tidy` without applying them.
  - `go list -m -json`: Now includes `Sum` and `GoModSum` fields for modules. 

## Compiler and Linker

- **Compiler**:
  - Enhanced diagnostics and error messages for better developer experience.
  - Improved performance and reduced binary sizes in certain scenarios. 

- **Linker**:
  - Introduced the `-bindnow` flag for ELF binaries, enabling immediate function binding.
  - This can improve startup times and security by reducing the window for certain types of attacks. 

## Miscellaneous

- **`go vet` Enhancements**:
  - Now reports symbols that are too new for the intended Go version, helping maintain compatibility. 

- **Module System**:
  - Improved support for managing tool dependencies within modules.
  - Enhanced error messages and diagnostics related to module operations. 


# Go Version 1.22

Url:  https://go.dev/doc/go1.22  

## Language Changes

- Loops and closures: The iteration variable in `for` loops is now unique per iteration when used in closures, which avoids common bugs with loop variables captured by goroutines or functions.
- The predeclared `any` type is now a type alias for `interface{}`.
- The new `go` and `defer` statements now allow type parameter inference.
- `go` and `defer` statements allow the use of type parameters, supporting generics with goroutines and deferred functions.

## Toolchain

- The `go mod tidy` command is faster and produces more precise error messages.
- `go test -json` now supports richer output, including structured logs.
- The linker is faster and uses less memory, especially for large projects.
- The Go compiler now generates more efficient code in several cases, reducing binary size and improving performance.
- The `go` command now supports a `-overlay` flag for all module-aware commands, enabling the use of a file overlay to replace files without modifying them on disk.

## Core Packages

### bytes

- `bytes.Lines`: Returns an iterator over the newline-terminated lines in a byte slice.
- `bytes.FieldsFunc`: Performance improved for common cases.

### strings

- `strings.Lines`: Returns an iterator over the newline-terminated lines in a string.
- `strings.FieldsFunc`: Performance improved for common cases.

### maps

- New package: `maps`
  - `maps.Equal(m1, m2)`: Reports whether two maps contain the same key/value pairs.
  - `maps.Clone(m)`: Returns a copy of the given map.
  - `maps.Keys(m)`: Returns a slice of all the keys in the map.
  - `maps.Values(m)`: Returns a slice of all the values in the map.
  - `maps.Clear(m)`: Removes all entries from the map.

### slices

- `slices.Compact`: Removes consecutive duplicate elements in a slice.
- `slices.Clip`: Reduces the capacity of a slice to its length.
- `slices.DeleteFunc`: Removes all elements for which a predicate returns true.
- `slices.Replace`: Returns a copy of a slice with a range replaced by a replacement slice.
- `slices.Insert`: Returns a copy of a slice with elements inserted at a specific position.
- `slices.Max`, `slices.Min`: Return the maximum or minimum value in a slice.
- `slices.SortStableFunc`: Sorts a slice in a stable order using a comparator function.

### cmp

- New package: `cmp`
  - `cmp.Compare(a, b)`: Compares two values and returns -1, 0, or 1.
  - `cmp.Less(a, b)`: Reports whether `a` is less than `b` using standard comparison rules.

### cmpopts

- New package: `cmp/cmpopts`
  - Provides helper functions for working with the `cmp` package (for example, `EquateApprox`, `SortSlices`).

### errors

- `errors.Is` and `errors.As`: Now work recursively with error chains and provide more helpful error matching.

### context

- `context.WithDeadlineCause` and `context.WithTimeoutCause`: Set a deadline or timeout and allow setting a cause for cancellation.

### net/http

- The HTTP/2 client now supports the `PING` frame.
- New option in `http.Server`: `BaseContext` field, to control the context used for incoming connections.

### sync

- `sync.Pool`: Improved performance in highly concurrent workloads.

### runtime/metrics

- New metrics available, including Go routine counts, allocation stats, and CPU metrics.

### syscall/js

- Expanded WebAssembly (Wasm) support in the `syscall/js` package.

### testing

- `T.TempDir`: Creates a temporary directory for use in tests, which is automatically cleaned up.
- New testing flags: `-test.short` is respected in more places.
- `T.Setenv`: Sets an environment variable for the test and restores the original value afterward.

### os

- `os.DirFS`: Returns a file system (fs.FS) for a directory tree.
- `os.UserHomeDir`: Returns the current user's home directory.
- `os.ReadFile`, `os.WriteFile`: Utility functions to read/write entire files.

### path/filepath

- `filepath.WalkDir`: Improved error handling and performance.

### embed

- The `embed` package now better supports `//go:embed` directives for embedding files and directories at build time.

### io/fs

- New interfaces and improvements for working with file systems, such as `ReadDirFS`, `StatFS`, and others.

## Standard Library Enhancements

- Several core packages (`crypto`, `encoding`, `image`, etc.) have received minor improvements and bug fixes.
- Many deprecated features and APIs have been removed or marked for removal in future releases.

## Performance

- Garbage collector: Improved pause times and reduced memory usage.
- Scheduler: More responsive scheduling in high-concurrency scenarios.

## Compatibility

- Go 1.22 maintains the Go 1 compatibility promise: code written for earlier Go 1.x versions should continue to work.
- Some legacy APIs are deprecated, and developers are encouraged to migrate to newer alternatives.

## Security

- Improved cryptographic algorithms and safer defaults in the `crypto` packages.
- Enhanced defense-in-depth in runtime and standard library.

---

> **When developing with Go 1.22, consider these features and new packages to write efficient, safe, and modern Go code. Always refer to the official docs for more usage examples.**
