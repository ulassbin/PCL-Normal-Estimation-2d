# Normal-Estimation-2d
PCL add-on to compute normal estimation to 2D pointCloud.
You can find a Normal2dEstimation class that take 3D points but ignore the z part.


You can see the example result just bellow :
![normal_illustration](https://user-images.githubusercontent.com/22777836/62471207-79ef0700-b79c-11e9-8c34-2a45c079acce.png)

## Why
The PCL library doesn't support pure 2D points. You have to use 3D points with a z compotent equals to 0. This trick work with
 many algorithm like centroid, ICP, ransac etc.. But it doesn't work with normal estimation due to your constant component (all normal are equal to (0,0,1). 
 My implementation solve this problem by ignoring the z component.

 Here you can see the problem with pcl 3d normal estimation: 
 ![problem_normal](https://user-images.githubusercontent.com/22777836/62474444-30ee8100-b7a3-11e9-9ee8-de2866f65ec8.png)

 

## A sample Cmakelists for loading this library:



```makefile
cmake_minimum_required(VERSION 2.8.3)
project(norm2d_loader_package)

## Compile as C++11, supported in ROS Kinetic and newer
add_compile_options(-std=c++11)


set(loader_ROS_DEPS
  norm_2d
  roscpp
)

find_package(catkin REQUIRED COMPONENTS ${loader_ROS_DEPS})

catkin_package(
 INCLUDE_DIRS include
# LIBRARIES norm2d_loader_package
 CATKIN_DEPENDS ${loader_ROS_DEPS}
#  DEPENDS system_lib
)

include_directories(
 include
 ${catkin_INCLUDE_DIRS}
)

add_executable(loader_package_node
  src/loader_package.cpp
)

add_dependencies(loader_package_node
  ${${PROJECT_NAME}_EXPORTED_TARGETS}
  ${catkin_EXPORTED_TARGETS}
)

target_link_libraries(loader_package_node 
  ${catkin_LIBRARIES}
)

install(
    TARGETS
	loader_package_node
    ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
    LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
    RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h"
)
```

