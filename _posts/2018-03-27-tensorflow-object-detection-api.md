---
layout: post
title: Tensorflow Object Detection API - Finetuning with your own dataset
categories: Tensorflow Tutorial Python Object Detection
tags: Tensorflow Tutorial Python Object Detection
comments: true
---

Note: I'm using Ubuntu 16.04 and Tensorflow-GPU 1.6.0 installed via pip for this tutorial.

Tensorflow has its own Object Detection API with tutorials and a ModelZoo, you can find it [here](https://github.com/tensorflow/models/tree/master/research/object_detection). With so much documentation it can be difficult to actually get your model working on your own dataset, so I will try to summarize my experience using it.  The tutorial will by composed of the following parts:

* Installing the Object Detection API
* (Optional) Labelling your dataset
* Preparing the dataset
* Finetune the network with your dataset
* Export the finetuned graph
* Evaluation in your dataset

## Installing the Object Detection API

You can download the API with the command:

{% highlight ruby %}
[lib]
git clone ttps://github.com/tensorflow/models.git
{% endhighlight %}

You will also need to install the API, follow the steps presented [here](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/installation.md). If you pass the test provided in the *object_detection/builders/model_builder_test.py* script you can continue to the next step.

## Preparing the dataset

#### (Optional) Labelling your dataset

In my case, I had a dataset without any bounding box annotation, so I used the [labelImg](https://github.com/tzutalin/labelImg) tool to create easily bounding box annotation in VOC Pascal format. The tool is very easy to use and comfortable. Remember to save in Pascal VOC the labels (.xml format).

### Convert to TFRecord format

The Tensorflow Object Detection API requires the use of the TFRecord formatting of the data. The repository actually provides a script to transform your data format into TFRecord, but you have to extract by yourself the data (bounding box annotation, class of the bounding boxes...) inside the script.

You will also need a label_map file, which maps the name of a class with an id. An example of the content for a classification of dogs and cats could be:

{% highlight ruby %}
[lib]
item {
  id: 1
  name: 'cat'
  display_name: 'Cat'
}
item {
  id: 2
  name: 'dog'
  display_name: 'Dog'
}
{% endhighlight %}

You can add all the labels you require, but remember that the first id must be 1. If you put a 0 that class will be discarded.

### Directory structure

The recommended folder structure by the Tensorflow Object Detection API developers for training and validation is the following:

{% highlight ruby %}
[lib]
+data
  -label_map file
  -train TFRecord file
  -eval TFRecord file
+models
  + model
    -pipeline config file
    +train
    +eval
{% endhighlight %}

where '+' is a folder and '-' is a file. 

* label_map file: mapping from class name to id.
* train/test TFRecord file: training/test set in TFRecord format, you should obtain these with the script to convert your dataset to TFRecord format.
* pipeline config file: a pipeline.config file should already be inside the folder of the model you download from the ModelZoo. You just need to change some paths.
* train/val folders: empty folders to store checkpoints from training and evaluation.

## Finetune the network with your dataset

First pick the model you want to finetune from the [Object Detection Modelzoo](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/detection_model_zoo.md) and download it. I used the Faster-RCNN with a Resnet101 pretrained in COCO. In this tutorial we will not be using segmentation, so Mask-RCNN is out of the scope of the tutorial.

{% highlight ruby %}
[lib]
python object_detection/train.py \
    --pipeline_config_path=./models/model/pipeline.config \
    --train_dir=./models/model/train
{% endhighlight %}

## Export the finetuned graph

{% highlight ruby %}
[lib]
python export_inference_graph.py --input_type image_tensor --pipeline_config_path ./models/model/pipeline.config --trained_checkpoint_prefix .models/model/train/model.ckpt-xxxx --output_directory ./models/model/fine_tuned_model/
{% endhighlight %}

where 'model.ckpt-xxxx' is a file stored in the 'models/train' folder of our directory structure.

## Evaluation in your dataset

Loading the exported finetuned graph.

{% highlight python %}
[lib]
model_path = model_path = './models/model/fine_tuned_model/saved_model/'
with tf.Session(graph=tf.Graph()) as sess:
    tf.saved_model.loader.load(sess, [tf.saved_model.tag_constants.SERVING], model_path) 

    detection_graph = tf.get_default_graph()
    input_tensor = detection_graph.get_tensor_by_name('image_tensor:0')
    predict_class_op = detection_graph.get_tensor_by_name('detection_classes:0')
    num_detections_op = detection_graph.get_tensor_by_name('num_detections:0')
    detection_boxes_op = detection_graph.get_tensor_by_name('detection_boxes:0')
    detection_scores_op = detection_graph.get_tensor_by_name('detection_scores:0')
{% endhighlight %}
