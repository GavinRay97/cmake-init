cmake_minimum_required(VERSION 3.14)

include(cmake/in-source-guard.cmake)

project(
    %(name)s
    VERSION %(version)s
    DESCRIPTION "%(description)s"
    HOMEPAGE_URL "%(homepage)s"
    LANGUAGES CXX
)

include(cmake/project-is-top-level.cmake)
include(cmake/variables.cmake)

# ---- Declare library ----

add_library(
    %(name)s_lib OBJECT
    source/lib.cpp
)

target_include_directories(
    %(name)s_lib
    ${%(name)s_warning_guard}
    PUBLIC
    "$<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>"
)

target_compile_features(%(name)s_lib PUBLIC cxx_std_%(std)s)

# ---- Declare executable ----

add_executable(%(name)s_%(name)s source/main.cpp)
add_executable(%(name)s::%(name)s ALIAS %(name)s_%(name)s)

set_target_properties(
    %(name)s_%(name)s PROPERTIES
    OUTPUT_NAME %(name)s
    EXPORT_NAME %(name)s
)

target_compile_features(%(name)s_%(name)s PRIVATE cxx_std_%(std)s)

target_link_libraries(%(name)s_%(name)s PRIVATE %(name)s_lib)

# ---- Install rules ----

include(cmake/install-rules.cmake)

# ---- Developer mode ----

if(NOT %(name)s_DEVELOPER_MODE)
  return()
elseif(NOT PROJECT_IS_TOP_LEVEL)
  message(
      AUTHOR_WARNING
      "Developer mode is intended for developers of %(name)s"
  )
endif()

include(CTest)
if(BUILD_TESTING)
  add_subdirectory(test)
endif()

add_custom_target(
    run_exe
    COMMAND "$<TARGET_FILE:%(name)s_%(name)s>"
    VERBATIM
)
