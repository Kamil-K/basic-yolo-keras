
# YOLOv2 in Keras and Applications

This project aims to implement all the details of YOLOv2 in Keras with Tensorflow backend. It also explores to apply YOLOv2 to some interesting applications. 

## Todo list:
- [x] Warmup training
- [x] Raccoon detection
- [x] Self-driving car
- [x] Kangaroo detection
- [ ] SqueezeNet backend
- [ ] MobileNet backend
- [ ] Multiscale training

## Some example applications:

### Raccon detection
<a href="https://www.youtube.com/watch?v=aibuvj2-zxA" rel="some text"><p align="center"><img src="https://i.imgur.com/6okeDjz.jpg" height="300"></p></a>

Dataset from shttps://github.com/datitran/raccoon_dataset.

### Kangaroo detection
<a href="https://youtu.be/vjmFzEP1qZw?t=34" rel="some text"><p align="center"><img src="https://i.imgur.com/v606VZX.jpg" height="300"></p></a>

Dataset to be released.

### Self-driving Car
<a href="https://www.youtube.com/watch?v=oYCaILZxEWM" rel="some text"><p align="center"><img src="https://i.imgur.com/kEc9ptL.jpg" height="300"></p></a>

Trained on COCO dataset and did detection on a random dashcam video.

## Usage for python code
### Data preparation
Download the Raccoon dataset from from https://github.com/datitran/raccoon_dataset.

Organize the dataset into 4 folders:

+ train_image_folder <= the folder that contains the train images.

+ train_annot_folder <= the folder that contains the train annotations in VOC format.

+ valid_image_folder <= the folder that contains the validation images.

+ valid_annot_folder <= the folder that contains the validation annotations in VOC format.
    
There is a one-to-one correspondence by file name between images and annotations. If the validation set is empty, the training set will be automatically splitted into the training set and validation set using the ratio of 0.8.

### Edit the configuration file
The configuration file is a json file, which looks like this:

```python
{
    "model" : {
        "architecture":         "Full Yolo",    # should be either "Tiny Yolo" or "Full Yolo"
        "input_size":           416,
        "anchors":              [0.57273, 0.677385, 1.87446, 2.06253, 3.33843, 5.47434, 7.88282, 3.52778, 9.77052, 9.16828],
        "max_box_per_image":    10,        
        "labels":               ["raccoon"]
    },

    "train": {
        "train_image_folder":   "/home/andy/data/raccoon_dataset/images/",
        "train_annot_folder":   "/home/andy/data/raccoon_dataset/anns/",      
          
        "train_times":          10,             # the number of time to cycle through the training set, useful for small datasets
        "pretrained_weights":   "",             # specify the path of the pretrained weights, but it's fine to start from scratch
        "batch_size":           16,             # the number of images to read in each batch
        "learning_rate":        1e-4,           # the base learning rate of the default Adam rate scheduler
        "nb_epoch":             50,             # number of epoches
        "warmup_batches":       100,            # the number of initial batches during which the sizes of the 5 boxes in each cell is forced to match the sizes of the 5 anchors, this trick seems to improve precision emperically

        "object_scale":         5.0 ,           # determine how much to penalize wrong prediction of confidence of object predictors
        "no_object_scale":      1.0,            # determine how much to penalize wrong prediction of confidence of non-object predictors
        "coord_scale":          1.0,            # determine how much to penalize wrong position and size predictions (x, y, w, h)
        "class_scale":          1.0,            # determine how much to penalize wrong class prediction

        "debug":                true            # turn on/off the line that prints current confidence, position, size, class losses and recall
    },

    "valid": {
        "valid_image_folder":   "",
        "valid_annot_folder":   "",

        "valid_times":          1
    }
}

```

The model section defines the type of the model to construct as well as other parameters of the model such as the input image size and the list of anchors. Two achitectures are supported at the moment: "Tiny Yolo" and "Full Yolo". 

Download pretrained weights of tiny yolo: https://1drv.ms/u/s!ApLdDEW3ut5fa5Z9jibkqUGG-CA

Download pretrained weights of full yolo: https://1drv.ms/u/s!ApLdDEW3ut5fbAMIhQAO1A26n2A

Important! Plese remember to change "labels" to the list of labels that you want to detect.

### Start the training process

`python train.py -c config.json`

By the end of this process, the code will write the weights of the best model to file best_weights.h5. The training process stops when the loss on the validation set is not improved in 3 consecutive epoches.

### Perform detection using trained weights on an image by running
`python predict.py -c config.json -w /path/to/best_weights.h5 -i /path/to/image`

It carries out detection on the image and write the image with detected bounding boxes to the same folder.

## Usage for jupyter notebook

Refer to the notebook (https://github.com/experiencor/basic-yolo-keras/blob/master/Yolo%20Step-by-Step.ipynb) for a complete walk-through implementation of YOLOv2 from scratch (training, testing, and scoring).

## Evaluation of the current implementation:

| Train        | Test          | mAP (with this implementation) | mAP (on released weights) |
| -------------|:--------------|:------------------------:|:-------------------------:|
| COCO train   | COCO val      | 28.6 |    42.1 |
