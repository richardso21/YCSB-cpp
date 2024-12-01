# YCSB-cpp

Yahoo! Cloud Serving Benchmark([YCSB](https://github.com/brianfrankcooper/YCSB/wiki)) written in C++.
This is a fork of [YCSB-C](https://github.com/basicthinker/YCSB-C) with some additions

 * Tail latency report using [HdrHistogram_c](https://github.com/HdrHistogram/HdrHistogram_c)
 * Modified the workload more similar to the original YCSB
 * Supported databases: LevelDB, RocksDB, LMDB, WiredTiger, SQLite

> **Additionally, this repository is an extension of the original
[YCSB-cpp](https://github.com/ls4154/YCSB-cpp) project, now supporting a few
more databases (e.g. Speedb, TerarkDB, UnQLite, etc.) and including instructions
to build and install DBs from source (locally, without root).**
>
> _**This specific fork serves as a portion of an end-of-semester project for
Georgia Tech's CS 6400 Databases course**_

# Build YCSB-cpp

## Prerequisites

  * Clone this repository, recursing to all submodules
  ```
  git clone --recurse-submodules git@github.com:richardso21/YCSB-cpp.git
  ```

  * Ensure you have `gcc` and `cmake` installed

### Installing LevelDB

  * LevelDB is included as a submodule in this repository. To build it, run the following commands:
  ```
  cd YCSB-cpp/third-party/leveldb
  mkdir build && cd build

  # to install globally...
  cmake -DCMAKE_BUILD_TYPE=Release ..

  # or, to install locally (e.g. if you are on PACE)
  cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=~/.local ..

  make install
  ```

  * **If you installed locally**, you will need to add the following to your
  `.bashrc` or `.bash_profile` (or `zsh` equivalents):
  ```
  export PATH=$PATH:~/.local/bin
  export CPATH=$CPATH:~/.local/include
  export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:~/.local/lib
  ```
> _LevelDB is included in order to provide a reference point for the original
`YCSB` implementation in Java._

## Build with Makefile on POSIX

Initialize submodule and use `make` to build.

```
git clone https://github.com/ls4154/YCSB-cpp.git
cd YCSB-cpp
git submodule update --init
make
```

Databases to bind must be specified as build options. You may also need to add additional link flags (e.g., `-lsnappy`).

To bind LevelDB:
```
make BIND_LEVELDB=1
```

To build with additional libraries and include directories:
```
make BIND_LEVELDB=1 EXTRA_CXXFLAGS=-I/example/leveldb/include \
                    EXTRA_LDFLAGS="-L/example/leveldb/build -lsnappy"
```

Or modify config section in `Makefile`.

RocksDB build example:
```
EXTRA_CXXFLAGS ?= -I/example/rocksdb/include
EXTRA_LDFLAGS ?= -L/example/rocksdb -ldl -lz -lsnappy -lzstd -lbz2 -llz4

BIND_ROCKSDB ?= 1
```

## Build with CMake on POSIX

```shell
git submodule update --init
mkdir build
cd build
cmake -DBIND_ROCKSDB=1 -DBIND_WIREDTIGER=1 -DBIND_LMDB=1 -DBIND_LEVELDB=1 -DWITH_SNAPPY=1 -DWITH_LZ4=1 -DWITH_ZSTD=1 ..
make
```

## Build with CMake+vcpkg on Windows

see [BUILD_ON_WINDOWS](BUILD_ON_WINDOWS.md)

## Running

Load data with leveldb:
```
./ycsb -load -db leveldb -P workloads/workloada -P leveldb/leveldb.properties -s
```

Run workload A with leveldb:
```
./ycsb -run -db leveldb -P workloads/workloada -P leveldb/leveldb.properties -s
```

Load and run workload B with rocksdb:
```
./ycsb -load -run -db rocksdb -P workloads/workloadb -P rocksdb/rocksdb.properties -s
```

Pass additional properties:
```
./ycsb -load -db leveldb -P workloads/workloadb -P rocksdb/rocksdb.properties \
    -p threadcount=4 -p recordcount=10000000 -p leveldb.cache_size=134217728 -s
```
