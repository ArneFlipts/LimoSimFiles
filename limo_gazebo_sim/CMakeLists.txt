cmake_minimum_required(VERSION 2.8.3)
project(limo_gazebo_sim)

add_compile_options(-std=c++11)

find_package(catkin REQUIRED COMPONENTS    
  roslaunch
  rospy
  roslint
  roscpp
  std_msgs
  sensor_msgs
  geometry_msgs
  tf2
  tf2_ros
  tf2_geometry_msgs
  gazebo_dev
  gazebo_msgs
  dynamic_reconfigure
)

generate_dynamic_reconfigure_options(
  cfg/GazeboTrafficLight.cfg
)

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES  gazebo_ros_ackerman
  CATKIN_DEPENDS roscpp sensor_msgs
)

find_package(gazebo REQUIRED)
link_directories(${GAZEBO_LIBRARY_DIRS})
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${GAZEBO_CXX_FLAGS}")



include_directories(
  include
  ${catkin_INCLUDE_DIRS}
  ${GAZEBO_INCLUDE_DIRS}
)

add_library(MyFlashLightPlugin SHARED src/MyFlashLightPlugin.cc)
target_link_libraries(MyFlashLightPlugin ${GAZEBO_LIBRARIES} ${catkin_LIBRARIES} FlashLightPlugin)

add_library(gazebo_ros_ackerman src/gazebo_ros_ackerman_drive.cpp)
target_link_libraries(gazebo_ros_ackerman ${catkin_LIBRARIES} ${Boost_LIBRARIES})

add_library(GazeboTrafficLight
  src/GazeboTrafficLight.cpp
)
add_dependencies(GazeboTrafficLight
GazeboTrafficLight_gencfg
)
target_link_libraries(GazeboTrafficLight
  ${catkin_LIBRARIES}
  ${GAZEBO_LIBRARIES}
)

install(TARGETS MyFlashLightPlugin DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION})
install(TARGETS gazebo_ros_ackerman 
ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
RUNTIME DESTINATION ${CATKIN_GLOBAL_BIN_DESTINATION}
)

install(FILES stoplight.sdf DESTINATION ${CMAKE_INSTALL_PREFIX}/share/gazebo/models/stoplight/)

install(
  TARGETS
   GazeboTrafficLight
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
)

install(
  DIRECTORY
    models
    worlds
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)