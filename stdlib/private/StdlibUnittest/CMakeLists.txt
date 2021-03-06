set(swift_stdlib_unittest_platform_sources)
set(swift_stdlib_unittest_module_depends
  SwiftPrivate SwiftPrivatePthreadExtras SwiftPrivateDarwinExtras)
set(swift_stdlib_unittest_framework_depends)
set(swift_stdlib_unittest_private_link_libraries)

if(SWIFT_HOST_VARIANT MATCHES "${SWIFT_DARWIN_VARIANTS}")
  list(APPEND swift_stdlib_unittest_platform_sources
    GetOSVersion.mm)
  list(APPEND swift_stdlib_unittest_module_depends
    Darwin
    Foundation)
  list(APPEND swift_stdlib_unittest_framework_depends
    Foundation)
endif()
if(SWIFT_HOST_VARIANT STREQUAL "linux" OR SWIFT_HOST_VARIANT STREQUAL "freebsd")
  list(APPEND swift_stdlib_unittest_module_depends
    Glibc)
endif()

add_swift_library(swiftStdlibUnittest SHARED IS_STDLIB
  # This file should be listed the first.  Module name is inferred from the
  # filename.
  StdlibUnittest.swift.gyb

  CheckCollectionType.swift
  CheckMutableCollectionType.swift.gyb
  CheckRangeReplaceableCollectionType.swift
  CheckRangeReplaceableSliceType.swift
  CheckSequenceType.swift
  InterceptTraps.cpp
  LoggingWrappers.swift.gyb
  OpaqueIdentityFunctions.cpp
  OpaqueIdentityFunctions.swift
  RaceTest.swift
  Statistics.swift
  StdlibCoreExtras.swift
  TestHelpers.swift
  TypeIndexed.swift
  LifetimeTracked.swift
  ${swift_stdlib_unittest_platform_sources}

  # Cannot serialize StdlibUnittest because of:
  # <rdar://problem/18917405> Compiling StdlibUnittest with -sil-serialize-all
  # crashes in SIL serializer
  #
  # SWIFT_COMPILE_FLAGS -Xfrontend -sil-serialize-all

  PRIVATE_LINK_LIBRARIES ${swift_stdlib_unittest_private_link_libraries}
  SWIFT_MODULE_DEPENDS ${swift_stdlib_unittest_module_depends}
  FRAMEWORK_DEPENDS ${swift_stdlib_unittest_framework_depends}
  INSTALL_IN_COMPONENT stdlib-experimental)

