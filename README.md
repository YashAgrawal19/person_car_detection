# person_car_detection

Here I have use YOLOv2 for object detection. For this I have using an open source repository called darknet.

YOLO.ipynb is the main file where we will train our model, JSON_to_YOLOformat.ipynb is the file where we have converted the json format dataset into YOLO format,
process.py file is used to split the training and testing dataset

# Installing Darknet

write the below code to clone the darknet repository:

    $ git clone https://github.com/AlexeyAB/darknet.git
    $ cd darknet

After cloning the repository we have to create a make file to use darknet. Perform below commands to setup the darknet environment

    $ vi Makefile
    $ make
    $ ./darknet

Note: if you are using GPU then put 1 in Makefile otherwise put 0 

If the step 5 runs properly we will see an output as 

     usage: ./darknet <function>
 
If there is an error in step 5 run the following command and then again go to step 5 you will be able to fix the error.
Enter the command to fix the error
 
    $ !chmod 755 ./darknet

  
# Daraset Preparation
  
As the dataset is in coco format. we have to convert that dataset in text file as yolo use txt files for labels.
COCO_to_YOLO_format.ipynb   is the file use to convert it into yolo format.

The Original dataset is in json file which contain 4 keywords
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

Below is the format of the Json file

![Alt text](Images/json_structure.png?raw=True "title")


Here we have to change the format of bbox into YOLO format and save it into a text file with the same name as the name of the image
 
The YOLO format contain 5 things for each annotations 1st is the class id for which the annotations is, 2nd and 3rd are the coordinate of the center of the boundary box, 4th and 5th are the width and height of the boundary box and this all values should be normalized

Lets say if,
 
   x, y, w, h are the coordinate and width and height of the boundary box in a COCO format
 
 then,
 
  The values in YOLO Format is:
 
    <absolute_x> = <x1>/<image_width>
     
    <absolute_y> = <y1>/<image_height>
     
    <absolute_width> = <w>/<image_width>
     
    <absolute_height> = <h>/<image_height>
     
     
     
 Below is the format of Json File
 
![Alt text](Images/previous_format.png?raw=True "title")  

After converting it into YOLO format

![Alt text](Images/new_format.png?raw=True "title")







All the conversion of the COCO format to YOLO format are in COCO_to_YOLO_format.ipynb
     
After creating all the text file for all the images. Keep the images and the text file in same folder.

The Folder should look like this

![Alt text](Images/final_folder.png?raw=True "title")


Now after creating the folder we have to split the dataset into training and testing set.

Here I have giving a process.py file which using to split the dataset into training and testing set. Just we have to give the path of the dataset and also the percentage_test value. Here I have split the dataset into 75-25 Ratio where 75% is for training and 25% for testing 

When you run the process.py file the 2 text file will be created one contain training images path and another contain test images path.

Here we are done with creating a datset in YOLO format

# Preparing YOLOv2 configuration files

YOLOv2 needs certain specific files to know how and what to train. We’ll be creating these three files.
  1. cfg/obj.data
  2. cfg/obj.name
  3. cfg/yolo-obj.cfg
 
Let’s start by creating obj.data and filling it with this content. This basically says that we are training two class, what the train and validation set files are (which we have just created using process.py file just add the path of both the files) and what file contains the names for the categories we want to detect.

    classes= 2 
    train  = train.txt  
    valid  = test.txt 
    names =  cfg/obj.name  
    backup = backup

Now for obj.name, Its just contain name of the classes in the order in which we have given the class value as in our case we have given person as value 0 so it will come first and then class Car will come next

    Person
    Car

And Finally in the last we have to edit the yolo-obj.cfg file. This is the file which contain the model and we have to change specific things in this file.

    Line 2: change the batch size. I have put 24
    Line 120: classes=2, the number of categories we want to detect
    Line 114: filters=(classes + 5)*5 in our case filters=35

To start training, YOLOv2 requires a set of convolutional weights. So for that we have used darknet53.conv.74 weight which are set on Imagenet 

Now we are good to go for training

For training we have to run the following command:

    $ !./darknet detector train cfg/obj.data cfg/yolo-obj.cfg darknet53.conv.74 -dont_show

This command will start the training of the model and store the weights in backup folder. If due to some reason if the training stops we can resume it by entering the following command

    $ ./darknet detector train cfg/obj.data cfg/yolo-obj.cfg backup/yolo-obj_last.weights -dont_show

Note: When completed 1000 iteration it will automatically store weights file and kill the process once the average loss is less than 0.06 to get good a accuracy.

# Results

Now we have our model weight. Lets test it on one of the image. For testing it we have to run the following command:

    $ !./darknet detector test cfg/obj.data cfg/yolo-obj.cfg backup/yolo-obj_14000.weights person.jpeg

I have downloaded some images on which I have tested my model and got good results here are some of the results

![Alt text](Images/person_pred.jpg?raw=True "title")
![Alt text](Images/car_pred.jpg?raw=True "title")
![Alt text](Images/person_car_pred.jpg?raw=True "title")


