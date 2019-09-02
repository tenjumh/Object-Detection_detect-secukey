# Tensorflow object detection ����� ���Ͽ� RPA ���� �� RPA�� ���� ���ϴ� ������ ������ ���Ҵ�.

RPA + AI Process�� ������ ����.
1. RPA)Ư�� URL�� �����Ѵ�.
2. RPA)�ش� URL�� �α��� �Ѵ�.
3. ����Ű �Է� â�� ���.
    (��� �κ��� ���ϵ��� �ϴ� �ǵ�, �� ������ ����Ʈ�� �� ����.)
4. RPA)�κ��� ����Ű ȭ���� ĸ���Ͽ� Ư�� ������ �����Ѵ�.
5. Object Detection�� ����ȴ�.
6. Object Detection)Ư�� ������ �νĵ� ����Ű ���� TXT�� �����Ѵ�.
7. RPA)�ش� TXT�� ���� Ű���� �����Ѵ�.
8. RPA)����â�� Ű���� �ְ� �α��� �Ѵ�.
9. �� Process�� ����.
   (������ �����ϸ� �ش� web���� �����͸� ������ �ͼ� excel���� ��ó���۾� �� ����ϴ� �����δ�.) --> ���� �����ص� ����� �ϸ� ��û�� �ð��� �ҿ�Ǵ� �۾��̴�.

�ϱ� Tensorflow Object Detection�� ���ư��� ���� ȯ���� ����� ����.

In the command terminal that pops up, create a new virtual environment called ��tensorflow1�� by issuing the following command:
```
C:\> conda create -n tensorflow1 pip python=3.5
```
Then, activate the environment and update pip by issuing:
```
C:\> activate tensorflow1

(tensorflow1) C:\>python -m pip install --upgrade pip
```
Install tensorflow-gpu in this environment by issuing:
```
(tensorflow1) C:\> pip install --ignore-installed --upgrade tensorflow-gpu
```

(Note: You can also use the CPU-only version of TensorFow, but it will run much slower. If you want to use the CPU-only version, just use "tensorflow" instead of "tensorflow-gpu" in the previous command.)

Install the other necessary packages by issuing the following commands:
```
(tensorflow1) C:\> conda install -c anaconda protobuf
(tensorflow1) C:\> pip install pillow
(tensorflow1) C:\> pip install lxml
(tensorflow1) C:\> pip install Cython
(tensorflow1) C:\> pip install contextlib2
(tensorflow1) C:\> pip install jupyter
(tensorflow1) C:\> pip install matplotlib
(tensorflow1) C:\> pip install pandas
(tensorflow1) C:\> pip install opencv-python
```
(Note: The ��pandas�� and ��opencv-python�� packages are not needed by TensorFlow, but they are used in the Python scripts to generate TFRecords and to work with images, videos, and webcam feeds.)

#### 2e. Configure PYTHONPATH environment variable
A PYTHONPATH variable must be created that points to the \models, \models\research, and \models\research\slim directories. Do this by issuing the following commands (from any directory):
```
(tensorflow1) C:\> set PYTHONPATH=C:\tensorflow1\models;C:\tensorflow1\models\research;C:\tensorflow1\models\research\slim
```
(Note: Every time the "tensorflow1" virtual environment is exited, the PYTHONPATH variable is reset and needs to be set up again. You can use "echo %PYTHONPATH% to see if it has been set or not.)

#### 2f. Compile Protobufs and run setup.py
Next, compile the Protobuf files, which are used by TensorFlow to configure model and training parameters. Unfortunately, the short protoc compilation command posted on TensorFlow��s Object Detection API [installation page](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/installation.md) does not work on Windows. Every  .proto file in the \object_detection\protos directory must be called out individually by the command.

In the Anaconda Command Prompt, change directories to the \models\research directory:
```
(tensorflow1) C:\> cd C:\tensorflow1\models\research
```

Then copy and paste the following command into the command line and press Enter:
```
protoc --python_out=. .\object_detection\protos\anchor_generator.proto .\object_detection\protos\argmax_matcher.proto .\object_detection\protos\bipartite_matcher.proto .\object_detection\protos\box_coder.proto .\object_detection\protos\box_predictor.proto .\object_detection\protos\eval.proto .\object_detection\protos\faster_rcnn.proto .\object_detection\protos\faster_rcnn_box_coder.proto .\object_detection\protos\grid_anchor_generator.proto .\object_detection\protos\hyperparams.proto .\object_detection\protos\image_resizer.proto .\object_detection\protos\input_reader.proto .\object_detection\protos\losses.proto .\object_detection\protos\matcher.proto .\object_detection\protos\mean_stddev_box_coder.proto .\object_detection\protos\model.proto .\object_detection\protos\optimizer.proto .\object_detection\protos\pipeline.proto .\object_detection\protos\post_processing.proto .\object_detection\protos\preprocessor.proto .\object_detection\protos\region_similarity_calculator.proto .\object_detection\protos\square_box_coder.proto .\object_detection\protos\ssd.proto .\object_detection\protos\ssd_anchor_generator.proto .\object_detection\protos\string_int_label_map.proto .\object_detection\protos\train.proto .\object_detection\protos\keypoint_box_coder.proto .\object_detection\protos\multiscale_anchor_generator.proto .\object_detection\protos\graph_rewriter.proto .\object_detection\protos\calibration.proto .\object_detection\protos\flexible_grid_anchor_generator.proto
```
This creates a name_pb2.py file from every name.proto file in the \object_detection\protos folder.

**(Note: TensorFlow occassionally adds new .proto files to the \protos folder. If you get an error saying ImportError: cannot import name 'something_something_pb2' , you may need to update the protoc command to include the new .proto files.)**

Finally, run the following commands from the C:\tensorflow1\models\research directory:
```
(tensorflow1) C:\tensorflow1\models\research> python setup.py build
(tensorflow1) C:\tensorflow1\models\research> python setup.py install
```

���� ������ �ϱ� ��ũ���� ������ ���� download�ϰ� �� gitub�� ������ ���� models > reaserch ���� ������ ��������. ���� ������ �̸� ������ ������ �� ������ �̸��� �� �ٸ��� �ٲٴ� ���� ����.

Download the full TensorFlow object detection repository located at https://github.com/tensorflow/models by clicking the ��Clone or Download�� button and downloading the zip file. Open the downloaded zip file and extract the ��models-master�� folder directly into the C:\tensorflow1 directory you just created. Rename ��models-master�� to just ��models��.

\inference_graph���� ���� frozen_inference_graph.zip, model.ckpt.data-00000-of-00001.zip�� Ǯ���ش�.(gitub�뷮 ���Ѷ�����)
\inference_graph\saved_model���� ���� saved_model.zip�� Ǯ���ش�.(gitub�뷮 ���Ѷ�����)

C:\tensorflow1\models\research\object_detection\fp_image�� �ش� ����Ű �̹����� ����ǰ�, Object Detection�� ����, �Ϸ�Ǹ� result_password ������ txt�� ����Ǿ� ����.
```
(tensorflow1) C:\tensorflow1\models\research\object_detection>python Object_detection_image.py
```
������� Ȯ���Ѵ�.

<img src="samples/result_image and txt_01.PNG">
<img src="samples/result_image and txt_02.PNG">
<img src="samples/result_image and txt_03.PNG">