cmake_minimum_required(VERSION 3.16)
project(cobra_vendor NONE)

find_package(ament_cmake REQUIRED)

set(extra_cmake_args)

if(DEFINED CMAKE_BUILD_TYPE)
  list(APPEND extra_cmake_args "-DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}")
endif()

# The Cobra source code version/revision numbers
set(VER "5.0")
set(REV "f87d90bb1b0c975da1a4ab5852fd454cb3c4f59a")

include(ExternalProject)
ExternalProject_Add(cobra-${VER}
  PREFIX cobra-${VER}
  GIT_REPOSITORY https://github.com/nimble-code/cobra
  GIT_TAG ${REV}
  BUILD_IN_SOURCE TRUE
  CONFIGURE_COMMAND true
  BUILD_COMMAND ${CMAKE_COMMAND} -E chdir <SOURCE_DIR>/src make all && ${CMAKE_COMMAND} -E chdir <SOURCE_DIR>/src_app make all LDFLAGS=-Wl,c.ar\ -pthread
  INSTALL_COMMAND true
)

install(FILES
  ${CMAKE_CURRENT_BINARY_DIR}/cobra-${VER}/src/cobra-${VER}/src/cobra
  ${CMAKE_CURRENT_BINARY_DIR}/cobra-${VER}/src/cobra-${VER}/gui/window.tcl
  ${CMAKE_CURRENT_BINARY_DIR}/cobra-${VER}/src/cobra-${VER}/src_app/cwe
  ${CMAKE_CURRENT_BINARY_DIR}/cobra-${VER}/src/cobra-${VER}/src_app/scope_check
  ${CMAKE_CURRENT_BINARY_DIR}/cobra-${VER}/src/cobra-${VER}/src_app/find_taint
  ${CMAKE_CURRENT_BINARY_DIR}/cobra-${VER}/src/cobra-${VER}/src_app/json_convert
  ${CMAKE_CURRENT_BINARY_DIR}/cobra-${VER}/src/cobra-${VER}/src_app/duplicates
  PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_WRITE GROUP_EXECUTE WORLD_READ WORLD_WRITE WORLD_EXECUTE 
  DESTINATION lib/cobra_vendor
)

install(FILES
  ${CMAKE_CURRENT_BINARY_DIR}/cobra-${VER}/src/cobra-${VER}/gui/icobra.tcl
  RENAME icobra
  PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_WRITE GROUP_EXECUTE WORLD_READ WORLD_WRITE WORLD_EXECUTE 
  DESTINATION lib/cobra_vendor
)


install(DIRECTORY
  ${CMAKE_CURRENT_BINARY_DIR}/cobra-${VER}/src/cobra-${VER}/rules
  DESTINATION share/${PROJECT_NAME}
)

ament_environment_hooks(env_hook/cobra_vendor_base.dsv.in)

ament_package()
