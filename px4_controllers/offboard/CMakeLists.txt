cmake_minimum_required(VERSION 3.0.2)
project(offboard)

add_definitions(-std=c++17)

find_package(catkin REQUIRED COMPONENTS
  geometry_msgs
  mavros_msgs
  roscpp
  roslaunch
  rospy
  std_msgs
  nav_msgs
  message_generation
  eigen_conversions
  #giang
  controller_msgs
  message_filters
  dynamic_reconfigure
  tf
  mavros_extras
  mavlink
)

generate_dynamic_reconfigure_options(
  cfg/Offboard.cfg
)

add_service_files(
  FILES
  setmode.srv
  getmode.srv
)

generate_messages(
  DEPENDENCIES
  std_msgs
  geometry_msgs
)

catkin_package(
   INCLUDE_DIRS include
  LIBRARIES offboard
   CATKIN_DEPENDS nav_msgs message_runtime roscpp rospy std_msgs mavros_msgs geometry_msgs sensor_msgs tf  message_filters controller_msgs
#  DEPENDS system_lib
)

include_directories(
  include
  ${Boost_INCLUDE_DIR}
  ${catkin_INCLUDE_DIRS}
  ${Eigen_INCLUDE_DIRS}
)
add_library(${PROJECT_NAME}
  src/offboard_lib.cpp
)
# roslaunch_add_file_check(launch)

# add_library(offboard_lib
#   src/offboard_lib.cpp
# )

add_dependencies(offboard ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

# target_link_libraries(offboard_lib
#   ${catkin_LIBRARIES}
# )

add_executable(offboard_node src/offboard_node.cpp)
# target_link_libraries(offboard_node
#   offboard_lib
# )

add_executable(setmode_offb src/setmode_offb.cpp)
# target_link_libraries(setmode_offb
#   ${catkin_LIBRARIES}
# )

add_dependencies(offboard_node offboard ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})


target_link_libraries(offboard_node ${PROJECT_NAME} ${catkin_LIBRARIES})

target_link_libraries(setmode_offb ${PROJECT_NAME} ${catkin_LIBRARIES})

catkin_install_python(PROGRAMS
  scripts/MarkerDetection.py
  scripts/real_cam.py
  scripts/transform.py
  DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)
