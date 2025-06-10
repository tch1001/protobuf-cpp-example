# Protobuf C++ Example

This is written to accompany the [official docs](https://protobuf.dev/getting-started/cpptutorial/). 

```bash
git clone github.com/tch1001/protobuf-cpp-example
cd protobuf-cpp-example
git submodule update --init --recursive

# install abseil
pushd third_party/abseil-cpp # already checked out 20250127.0 
mkdir build && cd build
cmake ..
cmake --build . -j$(nproc)
sudo make install
popd

# install protobuf
pushd third_party/protobuf
mkdir build && cd build 
cmake ..
cmake --build . -j$(nproc)
sudo make install
popd

# compile demo
mkdir build && cd build 
cmake .. 
cmake --build . -j$(nproc)
./write phonebook.bin
```