# Minimum CMake required. If available, accept the policy-controlled behavior up
# to 3.26.
cmake_minimum_required(VERSION 3.16...3.26)

# Revert to old behavior for MSVC debug symbols.
if(POLICY CMP0141)
  cmake_policy(SET CMP0141 OLD)
endif()

# Project
project(protobuf C CXX)

# We need to link with libatomic on systems that do not have builtin atomics, or
# don't have builtin support for 8 byte atomics (e.g. 32-bit powerpc).
set(protobuf_LINK_LIBATOMIC false)

include(CheckCXXSourceCompiles)
check_cxx_source_compiles("
  #include <atomic>
  int main() {
    return static_cast<int>(std::atomic<int64_t>{});
  }
" protobuf_HAVE_BUILTIN_ATOMICS)
if (NOT protobuf_HAVE_BUILTIN_ATOMICS)
  set(protobuf_LINK_LIBATOMIC true)
endif (NOT protobuf_HAVE_BUILTIN_ATOMICS)

message(STATUS "protobuf_HAVE_BUILTIN_ATOMICS=${protobuf_HAVE_BUILTIN_ATOMICS}")
message(STATUS "protobuf_LINK_LIBATOMIC=${protobuf_LINK_LIBATOMIC}")
