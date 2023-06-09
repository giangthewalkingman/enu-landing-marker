cmake_minimum_required(VERSION 3.14.0)
project(geometric_controller)

add_definitions(-std=c++17)

find_package(catkin REQUIRED COMPONENTS
  roscpp
  rospy
  dynamic_reconfigure
  tf
  mavros
  mavros_extras
  mavros_msgs
  mavlink
  controller_msgs
  message_filters
  message_generation
)

generate_dynamic_reconfigure_options(
  cfg/GeometricController.cfg
)

add_service_files(
  FILES
  setmode.srv
  getmode.srv
)

generate_messages(
  DEPENDENCIES
  std_msgs
)

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES geometric_controller
  CATKIN_DEPENDS roscpp rospy std_msgs mavros_msgs geometry_msgs sensor_msgs tf  message_filters controller_msgs
)

#############
# LIBRARIES #
#############
include_directories(
  include
  ${Boost_INCLUDE_DIR}
  ${catkin_INCLUDE_DIRS}
  ${Eigen_INCLUDE_DIRS}
)
add_library(${PROJECT_NAME}
  src/geometric_controller.cpp
)

add_dependencies(geometric_controller ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

############
# BINARIES #
############

add_executable(geometric_controller_node
  src/geometric_controller_node.cpp
)

add_executable(stat
  src/stat.cpp
)

add_executable(gps
  src/gnss.cpp
)

add_dependencies(geometric_controller_node geometric_controller ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

target_link_libraries(geometric_controller_node ${PROJECT_NAME} ${catkin_LIBRARIES})

target_link_libraries(stat ${PROJECT_NAME} ${catkin_LIBRARIES})

target_link_libraries(gps ${PROJECT_NAME} ${catkin_LIBRARIES})