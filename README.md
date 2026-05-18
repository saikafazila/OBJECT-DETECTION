# OBJECT-DETECTION
This repository provides a complete implementation of an object detection system using state-of-the-art deep learning models such as YOLO (You Only Look Once). The project focuses on detecting and classifying objects—specifically humans and vehicles—in images and video streams, with additional functionality for real-time counting and visualization.






Task-01: Dataset Understanding & Preprocessing

Dataset Structure :
This dataset contains high-resolution images and videos collected using drones flying over urban and suburban environments across various cities in China. These scenes include pedestrians, vehicles, bicycles, and other common objects, captured under different lighting conditions, angles, and motion patterns.The sturcture of the dataset can be described as below:
1.The data folder contains train,test ,validation data and a file with .yaml extension.
2.The train and test /validation data folder is in YOLO model input formatt.
3.The train data is not annotated ,rather the training folder contains image data as well lebel data.The lebel data is a txt file that contains all the information regarding the annotated object.
4.The lebel data is in the format: 
<class_id> <x_center> <y_center> <width> <height>
Where:
1.class_id — Integer label representing the object category. (the lebel files contains upto 10 object ids) 
2.x_center — Horizontal center of the bounding box (normalized from 0 to 1). 
3.y_center — Vertical center of the bounding box (normalized from 0 to 1). 
4.width — Width of the bounding box (normalized). 
5.height — Height of the bounding box (normalized).
5.The .yaml file works as a directory for the YOLO model used ,it contains the class definition of the objects  and the address to the train and test data.
6.The images (.jpg)contain ariel view of urban ans sub urban areas ,containing different classes of objects like human,car,house..etc.
preprocessing/augmentation steps :
1.The original footage taken from the drone  was taken and each object present was defined with integer values.
2.The images were captured into bounding box  using tools like Roboflow,makesence.ai etc.
The annotated images has 
Left coordinate: x_min 
Top coordinate: y_min 
Right coordinate: x_max 
Bottom coordinate: y_max
3.Convert Bounding Boxes to YOLO Format
Given image size:
Width = W 
Height = H 
Convert pixel coordinates to normalized values:
4.Data was saved in one line format for every object.









Challenges Noticed In The Dataset:
The challenges observed were:
1. Small Object Size:Humans appear very small due to aerial view. 
Makes detection difficult. 
2. High Object Density:Many objects appear in a single frame. 
Leads to overlapping bounding boxes. 
 3. Occlusion:Objects are partially hidden by buildings, trees, or other objects. 
 4. Class Imbalance:Some classes (e.g., cars) appear more frequently than humans. 
 5. Lighting & Weather Variations:Different scenes include shadows, low light, and haze.

Task-02: Model Training :








training approach :1. Data Preprocessing

Filtered VisDrone's 12 classes down to person (classes 1,2) and vehicle (classes 4,5,6,9), discarding irrelevant classes like bicycle and ignored regions
Images with zero target annotations were discarded to avoid polluting training with empty samples

2. Label Enhancement (Pseudo-labeling)

Original VisDrone labels are sparse — many visible objects were unannotated
Used a large pretrained YOLOv8x (COCO) as a teacher model to auto-generate dense labels
Merged auto-labels with original human labels using IoU-based deduplication to avoid duplicate boxes
This significantly improved training signal, especially for densely parked vehicles

3. Model

Architecture: YOLOv8s (small) pretrained on COCO
Fine-tuned on the filtered+enhanced VisDrone dataset
Input resolution: 640px (1280px recommended for small object accuracy)

4. Augmentation

Mosaic, MixUp, random flip (including vertical for drone perspective), rotation, scaling, and translation — all chosen specifically for aerial imagery characteristics

5. Optimization

Optimizer: AdamW with cosine learning rate decay
Early stopping with patience=15 to prevent overfitting
Trained for 50 epochs (100 recommended)

6. Inference

Detections filtered by confidence threshold (0.3) and NMS IoU (0.45)
Post-processing counts persons and vehicles per frame from the bounding box class labels
sample predictions/results:These are shown in inference results section of the notebook.



















Task 5 : 
 prediction outputs :

 counting visualization: 
 processed images/results :
 All these requirements are shown in inference results cell of the notebook




Strengths
**Efficient Object Detection**
The YOLO-based model provides fast and real-time detection, making it suitable for aerial surveillance tasks. 
**Focused Class Selection**
By limiting detection to humans and cars, the model becomes more specialized, improving accuracy for the required task. 
End-to-End Pipeline
The system covers the full workflow: 
odataset preprocessing 
omodel training 
oinference 
ovisualization 
ohuman counting 
Human Counting Capability
The implementation includes a simple yet effective counting mechanism based on detected bounding boxes. 
Scalability
The pipeline can be extended to include: 
omore object classes 
oobject tracking (e.g., DeepSORT) 
oreal-time video processing 

 Limitations
Small Object Detection Issue
Due to the aerial perspective, humans often appear very small, reducing detection accuracy. 
Class Imbalance
Some classes (e.g., cars) appear more frequently than humans, which may bias the model. 
Simple Counting Logic
Counting is based only on detections per frame, which may: 
odouble count the same person 
ofail in crowded scenes 
No Tracking Implemented
Without tracking, the system cannot maintain object identity across frames. 
Dependence on Data Quality
Performance heavily depends on annotation quality and dataset diversity. 

 Challenges Faced
Dataset Structure Understanding
The VisDrone dataset has a complex structure, requiring careful exploration to correctly access images and labels. 
Label Handling & Class Mapping
Identifying correct class IDs and mapping them to the required classes (human and car) required careful validation. 
Path and Environment Issues
Errors related to dataset paths and environment setup (Kaggle/Colab) needed debugging. 
Small Object Visibility
Detecting humans from high-altitude drone images was difficult due to low resolution and scale. 
Model Optimization
Balancing training time, batch size, and model performance required experimentation. 
PS:the output couldnt be shown because of the weak internet connnection leading to time scarsity.It took atleast 20 attempts to run this notebook entirely before final success.
