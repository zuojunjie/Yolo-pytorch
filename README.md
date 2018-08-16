# [PYTORCH] YOLO (You Only Look Once)

## Introduction

Here is my pytorch implementation of the model described in the paper **You Only Look Once:
Unified, Real-Time Object Detection** [paper](https://arxiv.org/abs/1506.02640). 

## How to use my code
With my code, you can:
* **Train your model from scratch**
* **Train your model with my trained model**
* **Evaluate test images with either my trained model or yours**

## Datasets:

I used 4 different datases: VOC2007, VOC2012, COCO2014 and COCO2017. Statistics of datasets I used for experiments is shown below

| Dataset                | Classes | #Train images/objects | #Validation images/objects |
|------------------------|:---------:|:-----------------------:|:----------------------------:|
| VOC2007                |    20   |      5011/12608       |           4952/-           |
| VOC2012                |    20   |      5717/13609       |           5823/13841       |
| COCO2014               |    80   |         83k/-         |            41k/-           |
| COCO2017               |    20   |         118k/-        |             5k/-           |

Create a data folder under the repository,

```
cd {repo_root}
mkdir data
```

- **VOC**:
  Download the voc images and annotations from [VOC2007](http://host.robots.ox.ac.uk/pascal/VOC/voc2007) or [VOC2012](http://host.robots.ox.ac.uk/pascal/VOC/voc2012). Make sure to put the files as the following structure:
  ```
  VOCDevkit
  ├── VOC2007
  │   ├── Annotations  
  │   ├── ImageSets
  │   ├── JPEGImages
  │   ├── ...
  └── VOC2012
      ├── Annotations  
      ├── ImageSets
      ├── JPEGImages
      └── ...
  ```
  
- **COCO**:
  Download the coco images and annotations from [coco website](http://cocodataset.org/#download). Make sure to put the files as the following structure:
  ```
  COCO
  ├── annotations
  │   ├── instances_train2014.json
  │   ├── instances_train2017.json
  │   ├── instances_val2014.json
  │   └── instances_val2017.json
  │── images
  │   ├── train2014
  │   ├── train2017
  │   ├── val2014
  │   └── val2017
  └── anno_pickle
      ├── COCO_train2014.pkl
      ├── COCO_val2014.pkl
      ├── COCO_train2017.pkl
      └── COCO_val2017.pkl
  ```
  
## Setting:

* **Model structure**: In compared to the paper, I changed structure of top layers, to make it converge better. You could see the detail of my YoloNet in **src/yolo_net.py**.
* **Data augmentation**: I performed dataset augmentation, to make sure that you could re-trained my model with small dataset (~500 images). Techniques applied here includes HSV adjustment, crop, resize and flip with random probabilities
* **Loss**: The losses for object and non-objects are combined into a single loss in my implementation
* **Optimizer**: My learning rate schedule is as follows: 
 - aa
 - bb

I almost keep default setting as described in the paper. For optimizer, I use **Adam** optimizer with initial learning rate of 0.001 instead of SGD with learning rate of 0.01.

Additionally, in the original model, one epoch is seen as a loop over batch_size x num_batch records (128x5000 or 128x10000 or 128x30000), so it means that there are records used more than once for 1 epoch. In my model, 1 epoch is a complete loop over the whole dataset, where each record is used exactly once.

## Training

If you want to train a model with common dataset and default parameters, you could run:
- **python train.py -d dataset_name**: For example, python train.py -d dbpedia

If you want to train a model with common dataset and your preference parameters, like the depth of network, you could run:
- **python train.py -d dataset_name -t depth**: For example, python train.py -d dbpedia -t 9

If you want to train a model with your own dataset, you need to specify the path to input and output folders:
- **python train.py -i path/to/input/folder -o path/to/output/folder**

## Test

You could find some trained models I have trained in [link](https://drive.google.com/open?id=1gx1qvgu8rZRtEgkCMA9KqJZtFwjr8fc-)

## Experiments:

I run experiments in 2 machines, one with NVIDIA TITAN X 12gb GPU and the other with NVIDIA quadro 6000 24gb GPU. For small and large models, you need about 1.6 gb GPU and 3.5 gb GPU respectively.

Results for test set are presented as follows:  A(B)/C:
- **A** is accuracy reproduced here.
- **B** is accuracy reported in the paper.
- **C** is the epoch when maximum accuracy observed

It should be noted that in experiments with depth is 49 layers, there is no accuracy reported in the paper. Therefore here I only show the results obtained from my experiments.

|     Depth     |        9        |        17       |        29       |        49       |
|:---------------:|:-----------------:|:-----------------:|:-----------------:|:-----------------:|
|    ag_news    | 87.67(90.17)/15 | 88.09(90.61)/20 | 88.01(91.33)/14 |    84.71/17     |
|   sogu_news   | 95.67(96.42)/16 | 95.89(96.49)/20 | 95.73(96.82)/19 |    95.35/20     |
|    db_pedia   | 98.33(98.44)/17 | 98.28(98.39)/18 | 98.07(98.59)/17 |    97.38/15     |
| yelp_polarity | 94.57(94.73)/12 | 95.20(94.95)/18 | 94.95(95.37)/20 |    95.08/19     |
|  yelp_review  | 62.44(61.96)/11 | 63.44(62.59)/19 | 62.70(63.00)/19 |    62.83/19     |
|  yahoo_answer | 69.57(71.76)/12 | 70.03(71.75)/15 | 70.34(72.84)/19 |    69.16/19     |
| amazon_review | 60.34(60.81)/12 | 60.98(61.19)/20 | 60.67(61.61)/20 |    59.80/17     |
|amazon_polarity| 94.30(94.31)/15 | 94.60(94.57)/17 | 94.53(95.06)/20 |    94.10/11     |

You could find detail log of each experiment containing loss, accuracy and confusion matrix at the end of each epoch in **output/datasetname_depth_number/logs.txt**, for example output/ag_news_depth_29/logs.txt
