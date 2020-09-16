# hsy_node

1. /nesfr3/1/image_converter
image_converter node subscribes orginal img data from fisheye camera and publish inference results creating child thread. Inference is done by TensorRT optimized SSD in child thread while main thread drawing detection results and displaying video.

1.1. Package
nesfr3_human_detection

1.2. Topics
Subscribes
- nesfr3/1/fisheye_camera/image_raw/image_topics

fisheye_camera/image_raw/image_topics contains image data obtained by fisheye camera.

Publishes
- nesfr3/1/bounding_boxes

1.3. Role
class TrtThread: It implements the child thread (read images from cam to TRT inferencing). The child thread stores the input image and detectino results (global & condition variable) 
- run(): run until running flag is set to False in main thread. It detects human by function trt_ssd.detect(img, self.conf_th). Please refer detect.py .

class image_converter: This class exists for parsing, and setting robot args. It publishes and subscribes image.
- callback(self,data): Converting ROS image messages to OpenCV images, put cv_image to img_raw_queue, put bounidng boxes value
 
bbox(box1, box2): returns the iou of tow bbox. get the coordinates of bbox (if x1min>x1max x1min -w, else do nothing), get the coordinates of intersec rec (if x1min>x1max and x2min<x2max calculate iou1 as original(b1_x1 ..) and io2 as previous original box value(b1_x1+w) and set iou as max

calculate_iou(coordinates..): find intersection and calculate iou by ratio of area

duplicate_box_removal(bbox): remove bbox for same object

2. /nesfr3/1/object3d_detecter

2.1. Package
object3d_detecter

2.2. Topics
Subscribes
- nesfr3/1/lidar/points

lidar/points contains points data from lidar sensor.

Publishes
- nesfr3/1/object3d_detector/poses
poses contain information of points such as position(centroid)

- nesfr3/1/cluster
It contains cluster property such as min, max, centroid.

2.3. Role
class Object3dDetector: contains publisher / subscriber, arguments and functions 
Object3dDetector::pointCloudCallback: It calls function extractCluster, and classify.

extractCluster: It compare distances of pc_indices(from lidar) with range. If distance is in range, then push it to indices_array. (do space divde) And 
set cluster_size min, max value. Also it conducts clustering, set(calculate) width, height, centroid. And limit size(not accurate).

classify: marking (color)
