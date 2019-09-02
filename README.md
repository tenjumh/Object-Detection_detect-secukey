# Tensorflow object detection 기능을 통하여 RPA 구현 중 RPA가 하지 못하는 영역을 구현해 보았다.

RPA + AI Process는 다음과 같다.
1. RPA)특정 URL에 접근한다.
2. RPA)해당 URL은 로그인 한다.
3. 보안키 입력 창이 뜬다.
    (사실 로봇이 못하도록 하는 건데, 좀 허접한 싸이트인 것 같다.)
4. RPA)로봇은 보안키 화면을 캡쳐하여 특정 폴더에 저장한다.
5. Object Detection이 실행된다.
6. Object Detection)특정 폴더에 인식된 보안키 값을 TXT로 저장한다.
7. RPA)해당 TXT를 열어 키값을 복사한다.
8. RPA)보안창에 키값을 넣고 로그인 한다.
9. 뒤 Process는 생략.
   (간단히 설명하면 해당 web에서 데이터를 가지고 와서 excel에서 전처리작업 후 출력하는 업무인다.) --> 말은 간단해도 사람이 하면 엄청난 시간이 소요되는 작업이다.

하기 Tensorflow Object Detection이 돌아가기 위한 환경은 만들어 주자.

In the command terminal that pops up, create a new virtual environment called “tensorflow1” by issuing the following command:
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
(Note: The ‘pandas’ and ‘opencv-python’ packages are not needed by TensorFlow, but they are used in the Python scripts to generate TFRecords and to work with images, videos, and webcam feeds.)

#### 2e. Configure PYTHONPATH environment variable
A PYTHONPATH variable must be created that points to the \models, \models\research, and \models\research\slim directories. Do this by issuing the following commands (from any directory):
```
(tensorflow1) C:\> set PYTHONPATH=C:\tensorflow1\models;C:\tensorflow1\models\research;C:\tensorflow1\models\research\slim
```
(Note: Every time the "tensorflow1" virtual environment is exited, the PYTHONPATH variable is reset and needs to be set up again. You can use "echo %PYTHONPATH% to see if it has been set or not.)

#### 2f. Compile Protobufs and run setup.py
Next, compile the Protobuf files, which are used by TensorFlow to configure model and training parameters. Unfortunately, the short protoc compilation command posted on TensorFlow’s Object Detection API [installation page](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/installation.md) does not work on Windows. Every  .proto file in the \object_detection\protos directory must be called out individually by the command.

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

또한 사전에 하기 링크에서 마스터 모델을 download하고 이 gitub은 마스터 모델의 models > reaserch 하위 폴더에 저장하자. 만약 동일한 이름 폴더가 있으면 그 폴더를 이름을 좀 다르게 바꾸는 것이 좋다.

Download the full TensorFlow object detection repository located at https://github.com/tensorflow/models by clicking the “Clone or Download” button and downloading the zip file. Open the downloaded zip file and extract the “models-master” folder directly into the C:\tensorflow1 directory you just created. Rename “models-master” to just “models”.

\inference_graph폴더 안의 frozen_inference_graph.zip, model.ckpt.data-00000-of-00001.zip을 풀어준다.(gitub용량 제한때문에)
\inference_graph\saved_model폴더 안의 saved_model.zip을 풀어준다.(gitub용량 제한때문에)

C:\tensorflow1\models\research\object_detection\fp_image에 해당 보안키 이미지가 저장되고, Object Detection이 실행, 완료되면 result_password 폴더에 txt로 저장되어 진다.
```
(tensorflow1) C:\tensorflow1\models\research\object_detection>python Object_detection_image.py
```
결과값을 확인한다.

<img src="samples/result_image and txt_01.PNG">
<img src="samples/result_image and txt_02.PNG">
<img src="samples/result_image and txt_03.PNG">