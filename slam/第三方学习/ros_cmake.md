---
title: ros_cmake配置模板
date: 2021-10-19 12:30:12
categories:
  - slam
  - ros
tags:
  - ros
---
### 简易CmakeLists.txt

```cmake
cmake_minimum_required(VERSION 2.8.3)

# 工程名
project(hmi)

# 支持编译
add_compile_options(-std=c++11)

# 查找包
find_package(catkin REQUIRED COMPONENTS
  roscpp
  sensor_msgs
  pcl_ros
  perception_msgs
)

# catkin库
catkin_package(
  INCLUDE_DIRS include
  CATKIN_DEPENDS roscpp sensor_msgs pcl_ros
)

# 头文件库
include_directories(
 include
  ${catkin_INCLUDE_DIRS}
)

# 链接库文件夹
link_directories(${PCL_LIBRARY_DIRS})

# 生成执行文件
add_executable(${PROJECT_NAME}_node src/hmi_node.cpp src/hmi_core.cpp)

# 依赖包
add_dependencies(${PROJECT_NAME}_node perception_msgs_generate_messages_cpp)

# 链接库
target_link_libraries(${PROJECT_NAME}_node
  ${catkin_LIBRARIES}
  ${PCL_LIBRARIES}
)

```

### 详细CmakeLists.txt

```cmake
cmake_minimum_required()  #CMake的版本号

project()         #项目名称

find_package()        #找到编译需要的其他CMake/Catkin  package 

catkin_python_setup()   #catkin新加宏，打开catkin的Python Module的支持

add_message_files()     #catkin新加宏，添加自定义Message文件 

add_service_files()         #catkin新加宏，添加自定义Service文件 

add_action_files()          #catkin新加宏，添加自定义Action文件 

generate_message()      #catkin新加宏，生成不同语言版本的msg/srv/action接口 

catkin_package()      #catkin新加宏，生成当前package的cmake配置，供依赖本包的其他软件包调用 

add_library()       #生成库 

add_executable()      #生成可执行二进制文件

add_dependencies()      #定义目标文件依赖于其他目标文件，确保其他目标已被构建 

target_link_libraries()   #链接

catkin_add_gtest()      #catkin新加宏，生成测试

install()         #安装至本机
```

### 对照实例

```cmake
cmake_minimum_required(VERSION 3.0.2)    # catkin至少需要3.0.2版本以上的Cmake
#通过project()这个函数指定包的名字，在CMake中指定后，你可在其他地方通过使用变量${PROJECT_NAME}来引用它
project(ultrasonic_data) 

## Compile as C++11, supported in ROS Kinetic and newer
# add_compile_options(-std=c++11)

## 查找编译依赖的其他CMake/Catkin包（声明依赖库）：find_package(）  
##find_package(catkin REQUIRED COMPONENTS xyz) 被用于找其他依赖的功能包
##这里指明构建这个package需要依赖的package，我们使用catkin_make的编译方式，至少需要catkin这个包。
find_package(catkin REQUIRED COMPONENTS
  message_generation
  roscpp
  sensor_msgs
  std_msgs
  visualization_msgs
  can_msgs
)

## 系统依赖关系是通过CMake的约定找到的
# find_package(Boost REQUIRED COMPONENTS system)

##启动Python模块支持：catkin_python_package()
##如果功能包中提供了一些Python模块.这个宏定义可以确保包中声明的模块和全局脚本被安装
## See http://ros.org/doc/api/catkin/html/user_guide/setup_dot_py.html
# catkin_python_setup()

############################
## 声明ROS消息、服务和动作 ##
############################

## 消息/服务/操作(Message/Service/Action)生成器：add_message_files(),add_service_files(),add_action_files()
##当我们需要使用.msg.srv.action形式的文件时，我们需要特殊的预处理器把他们转化为系统可以识别特定编程语言（.h.cpp）。
##注意：这些宏必须在catkin_package() 宏前面
##请执行以下步骤：
## * 在package.xml中:
## *1.添加"message_generation"在build_depend标记中
## *2.添加"message_runtime"在exec_depend标记中

## * 在CMakeLists.txt中:
## *1、在find_package(catkin REQUIRED COMPONENTS ...)添加"message_generation" 
## *2、在catkin_package(CATKIN_DEPENDS ...)添加"message_runtime"

##   根据需要取消下面“add_######_files”部分的注释
##   * 取消下面的generate_messages(DEPENDENCIES...注释
##   * add every package  to generate_messages(DEPENDENCIES ...)

##  在'msg' 文件夹生成消息
 add_message_files(
   FILES
   Ultrasonic.msg
#   Message2.msg
 )

##在 'srv' 文件夹生成服务
# add_service_files(
#   FILES
#   Service1.srv
#   Service2.srv
# )

## 在 'action' 文件夹生成动作
# add_action_files(
#   FILES
#   Action1.action
#   Action2.action
# )

## 在此处列出的任何添加的消息和服务生成的依赖项
##调用消息/服务/操作生成：generate_messages()
 generate_messages(
   DEPENDENCIES
#   sensor_msgs
     std_msgs
#   visualization_msgs
 )

################################################
##声明ROS动态配置参数 ##
################################################

## 声明和生成动态重新配置参数包:遵循以下步骤
## * 在package.xml中:
## *添加"dynamic_reconfigure"在build_depend和exec_depend标记中
## * 在CMakeLists.txt中:
## *在find_package(catkin REQUIRED COMPONENTS ...)添加"dynamic_reconfigure" 
## * 取消下面 "generate_dynamic_reconfigure_options" 的注释
## 列出要处理的每个.cfg文件

## Generate dynamic reconfigure parameters in the 'cfg' folder
# generate_dynamic_reconfigure_options(
#   cfg/DynReconf1.cfg
#   cfg/DynReconf2.cfg
# )

###################################
## catkin 特定的配置 ##
###################################
##  catkin_package为包生成cmake配置文件  这是一个catkin提供的cmake宏，当我们要给构建系统指定catkin的特定的信息时就需要了
##  或者反过来利用他产生pkg-config和CMake文件。
##指定包编译信息导出：catkin_package()
## 该函数必须在使用 add_library()或add_executable()声明任何targets之前调用。

## INCLUDE_DIRS: 如果包包含头文件，请取消对INCLUDE_DIRS的注释
## LIBRARIES: 此项目中创建的依赖项目需要的库LIBRARIES
## CATKIN_DEPENDS: catkin_packages依赖的项目
## DEPENDS:  依赖项目需要的系统依赖项
catkin_package(
    INCLUDE_DIRS include
#  LIBRARIES ultrasonic_data
    CATKIN_DEPENDS 
    message_runtime
#  DEPENDS system_lib
)

###########
## Build ##
###########

## include_directories用来设置头文件的相对路径
##include_directories的参数是通过find_package产生的*_INCLUDE_DIRS变量和其他所有额外的头文件路径
##这里"include"表示你的pacakge里面的include这个路径也包含在里面

include_directories(
  include
  ${catkin_INCLUDE_DIRS}
)
##########################################################################
##添加要编译的库和可执行文件：add_library()/add_executable()/target_link_libraries()##
##########################################################################
## add_library()用来指定编译产生的库。默认的catkin编译产生共享库：
# add_library(${PROJECT_NAME}
#   src/${PROJECT_NAME}/ultrasonic_data.cpp
# )

## add_dependencies用于设置依赖
##如果你有一个包编译.msg .srv，并且可执行文件要使用他们，那么你就需要创建一个显式的依赖项，
## 我们定义的语言无关的消息类型，消息类型会在编译过程中产生相应的语言的代码，如果编译的可执行文件依赖这些动态生成的代码，则需要
## 使用add_dependencies(${PROJECT_NAME}_generate_messages_cpp)
## add_dependencies(${PROJECT_NAME} ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

## add_executable用于设置需要编译的代码和生成的可执行文件
##第一个参数为期望生成的可执行文件名称；后面的参数为参与编译的源文件（cpp),如果需要多个代码文件，用空格区分开
## 推荐的前缀可以确保跨包的目标名称不会冲突
# add_executable(${PROJECT_NAME}_node src/ultrasonic_data_node.cpp)

## 重命名C++无前缀可执行文件
## 利用set_target_properties()函数将这个target进行重命名，在catkin中target的名字必须是唯一的
# set_target_properties(${PROJECT_NAME}_node PROPERTIES OUTPUT_NAME node PREFIX "")

## 添加可执行文件的cmake目标依赖项
## same as for the library above与上面的库相同
# add_dependencies(${PROJECT_NAME}_node ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

##target_link_libraries用于设置链接库
# target_link_libraries(${PROJECT_NAME}_node
#   ${catkin_LIBRARIES}
# )

add_executable(ultrasonic_activate_node
  src/ultrasonic_activate_node.cpp
)
add_dependencies(ultrasonic_activate_node ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(ultrasonic_activate_node
  ${catkin_LIBRARIES}
)

add_executable(ultrasonic_node
  src/ultrasonic_node.cpp
  src/ultrasonic_filter.cpp

)
add_dependencies(ultrasonic_node ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(ultrasonic_node
  ${catkin_LIBRARIES}
)

#############
###安装至本机 ##
#############

# 所有安装目标都应该使用catkin目标变量
# See http://ros.org/doc/api/catkin/html/adv_user_guide/variables.html

##安装Python可执行脚本
# catkin_install_python(PROGRAMS
#   scripts/my_python_script
#   DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## 安装的可执行文件
## See http://docs.ros.org/melodic/api/catkin/html/howto/format1/building_executables.html
# install(TARGETS ${PROJECT_NAME}_node
#   RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## 要安装的库
##编译完成后，目标被放入catkin工作空间下的devel目录。一般希望将目标安装到系统上，以使其他用户使用，
##或者安装到本地目录来测试系统级别的安装。也就是说，如果希望能够对代码进行make install，就需要明确目标结束的位置。
##上述过程可以使用CMake的 install()函数实现。
##该函数的参数有
##TARGETS：要安装的目标
##ARCHIVE DESTINATION：静态库和动态链接库DLL(Windows).lib存根
##LIBRARY DESTINATION：非DLL共享库和模块
##RUNTIME DESTINATION：可执行目标和DLL(Windows)模式共享库
## See http://docs.ros.org/melodic/api/catkin/html/howto/format1/building_libraries.html
# install(TARGETS ${PROJECT_NAME}
#   ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   RUNTIME DESTINATION ${CATKIN_GLOBAL_BIN_DESTINATION}
# )

## 安装头文件
##头文件必须安装到include目录下，这通常通过安装整个文件夹的文件来完成（可以根据文件名模式进行过滤，并排除SVN子文件夹）
##可以通过以下安装规则实现：
# install(DIRECTORY include/${PROJECT_NAME}/
#   DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
#   FILES_MATCHING PATTERN "*.h"
#   PATTERN ".svn" EXCLUDE
# )

## 安装roslaunch文件或其他源
##其他像launchfiles的资源可以安装到 ${CATKIN_PACKAGE_SHARE_DESTINATION}
# install(FILES
#   # myfile1
#   # myfile2
#   DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
# )

#############################
## 测试编译：catkin_add_gtest()##
#############################

## 基于gtest测试目标和链接库
# catkin_add_gtest(${PROJECT_NAME}-test test/test_ultrasonic_data.cpp)
# if(TARGET ${PROJECT_NAME}-test)
#   target_link_libraries(${PROJECT_NAME}-test ${PROJECT_NAME})
# endif()

## 添加要由python osetests运行的文件夹
# catkin_add_nosetests(test)

```

### 具体可参考网址

[ROS中的CMake List详解_yangtao420902的博客-CSDN博客](https://blog.csdn.net/yangtao420902/article/details/110873333?ops_request_misc=%7B%22request%5Fid%22%3A%22163430132516780264065592%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=163430132516780264065592&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-8-110873333.pc_search_ecpm_flag&utm_term=ros的CMakeLists详解&spm=1018.2226.3001.4187)

[ROS下CmakeLists.txt模板_CipherPolzz的博客-CSDN博客](https://blog.csdn.net/CipherPolzz/article/details/118963390?ops_request_misc=%7B%22request%5Fid%22%3A%22163430132516780264065592%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=163430132516780264065592&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-6-118963390.pc_search_ecpm_flag&utm_term=ros的CMakeLists详解&spm=1018.2226.3001.4187)

[ROS新建程序包CmakeLists和package详解_JayLee719的博客-CSDN博客](https://blog.csdn.net/weixin_43877080/article/details/110953533?ops_request_misc=%7B%22request%5Fid%22%3A%22163430132516780264065592%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=163430132516780264065592&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-5-110953533.pc_search_ecpm_flag&utm_term=ros的CMakeLists详解&spm=1018.2226.3001.4187)

[(46条消息) ROS中CMakeLists.txt文件 使用 讲解与总结_月照银海似蛟龙的博客-CSDN博客](https://blog.csdn.net/qq_32761549/article/details/104535989?ops_request_misc=%7B%22request%5Fid%22%3A%22163430132516780264065592%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=163430132516780264065592&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-3-104535989.pc_search_ecpm_flag&utm_term=ros的CMakeLists详解&spm=1018.2226.3001.4187)
