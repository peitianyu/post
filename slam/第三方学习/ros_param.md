---
title: param
date: 2022-4-26 12:30:12
categories:
  - slam
  - ros
tags:
  - ros
---

# 参考网址:

[(128条消息) ROS-节点参数param_TYINY的博客-CSDN博客_node.param](https://blog.csdn.net/sinat_16643223/article/details/112441563?ops_request_misc=%7B%22request%5Fid%22%3A%22165093661116781435481632%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=165093661116781435481632&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-4-112441563.142^v9^control,157^v4^control&utm_term=ros+param&spm=1018.2226.3001.4187)

[(128条消息) 【ROS学习】ROS param 的使用_wongHome的博客-CSDN博客_ros::param::get](https://blog.csdn.net/qq_39779233/article/details/108411778?ops_request_misc=%7B%22request%5Fid%22%3A%22165094280716781685346224%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=165094280716781685346224&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-108411778.142^v9^control,157^v4^control&utm_term=ros+param&spm=1018.2226.3001.4187)

# 例程

```c++
include "ros/ros.h" 
#include <cstdlib> 
 
using namespace std; 
 
int main(int argc,char** argv) 
{ 
    //initial and name node 
    ros::init(argc,argv,"node_param"); 
    if(argc!=2) 
    { 
        cout<<"Error command paramter! Please run command eg:\n"\ 
            <<"rosrun book_param book_param 1\n"\ 
            <<"help information:\n"\ 
            <<" 1 ------ set param mode(ros::param::set())\n"\ 
            <<" 2 ------ set param mode(ros::NodeHandle::setParam())\n"\ 
            <<" 3 ------ get param mode(ros::param::get())\n"\ 
            <<" 4 ------ get param mode(ros::NodeHandle::getParam())\n"\ 
            <<" 5 ------ get param mode(ros::NodeHandle::param())\n"\ 
            <<endl; 
        return 1; 
    } 
    //create node handle 
    ros::NodeHandle nh; 
    //param variable 
    int IntParam; 
    string StrParam; 
    bool isIntParam, isStrParam; 
    //mode flag 
    int flag = atoi(argv[1]); 
     // set or get param with different ways 
    switch(flag) 
    { 
        case 1: 
            ROS_INFO("set param mode(ros::param::set()):"); 
            ros::param::set("IntParam",1); 
            ros::param::set("StrParam","stringdemo"); 
            break; 
        case 2: 
            ROS_INFO("set param mode(ros::NodeHandle::setParam()):"); 
            nh.setParam("IntParam",1); 
            nh.setParam("StrParam","stringdemo"); 
            break; 
        case 3: 
            ROS_INFO("get param mode(ros::param::get()):"); 
            isIntParam = ros::param::get("IntParam",IntParam); 
            isStrParam = ros::param::get("StrParam",StrParam); 
 
            if(isIntParam){ 
                ROS_INFO("The IntParam is:%d",IntParam); 
            }else{ 
                ROS_INFO("Get IntParam fail!"); 
            } 
 
            if(isIntParam){ 
                ROS_INFO("The StrParam is:%s",StrParam.c_str()); 
            }else{ 
                ROS_INFO("Get StrParam fail!"); 
            } 
            break; 
        case 4: 
            ROS_INFO("get param mode(ros::NodeHandle::getParam()):"); 
            isIntParam = nh.getParam("IntParam",IntParam); 
            isStrParam = nh.getParam("StrParam",StrParam); 
 
            if(isIntParam){ 
                ROS_INFO("The IntParam is:%d",IntParam); 
            }else{ 
                ROS_INFO("Get IntParam fail!"); 
            } 
 
            if(isIntParam){ 
                ROS_INFO("The StrParam is:%s",StrParam.c_str()); 
            }else{ 
                ROS_INFO("Get StrParam fail!"); 
            } 
            break; 
        case 5: 
            ROS_INFO("get param mode(ros::NodeHandle::param()):"); 
            //warning: this way will set default value when get no param! 
            nh.param("IntParam",IntParam,11); 
            // be careful when use ros::NodeHandle::param get string param! 
            nh.param<std::string>("StrParam",StrParam,"stringdemo_default"); 
            ROS_INFO("\nThe IntParam is:%d\nThe StrParam is:%s",IntParam,StrParam.c_str()); 
            break; 
        default: 
            ROS_INFO("flag value is not in range: [1,5]"); 
    } 
    return 0;
    }
}
```

# launch文件配置

```xml
<launch>
	<!--param参数配置-->
	<param name="param1" value="1" />
	<param name="param2" value="2" />
	<!--param name="table_description" command="$(find xacro)/xacro.py $(find gazebo_worlds)/objects/table.urdf.xacro" /-->
	<!--rosparam参数配置-->
	<rosparam>   
        param3: 3
        param4: 4
        param5: 5
    </rosparam>
	<!--以上写法将参数转成YAML文件加载，注意param前面必须为空格，不能用Tab，否则YAML解析错误-->
	<!--rosparam file="$(find robot_sim_demo)/config/xbot-u_control.yaml" command="load" /-->
	<node pkg="param_demo" type="param_demo" name="param_demo" output="screen" >
	</node>
</launch>
```

# yaml文件

```yaml
param1:1
param2:"str"
```

