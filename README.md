# person_car_detection

Here I have use YOLOv2 for object detection. for this I have using an open source repository called darknet.

#Installing Darknet
write the below code to clone the darknet repository:

    $ git clone https://github.com/AlexeyAB/darknet.git
    $ cd darknet

After cloning the repository we have to create a make file to use darknet. perform step 3,4,5 for setup the darknet environment

    $ vi Makefile
    $ make
    $ ./darknet

Note: if you are using GPU then put 1 in Makefile otherwise put 0 

If the step 5 runs properly we will see an output as 

     usage: ./darknet <function>
 
If there is an error in step 5 run the following command and then again go to step 5 you will be able to fix the error
"Enter the command to fix the error
 
    $ !chmod 755 ./darknet

  
# Daraset Preparation
  
As the dataset is in coco format. we have to convert that dataset in text file as yolo use txt files for labels.
COCO_to_YOLO_format.ipynb   is the file use to convert it into yolo format.

The Original dataset is in josn file which contain 4 keywords
  1. images
  2. annotations
  3. categories
  4. licenses

we only have to deal with images and annotations
The keywords which are there in images and annotations:

Images {
  1. file_name
  2. width
  3. height
  4. id
  5. license
 
 }

 Annotations {
  1. category_id: 1 for person and 2 for car
  2. image_id: this means that this annotations is for image which have id equal to id attribute in Images keyword
  3. bbox: this is the boundary box values where 1st and 2nd value is the top left corner point coordinate and 3rd is the width of the box and 4th is the height of the box
  4. other keywords like segmentation, area, iscrowd, id,license are not required
 
 }




Here we have to change the format of bbox into YOLO format and save it into a text file with the same name as the name of the image
 
The YOLO format contain 5 things for each annotations
 
1st is the class id for which the annotations is
 
2nd and 3rd are the coordinate of the center of the boundary box
 
4th and 5th are the width and height of the boundary box and this all values should be normalized

Lets say
 
 if x,y,w,h are the coordinate and widht and height of the boundary box in a COCO format
 
 then,
 
  The values in YOLO Format is:
 
    <absolute_x> = <x1>/<image_width>
     
    <absolute_y> = <y1>/<image_height>
     
    <absolute_width> = <w>/<image_width>
     
    <absolute_height> = <h>/<image_height>
     




All the conversion of the COCO format to YOLO format are in COCO_to_YOLO_format.ipynb
     
After creating all the text file for all the images. Keep the images and the text file in same folder


Now we have to split the dataset into training and testing dataset. Here we have to give the path of the images for training and testing

Here I have giving a process.py file which using to split the dataset into training and testing set. Just we have to give the path of the dataset and also the percentage_test value. Here I have split the dataset into 75-25 Ratio where 75% is for training and 25% for testing 

When you run the process.py file the 2 text file will be created one contain training images path and another contain test images path.

Here we are done with creating a datset in YOLO format
    
