cmake_minimum_required(VERSION 2.8.3)
project(norm_2d)

set(CMAKE_CXX_STANDARD 11)

find_package (Eigen3 3.3 REQUIRED NO_MODULE)
find_package(catkin REQUIRED COMPONENTS)


# PCL Package ##############################################################
find_package(PCL 1.8 REQUIRED)
# include_directories(include 
# ${PCL_INCLUDE_DIRS} 
# LIBRARIES ${PROJECT_NAME}
# )
link_directories(${PCL_LIBRARY_DIRS})
add_definitions(${PCL_DEFINITIONS})

# Boost Package ############################################################
set(Boost_USE_STATIC_LIBS OFF)
set(Boost_USE_MULTITHREADED ON)
set(Boost_USE_STATIC_RUNTIME OFF)
find_package(Boost 1.58.0 COMPONENTS program_options filesystem REQUIRED)
catkin_package(INCLUDE_DIRS include 
  LIBRARIES ${PROJECT_NAME}
  DEPENDS Boost PCL Eigen3
)
include_directories(include 
${Boost_INCLUDE_DIR} ${PCL_INCLUDE_DIRS} ${catkin_INCLUDE_DIRS})

get_property(dirs DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} PROPERTY INCLUDE_DIRECTORIES)
foreach(dir ${dirs})
  message(STATUS "dir='${dir}'")
endforeach()

add_library(PCA2D
  src/PCA2D.cpp
)

add_library(Normal2dEstimation
  src/Normal2dEstimation.cpp
)

add_library(norm_2d
  src/PCA2D.cpp
  src/Normal2dEstimation.cpp
)

target_link_libraries(PCA2D
  ${catkin_LIBRARIES}
  ${PCL_LIBRARIES}
  ${Boost_LIBRARIES}
)

target_link_libraries(Normal2dEstimation
  ${catkin_LIBRARIES}
  ${PCL_LIBRARIES}
  ${Boost_LIBRARIES}
  PCA2D
)

target_link_libraries(Normal2dEstimation
  ${catkin_LIBRARIES}
  ${PCL_LIBRARIES}
  ${Boost_LIBRARIES}
  Normal2dEstimation
  PCA2D
)


add_dependencies(PCA2D
  ${${PROJECT_NAME}_EXPORTED_TARGETS}
  ${catkin_EXPORTED_TARGETS}
)
add_dependencies(Normal2dEstimation
  ${${PROJECT_NAME}_EXPORTED_TARGETS}
  ${catkin_EXPORTED_TARGETS}
  PCA2D
)

#PointXYNormal.cpp PointXYNormal.h
add_executable(norm_2d_node src/main.cpp)
target_link_libraries(norm_2d_node
  ${catkin_LIBRARIES}
  ${PCL_LIBRARIES}
  ${Boost_LIBRARIES}
  PCA2D
  Normal2dEstimation
)

add_dependencies(norm_2d_node
  ${${PROJECT_NAME}_EXPORTED_TARGETS}
  ${catkin_EXPORTED_TARGETS}
  PCA2D
  Normal2dEstimation
)



message("Lib")
message("${CATKIN_PACKAGE_LIB_DESTINATION}")
message("RUNTIME")
message("${CATKIN_PACKAGE_BIN_DESTINATION}")

install(
    TARGETS
    norm_2d
    norm_2d_node
    ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
    LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
    RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(FILES sample.pcd
    DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)

install(
  DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)

install(DIRECTORY include/${PROJECT_NAME}/ DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION} FILES_MATCHING PATTERN "*.h" PATTERN ".svn" EXCLUDE )