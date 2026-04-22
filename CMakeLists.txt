cmake_minimum_required(VERSION 3.5)
project(dvl_a50)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

set(DEPS
  rclcpp
  rclcpp_lifecycle
  std_srvs
  geometry_msgs
  nav_msgs
  marine_acoustic_msgs
  tf2_geometry_msgs
  dvl_msgs
)


# find dependencies
foreach(dep IN LISTS DEPS)
  find_package(${dep} REQUIRED)
endforeach()
find_package(ament_cmake REQUIRED)
find_package(rclcpp_components REQUIRED)
find_package(lifecycle_msgs REQUIRED)


include_directories(include
  ${rclcpp_INCLUDE_DIRS}
)


# Library
add_library(${PROJECT_NAME} SHARED src/dvl_a50.cpp)
ament_export_targets(${PROJECT_NAME} HAS_LIBRARY_TARGET)

INSTALL(
  TARGETS ${PROJECT_NAME}
  EXPORT ${PROJECT_NAME}
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  RUNTIME DESTINATION bin
)

# Need to be installed separately
INSTALL(
  DIRECTORY include/ 
  DESTINATION include
)

# ROS2 Node
ADD_EXECUTABLE(${PROJECT_NAME}_node 
    src/dvl_a50_ros2.cpp
)
ament_target_dependencies(${PROJECT_NAME}_node ${DEPS})
# Link node to library compiled above and install it in the correct location
TARGET_LINK_LIBRARIES(${PROJECT_NAME}_node ${PROJECT_NAME})
INSTALL(TARGETS
  ${PROJECT_NAME}_node
  DESTINATION lib/${PROJECT_NAME}/
)


# Launch and config files
install(DIRECTORY
  launch
  DESTINATION share/${PROJECT_NAME}/
)

install(DIRECTORY
  config
  DESTINATION share/${PROJECT_NAME}
)


ament_package()
