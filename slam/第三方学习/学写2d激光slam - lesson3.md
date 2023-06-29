---
title: 学写2d激光slam-lesson3
date: 2022-2-13 12:30:12
categories:
  - slam
  - 第三方学习
tags:
  - ros
  - slam
---

# 参考网址:

  [xiangli0608/Creating-2D-laser-slam-from-scratch: 从零开始创建二维激光SLAM (github.com)](https://github.com/xiangli0608/Creating-2D-laser-slam-from-scratch) 

 [李想的博客_李太白lx_CSDN博客-从零开始搭二维激光SLAM,激光SLAM,cartographer领域博主](https://blog.csdn.net/tiancailx?spm=1011.2124.3001.5113) 

 [(107条消息) ROS中点云学习（九）：sensor_msgs::LaserScan转sensor_msgs::PointCloud2_00 2的博客-CSDN博客](https://blog.csdn.net/weixin_41170972/article/details/120015410?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_title~default-1.pc_relevant_paycolumn_v3&spm=1001.2101.3001.4242.2&utm_relevant_index=4) 

# lesson3-PLICP

## 简介

 使用点到线距离最小的方式进行ICP的计算，收敛速度快很多，同时精度也更高一些． 

## 常见坐标系

```bash
map: 地图坐标系，也被称为世界坐标系，是静止不动的
odom: 里程计坐标系，相对于map来说一般情况下是静止的，有些情况下会变动（定位节点为了修正机器人的位姿从而改变了map->odom间的坐标变换）
base_link: 代表机器人的旋转中心的坐标系，相对于odom来说base_link是运动的
laser_link: 激光雷达的坐标系，相对于base_link来说是静止的，因为雷达装在机器人上，雷达不会自己飞起来
根据依赖关系:map -> odom -> base_link -> laser_link
```

## 基于PL-ICP的帧间匹配

作者在做此实验时采用的是csm库,代码中表现为

```c
input_.laser_ref = prev_ldp_scan_;
input_.laser_sens = curr_ldp_scan;
sm_icp(&input_, &output_);
```

## 基于PL-ICP的里程计

由pl-icp匹配得到delta_x,delta_y,delta_theta乘以时间,并加入匀速模型,得到里程计,这里值得注意的是关于tf转换的使用,尤其麻烦.

```c
void ScanMatchPLICP::ScanMatchWithPLICP(LDP &curr_ldp_scan, const ros::Time &time)
{
    // CSM is used in the following way:
    // The scans are always in the laser frame
    // The reference scan (prevLDPcan_) has a pose of [0, 0, 0]
    // The new scan (currLDPScan) has a pose equal to the movement
    // of the laser in the laser frame since the last scan
    // The computed correction is then propagated using the tf machinery

    prev_ldp_scan_->odometry[0] = 0.0;
    prev_ldp_scan_->odometry[1] = 0.0;
    prev_ldp_scan_->odometry[2] = 0.0;

    prev_ldp_scan_->estimate[0] = 0.0;
    prev_ldp_scan_->estimate[1] = 0.0;
    prev_ldp_scan_->estimate[2] = 0.0;

    prev_ldp_scan_->true_pose[0] = 0.0;
    prev_ldp_scan_->true_pose[1] = 0.0;
    prev_ldp_scan_->true_pose[2] = 0.0;

    input_.laser_ref = prev_ldp_scan_;
    input_.laser_sens = curr_ldp_scan;

    // 匀速模型，速度乘以时间，得到预测的odom坐标系下的位姿变换
    double dt = (time - last_icp_time_).toSec();
    double pr_ch_x, pr_ch_y, pr_ch_a;
    GetPrediction(pr_ch_x, pr_ch_y, pr_ch_a, dt);

    tf2::Transform prediction_change;
    CreateTfFromXYTheta(pr_ch_x, pr_ch_y, pr_ch_a, prediction_change);

    // account for the change since the last kf, in the fixed frame
    // 将odom坐标系下的坐标变换 转换成 base_in_odom_keyframe_坐标系下的坐标变换
    prediction_change = prediction_change * (base_in_odom_ * base_in_odom_keyframe_.inverse());

    // the predicted change of the laser's position, in the laser frame
    // 将base_link坐标系下的坐标变换 转换成 雷达坐标系下的坐标变换
    tf2::Transform prediction_change_lidar;
    prediction_change_lidar = laser_to_base_ * base_in_odom_.inverse() * prediction_change * base_in_odom_ * base_to_laser_;

    input_.first_guess[0] = prediction_change_lidar.getOrigin().getX();
    input_.first_guess[1] = prediction_change_lidar.getOrigin().getY();
    input_.first_guess[2] = tf2::getYaw(prediction_change_lidar.getRotation());

    // If they are non-Null, free covariance gsl matrices to avoid leaking memory
    if (output_.cov_x_m)
    {
        gsl_matrix_free(output_.cov_x_m);
        output_.cov_x_m = 0;
    }
    if (output_.dx_dy1_m)
    {
        gsl_matrix_free(output_.dx_dy1_m);
        output_.dx_dy1_m = 0;
    }
    if (output_.dx_dy2_m)
    {
        gsl_matrix_free(output_.dx_dy2_m);
        output_.dx_dy2_m = 0;
    }
    
    start_time_ = std::chrono::steady_clock::now();
    // 调用csm进行plicp计算
    sm_icp(&input_, &output_);

    end_time_ = std::chrono::steady_clock::now();
    time_used_ = std::chrono::duration_cast<std::chrono::duration<double>>(end_time_ - start_time_);
    // std::cout << "PLICP计算用时: " << time_used_.count() << " 秒。" << std::endl;

    tf2::Transform corr_ch;

    if (output_.valid)
    {
        // 雷达坐标系下的坐标变换
        tf2::Transform corr_ch_l;
        CreateTfFromXYTheta(output_.x[0], output_.x[1], output_.x[2], corr_ch_l);

        // 将雷达坐标系下的坐标变换 转换成 base_link坐标系下的坐标变换
        corr_ch = base_to_laser_ * corr_ch_l * laser_to_base_;

        // 更新 base_link 在 odom 坐标系下 的坐标
        base_in_odom_ = base_in_odom_keyframe_ * corr_ch;

        latest_velocity_.linear.x = corr_ch.getOrigin().getX() / dt;
        latest_velocity_.angular.z = tf2::getYaw(corr_ch.getRotation()) / dt;
    }
    else
    {
        ROS_WARN("not Converged");
    }

    // 发布tf与odom话题
    PublishTFAndOdometry();

    // 检查是否需要更新关键帧坐标
    if (NewKeyframeNeeded(corr_ch))
    {
        // 更新关键帧坐标
        ld_free(prev_ldp_scan_);
        prev_ldp_scan_ = curr_ldp_scan;
        base_in_odom_keyframe_ = base_in_odom_;
    }
    else
    {
        ld_free(curr_ldp_scan);
    }

    last_icp_time_ = time;
}

```

