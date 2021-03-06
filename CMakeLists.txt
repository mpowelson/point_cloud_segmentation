cmake_minimum_required(VERSION 2.8.3)
project(point_cloud_segmentation)

add_compile_options(-std=c++11)

find_package(catkin REQUIRED COMPONENTS
  cv_bridge
  message_generation
  pcl_conversions
  pcl_ros
  roscpp
  sensor_msgs
  )

#find_package(PCL 1.8 REQUIRED)
find_package(OpenCV 3 REQUIRED)

add_service_files(
  FILES
    ImageProcessing.srv
)

generate_messages(
  DEPENDENCIES
    sensor_msgs
)

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES pc_masker
  CATKIN_DEPENDS
  cv_bridge
  message_runtime
  pcl_conversions
  pcl_ros
  roscpp
  sensor_msgs
#  DEPENDS
)

###########
## Build ##
###########

include_directories(
 include
 ${catkin_INCLUDE_DIRS}
 ${PCL_INCLUDE_DIRS}
)

## Declare a C++ library
 add_library(pc_masker
   src/${PROJECT_NAME}/pc_masker.cpp
 )
add_dependencies(pc_masker ${pc_masker_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS} point_cloud_segmentation_generate_messages_cpp)
target_link_libraries(pc_masker
  ${catkin_LIBRARIES}
  ${OpenCV_LIBS}
  ${OpenCV_LIBRARIES})


## Add cmake target dependencies of the library
## as an example, code may need to be generated before libraries
## either from message generation or dynamic reconfigure
# add_dependencies(${PROJECT_NAME} ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

## Declare a C++ executable
add_executable(pc_masker_node src/pc_masker_node.cpp)
add_dependencies(pc_masker_node ${pc_masker_node_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS} point_cloud_segmentation_generate_messages_cpp)
target_link_libraries(pc_masker_node pc_masker ${catkin_LIBRARIES})

#############
## Install ##
#############

# all install targets should use catkin DESTINATION variables
# See http://ros.org/doc/api/catkin/html/adv_user_guide/variables.html

## Mark executable scripts (Python etc.) for installation
## in contrast to setup.py, you can choose the destination
# install(PROGRAMS
#   scripts/my_python_script
#   DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark executables and/or libraries for installation
 install(TARGETS pc_masker_node
   ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
   LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
   RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
 )

## Mark cpp header files for installation
 install(DIRECTORY include/${PROJECT_NAME}/
   DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
   FILES_MATCHING PATTERN "*.h"
   PATTERN ".svn" EXCLUDE
 )

## Mark other files for installation (e.g. launch and bag files, etc.)
 install(FILES
   launch/demo.launch
   config/demo.rviz
   DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
 )

#############
## Testing ##
#############

## Add gtest based cpp test target and link libraries
# catkin_add_gtest(${PROJECT_NAME}-test test/point_cloud_segmentation.cpp)
# if(TARGET ${PROJECT_NAME}-test)
#   target_link_libraries(${PROJECT_NAME}-test ${PROJECT_NAME})
# endif()

## Add folders to be run by python nosetests
# catkin_add_nosetests(test)
