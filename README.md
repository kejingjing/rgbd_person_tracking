# R-GBD Person Tracking
R-GBD Person Tracking (RGPT) is a ROS framework for detecting and tracking people from a mobile robot.

## Requirements

AT requires the following packeges to build:

* OpenCV 
* Boost
* PCL
* ROS Indigo
* OpenMP

## How to build
RGPT works under Linux 14.04 and ROS Indigo. For building the source, you
have to put the repository inside your catking workspace and then
follows the following command sequence:

* rospack profile
* catkin_make

## How to setup

_INPUT:_
	Check the file people_detection_complete.launch inside the folder
	people_detection/launch
	
	Example:
	<launch>
		<arg name="prefix" value="/top_camera" />
		<node name="ground_detector" pkg="ground_detector" type="ground_detector_node" output="screen">
			<param name="theta" value="12"/> <!-- xtion tilt angle -->
			<param name="ty" value="1.5"/> <!-- xtion y traslation -->
			<param name="debug" value="false"/> <!-- show the segmentation output -->
			<param name="groundThreshold" value="0.05" /> <!-- under this threshold is considered ground --> 
			<param name="voxel_size" value="0.06" /> <!-- voxel size -->
			<param name="min_height" value="1.0" /> <!-- min blob height -->
			<param name="max_height" value="2.0" /> <!-- max blob height -->
			<param name="min_head_distance" value="0.3" /> <!-- min distance between two heads -->
			<param name="sampling_factor" value="3" /> <!-- sampling cloud factor -->
			<param name="apply_denoising" value="false" /> 
			<param name="mean_k_denoising" value="5" /> <!-- meanK for denoising (the higher it is, the stronger is the filtering) -->
			<param name="std_dev_denoising" value="0.3" /> <!-- standard deviation for denoising (the lower it is, the stronger is the filtering) -->
			<param name="max_distance" value="6" /> <!-- detection rate in meters -->
			<param name="depth_topic" value="$(arg prefix)/depth/image_raw" />
			<param name="camera_info_topic" value="$(arg prefix)/depth/camera_info" />
			<param name="rgb_topic" value="$(arg prefix)/rgb/image_raw" />			
		</node>
		
		<node name="dispatcher_node" pkg="dispatcher_node" type="dispatcher_node" output="screen">
			<param name="min" value="1.5"/> <!-- min value for tracking only the face -->
		</node>
		
		<node name="people_detector" pkg="people_detection" type="people_detection_node" output="screen">
			<param name="dataset" value="$(find people_detection)/config/inria_detector.xml"/> <!-- dataset filename -->
			<param name="confidence" value="65."/> <!--min confidence for considering the blob as a person -->
			<param name="image_scaling_factor" value="1.5"/><!--scaling factor for image detection (if you increase it, the detection speed increses and the precision decreses) -->
		</node>
		
		<node name="visual_tracker" pkg="visual_tracker" type="visual_tracker" output="screen">
			<param name="image_scaling_factor" value="1.5"/><!--scaling factor for image detection (if you increase it, the detection speed increses and the precision decreses) -->
		</node>
		
	</launch>
	
_OUTPUT:_
	topic: /tracks
	Message type: Traks
		A vector containing:
			int32 id
			Point2i point2D
			Point3D point3D	
			
			
			
## How to use
Once the build phase has been successfully, you can use RGPT by launching the following command:

* roslaunch people_detection people_detection_complete.launch

## How it works:

RGPT is divided in three steps:

* RGBD Segmentation
* People Detection
* People Tracking

You can find more information in the paper: COACHES: An assistance Multi-Robot System in public areas [<a href="http://research.sabanciuniv.edu/33937/1/PID4962853.pdf" target="_blank">link</a>]
