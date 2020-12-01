cmake_minimum_required(VERSION 2.8.3)
project(swiftnav_piksi)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS diagnostic_updater roscpp sensor_msgs std_srvs std_msgs)

## System dependencies are found with CMake's conventions
#find_package(swiftnav REQUIRED)
#find_library(SBP sbp)

include(ExternalProject)

# SBP
set(SBP_INSTALL_DIR ${PROJECT_SOURCE_DIR}/sbp)
ExternalProject_Add(SBP
  GIT_REPOSITORY https://github.com/swift-nav/libsbp.git
  GIT_TAG  a8b96c3941330e3ab2cf22216f045682da2726dd
  TIMEOUT 30
  CONFIGURE_COMMAND ${CMAKE_COMMAND} -DCMAKE_INSTALL_PREFIX:PATH=${SBP_INSTALL_DIR}   <SOURCE_DIR>/c
  BUILD_COMMAND make
  BUILD_IN_SOURCE 1
  INSTALL_COMMAND make install
  LOG_DOWNLOAD ON
  LOG_CONFIGURE ON
  LOG_BUILD ON
  )
include_directories(${SBP_INSTALL_DIR}/include)
link_directories(${SBP_INSTALL_DIR}/lib)

catkin_package(
   INCLUDE_DIRS include
   LIBRARIES piksi piksi_driver
   CATKIN_DEPENDS diagnostic_updater roscpp sensor_msgs std_srvs
)

include_directories(include
  ${catkin_INCLUDE_DIRS}
)

## Declare a cpp library
add_library(piksi src/piksi.c)
target_link_libraries(piksi sbp)
add_dependencies(piksi SBP)

add_library(piksi_driver src/piksi_driver.cpp)
target_link_libraries(piksi_driver piksi)

add_executable(piksi_node src/piksi_node.cpp)
target_link_libraries(piksi_node piksi_driver ${catkin_LIBRARIES})




