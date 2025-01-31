# What is nvCOMP?

nvCOMP is a CUDA library that features generic compression interfaces to enable developers to use high-performance GPU compressors and decompressors in their applications.

Example benchmarking results and a brief description of each algorithm are available on the [nvCOMP Developer Page](https://developer.nvidia.com/nvcomp).

## Version 2.3 Release

This minor release of nvCOMP introduces support for zStandard (https://github.com/facebook/zstd/blob/dev/doc/zstd_compression_format.md) and Deflate standards (https://datatracker.ietf.org/doc/html/rfc1951) .

Modified-CRC32 checksum support was added to the HLIF. This optional capability is intended for error detection in HLIF-compressed buffers, but is not targeted towards security.

This release includes the following performance improvements:
- ANS performance was significantly improved. This included a ~100% speedup in compression and a ~50% speedup in decompression. 
- The GDeflate high compression mode now has 30-40x faster compression throughput.

For 2.3, zStandard support is limited to decompression in the low-level interface. Deflate support is not included in the high level interface.

From version 2.3 onwards, the compression / decompression source code will not be released. We'll continue to maintain this Github for documentation and code sample purposes.

## Known issues
* Cascaded, GDeflate and Bitcomp decompressors can only operate on valid input data (data that was compressed using the same compressor). Other decompressors can sometimes detect errors in the compressed stream. 
* Cascaded ZSTD and Bitcomp batched decompression C APIs cannot currently accept nullptr for actual_decompressed_bytes or device_statuses values.
* The Bitcomp low-level batched decompression function is not fully asynchronous.
* ZSTD Compression is not supported
* HLIF is not available for Deflate or ZSTD

## Download
* New in 2.3, you can download the appropriate built binary packages from the [nvCOMP Developer Page](https://developer.nvidia.com/nvcomp)
* For linux, choose the package that corresponds to your CUDA toolkit version
* For Windows, CUDA toolkit 11.6 is required
* On linux, the package includes
```
include/ 
  nvcomp/ 
    // nvcomp API headers
  gdeflate/
    // Gdeflate CPU library headers
lib/
  libnvcomp.so
  // Other nvcomp libraries that are used internally by nvcomp's APIs
  libnvcomp_gdeflate_cpu.so // CPU library for gdeflate
bin/ 
  // benchmark scripts
```

## Requirements
* Pascal (sm60) or higher GPU architecture is required. Volta (sm70)+ GPU architecture is recommended for best results. 
* To compile using nvCOMP as a dependency, you need a compiler with C++ 11 support (e.g. GCC 4.4, Clang 3.3, MSVC 2017).
  * To use the packages provided for Linux, you'll need to compile translation units that will be linked against nvcomp using the old ABI, i.e. for GCC `-D_GLIBCXX_USE_CXX11_ABI=0`

## nvCOMP library API Descriptions

Please view the following guides for information on how to use the two APIs provided by the library. Each of these guides links to a compilable example for further reference. 
* [High-level Quick Start Guide](doc/highlevel_cpp_quickstart.md)
* [Low-level Quick Start Guide](doc/lowlevel_c_quickstart.md)

## GPU Benchmarking

GPU Benchmark source are included in the binary releases. Source code for the benchmarks is also provided here on Github to provide additional examples on how to use nvCOMP. For further information on how to execute the benchmarks, please view [Benchmarks Page](doc/Benchmarks.md)

## CPU compression examples

We provide some examples of how you might use CPU compression and GPU decompression or vice versa for LZ4 and GDeflate. These require some external dependencies, namely:
- [zlib](https://github.com/madler/zlib) for the GDeflate CPU compression example (`zlib1g-dev` on debian based systems)
- [LZ4](https://github.com/lz4/lz4) for the LZ4 CPU compression example (`liblz4-dev` on debian based systems)

The CPU example executables are:
```
gdeflate_cpu_compression {-f <input_file>}
lz4_cpu_compression {-f <input_file>}
lz4_cpu_decompression {-f <input_file>}
```

## Building CPU and GPU Examples, GPU Benchmarks provided on Github
To build only the examples, you'll need cmake >= 3.18 and an nvcomp artifact. Then, you can follow the following steps from the top-level of your clone of nvCOMP from Github
```
cmake .. -DCMAKE_PREFIX_PATH=<path_to_nvcomp_install>
```

The `path_to_nvcomp_install` is the directory where you extracted the nvcomp artifact.

To compile the benchmarks too, you can add `-DBUILD_BENCHMARKS=1`, but note this is only provided for an additional example of building against the artifacts. The benchmarks are already provided in the artifact `bin/` folder.

