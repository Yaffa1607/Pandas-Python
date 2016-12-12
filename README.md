To begin , download the darknet-2-classes folder. 
The cfg that I used is : darknet/cfg/yolo_2class_box11.cfg

Pre trained weights can be found here : https://drive.google.com/file/d/0B0-t7TXfMJHOYU9TaEVILUF5RHc/view?usp=sharing
The pre-compiled software with source code package for the demo: darknet-video-2class.zip

If you would like to repeat the training process or get a feel of YOLO, you can download the data I collected and the annotations I labeled.

images:

labels: 

To start training : 

1. Collect Data and Annotation

(1). For Videos, create static images.

(2). For Images, we can use BBox-Label-Tool to label objects.

2. Create Annotation in Darknet Format

(1). If we choose to use VOC data to train, use scripts/voc_label.py to convert existing VOC annotations to darknet format.

(2). If we choose to use our own collected data, use scripts/convert.py to convert the annotations.

At this step, we should have darknet annotations (.txt) and a training list (.txt).

Upon labeling, the format of annotations generated by BBox-Label-Tool is:

class_number

box1_x1 box1_y1 box1_width box1_height

box2_x1 box2_y1 box2_width box2_height

….

After conversion, the format of annotations converted by scripts/convert.py is:

class_number box1_x1_ratio box1_y1_ratio box1_width_ratio box1_height_ratio

class_number box2_x1_ratio box2_y1_ratio box2_width_ratio box2_height_ratio

….

The exact conversion techniques from bounding box from the format required can be found in the scripts/convert.py
Note that each image corresponds to an annotation file. But we only need one single training list of images. Remember to put the folder “images” and folder “annotations” in the same parent directory, as the darknet code look for annotation files this way (by default).

You can download some examples to understand the format:

after_conversion.txt :https://github.com/Yaffa1607/Pandas-Python/blob/master/darknet-2-class/labels/yieldsign/Frame_movie_1_1.txt

training_list.txt : https://github.com/Yaffa1607/Pandas-Python/blob/master/darknet-2-class/training_list.txt

3. Modify Some Code

(1) In src/yolo.c, change class numbers and class names. (And also the paths to the training data and the annotations, i.e., the list we obtained from step 2. )

If we want to train new classes, in order to display correct png Label files, we also need to moidify and rundata/labels/make_labels

(2) In src/yolo_kernels.cu, change class numbers.

(3) Now we are able to train with new classes, but there is one more thing to deal with. In YOLO, the number of parameters of the second last layer is not arbitrary, instead it is defined by some other parameters including the number of classes, the side(number of splits of the whole image). Please read the paper.

(5 x 2 + number_of_classes) x 7 x 7, as an example, assuming no other parameters are modified.

Therefore, in cfg/yolo.cfg, change the “output” in line 218, and “classes” in line 222.

(4) Now we are good to go. If we need to change the number of layers and experiment with various parameters, just mess with the cfg file. For the original yolo configuration, we have the pre-trained weights to start from. For arbitrary configuration, I’m afraid we have to generate pre-trained model ourselves.

4. Start Training

Try something like:

./darknet yolo train cfg/yolo.cfg extraction.conv.weights

You can join the  Google Group; there are many brilliant people answering questions out there for anymore questions. 

https://groups.google.com/forum/#!forum/darknet



Things to keep in mind : 
1. The images and training folder should be two folders with the same parent directory as in the darknet folder
2. The training list should be just 1 file with all path names
3. The labels flder should have the data for each image as a separate txt file. 
4. You can change the number of  classes as shown above , be sure to create a label for each class. 
5. Common errors I found were , upon training , “cannot load image”. Check your txt file for training ,there is a difference between Python2 and Python3 in treating spaces in text files.
6. Add ~500-1000 images for each class
