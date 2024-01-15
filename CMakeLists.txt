cmake_minimum_required(VERSION 3.0.2)
project(dyros_bolt_sensor)

## Compile as C++11, supported in ROS Kinetic and newer
add_compile_options(-std=c++17 -Ofast)

##if(${FORCE_REALROBOT} MATCHES ON)
##  message(" forces realrobot compile mode ")
##  ADD_DEFINITIONS(-DCOMPILE_REALROBOT)
##  set(COMPILE_REAL TRUE)
##else()

execute_process(COMMAND uname -r OUTPUT_VARIABLE UNAME_RES OUTPUT_STRIP_TRAILING_WHITESPACE)
message("-- kernel version : " ${UNAME_RES})

string(FIND ${UNAME_RES} "xenomai" wsloc)

# if(${wsloc} EQUAL -1 )
#   message(" simulation only compile mode ")
#   ADD_DEFINITIONS(-DCOMPILE_SIMULATION)
#   set(COMPILE_SIM TRUE)
# else()
#   message(" xenomai detected realrobot compilt mode ")
#   ADD_DEFINITIONS(-DCOMPILE_REALROBOT)
#   set(COMPILE_REAL TRUE)
# endif() 

message(" forces realrobot compile mode ")
ADD_DEFINITIONS(-DCOMPILE_REALROBOT)
set(COMPILE_REAL TRUE)

#endif()

if(${COMPILE_REAL})

find_package(catkin REQUIRED COMPONENTS
  eigen_conversions
  roscpp
  sensor_msgs
  geometry_msgs
  dyros_bolt_lib
  tf2
  tf2_ros
)
FIND_PACKAGE(Boost COMPONENTS system thread filesystem REQUIRED)


catkin_package(
#  INCLUDE_DIRS include
#  LIBRARIES dyros_bolt_sensor
#  CATKIN_DEPENDS eigen_conversions roscpp sensor_msgs tf2 tf2_ros
#  DEPENDS system_lib
)

include_directories(
  include
  ${catkin_INCLUDE_DIRS}
  /usr/share/c++-mscl/source
  /usr/share/c++-mscl/Boost/include
)

link_directories(
      /usr/share/c++-mscl
)

add_library(mx5_lib
  src/mx5_imu.cpp  
)

add_executable(${PROJECT_NAME} src/sensor_manager.cpp)

target_link_libraries(${PROJECT_NAME}
  ${catkin_LIBRARIES}
  mx5_lib
  -lmscl 
  ${Boost_LIBRARIES} 
  /usr/share/c++-mscl/Boost/lib/libboost_system.so.1.68.0

)

add_dependencies(mx5_lib ${catkin_EXPORTED_TARGETS})
add_dependencies(${PROJECT_NAME} ${catkin_EXPORTED_TARGETS})


endif()
