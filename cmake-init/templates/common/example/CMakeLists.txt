cmake_minimum_required(VERSION 3.14)

project(%(name)sExamples CXX)

include(../cmake/project-is-top-level.cmake)

if(PROJECT_IS_TOP_LEVEL)
  find_package(%(name)s REQUIRED)
endif()

add_custom_target(run_examples)

function(add_example NAME)
  add_executable("${NAME}" "${NAME}.cpp")
  target_link_libraries("${NAME}" PRIVATE %(name)s::%(name)s)
  target_compile_features("${NAME}" PRIVATE cxx_std_%(std)s)
  add_custom_target(
      "run_${NAME}"
      COMMAND "$<TARGET_FILE:${NAME}>"
      VERBATIM
  )
  add_dependencies(run_examples "run_${NAME}")
endfunction()

add_example(empty_example)
