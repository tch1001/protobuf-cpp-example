# Protobuf C++ Example

This is written to accompany the [official docs](https://protobuf.dev/getting-started/cpptutorial/). 

At the time of writing, I couldn't find a modern (protobuf v31.1) example.

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
./writing phonebook.bin
# follow prompts to add some entries in phonebook
hexdump phonebook.bin
./reading phonebook.bin
```

For development, 

```bash
touch protos/my_own_proto.proto
mkdir build && cd build
cmake .. 
cmake --build . --target generated_protos -j$(nproc)
# now the .pb.cc and .pb.h is generated alongside .proto
```