if(NOT ENABLE_GRPC)
  return()
endif()

set(LOKI_CPP_SOURCES
  ${GRPC_CREDENTIALS_SOURCES}
  ${GRPC_METRICS_SOURCES}
  loki-dest.hpp
  loki-dest.cpp
  loki-dest.h
  loki-worker.hpp
  loki-worker.cpp
  loki-worker.h
)

set(LOKI_SOURCES
  loki-parser.h
  loki-plugin.c
  loki-parser.c
)

add_module(
  TARGET loki-cpp
  SOURCES ${LOKI_CPP_SOURCES}
  DEPENDS ${MODULE_GRPC_LIBS} grpc-protos
  INCLUDES ${LOKI_PROTO_BUILDDIR} ${PROJECT_SOURCE_DIR}/modules/grpc
  LIBRARY_TYPE STATIC
)

add_module(
  TARGET loki
  GRAMMAR loki-grammar
  DEPENDS loki-cpp
  INCLUDES ${PROJECT_SOURCE_DIR}/modules/grpc
  SOURCES ${LOKI_SOURCES}
)

target_compile_options(loki-cpp PRIVATE -Wno-deprecated -Wno-double-promotion -DPROTOBUF_ENABLE_DEBUG_LOGGING_MAY_LEAK_PII=0)

set_target_properties(loki PROPERTIES INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/lib;${CMAKE_INSTALL_PREFIX}/lib/syslog-ng")
