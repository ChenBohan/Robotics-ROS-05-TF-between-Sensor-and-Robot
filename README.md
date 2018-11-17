# Robotics-ROS-05-TF-between-Sensor-and-Robot
ROS Tutorial: Setting up your robot using tf

Ref:

[Setting up your robot using tf](http://wiki.ros.org/navigation/Tutorials/RobotSetup/TF)

[ROS探索总结（二十二）——设置机器人的tf变换](http://www.guyuehome.com/355?replytocom=47816)

## Overview

<img src="https://github.com/ChenBohan/Robotics-ROS-05-TF-between-Sensor-and-Robot/blob/master/readme_img/relationship.png" width = "80%" height = "80%" div align=center />

<img src="https://github.com/ChenBohan/Robotics-ROS-05-TF-between-Sensor-and-Robot/blob/master/readme_img/tf_tree.png" width = "80%" height = "80%" div align=center />

<img src="https://github.com/ChenBohan/Robotics-ROS-05-TF-between-Sensor-and-Robot/blob/master/readme_img/overview.jpg" width = "100%" height = "100%" div align=center />

## Broadcasting a Transform

```cpp
    broadcaster.sendTransform(
      tf::StampedTransform(
        tf::Transform(tf::Quaternion(0, 0, 0, 1), tf::Vector3(0.1, 0.0, 0.2)),
        ros::Time::now(),"base_link", "base_laser"));
```

This is where the real work is done. Sending a transform with a TransformBroadcaster requires five arguments. 
First, we pass in the rotation transform, which is specified by a btQuaternion for any rotation that needs to occur between the two coordinate frames. In this case, we want to apply no rotation, so we send in a btQuaternion constructed from pitch, roll, and yaw values equal to zero. 

Second, a btVector3 for any translation that we'd like to apply. We do, however, want to apply a translation, so we create a btVector3 corresponding to the laser's x offset of 10cm and z offset of 20cm from the robot base. 

Third, we need to give the transform being published a timestamp, we'll just stamp it with ros::Time::now(). 

Fourth, we need to pass the name of the parent node of the link we're creating, in this case "base_link." 

Fifth, we need to pass the name of the child node of the link we're creating, in this case "base_laser."

## Using a Transform

```cpp
  try{
    geometry_msgs::PointStamped base_point;
    listener.transformPoint("base_link", laser_point, base_point);

    ROS_INFO("base_laser: (%.2f, %.2f. %.2f) -----> base_link: (%.2f, %.2f, %.2f) at time %.2f",
        laser_point.point.x, laser_point.point.y, laser_point.point.z,
        base_point.point.x, base_point.point.y, base_point.point.z, base_point.header.stamp.toSec());
  }
```

Now that we have the point in the "base_laser" frame we want to transform it into the "base_link" frame. 

To do this, we'll use the TransformListener object, and call transformPoint() with three arguments: the name of the frame we want to transform the point to ("base_link" in our case), the point we're transforming, and storage for the transformed point. 

So, after the call to transformPoint(), base_point holds the same information as laser_point did before only now in the "base_link" frame.
