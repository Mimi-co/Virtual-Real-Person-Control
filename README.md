# Virtual-Real-Person-Control
We study the problem of image-to-video synthesis, whose goal is to learn a mapping function from an input source video to an output photorealistic video that precisely depicts the content of the source video.

### Example Generating video
The video in the center shows the Source driving video. the image on the left side is the source image that wants it to do a specific motion. the Video on the Right shows output generating fake video after Mapping motion from the source video to the source image.

<p align="center">
  <img src="images/1.gif" />
  <img src="images/2.gif" />
</p>


We support python3. To install the dependencies run:
``` pip install -r requirements.txt ```

### YAML configs
There are two configuration (config/dataset_name.yaml) files one for each dataset. See config/taichi-256.yaml to get description of each parameter.

### Pre-trained checkpoint
Checkpoints can be found under following link: google-drive or yandex-disk.

### Colab Demo
prepared a gui-demo for the google-colab see: demo_mimico.ipynb. To run press Open In Colab button.

The driving videos and source images should be cropped before it can be used in our method. To obtain some semi-automatic crop suggestions you can use python crop-video.py --inp some_youtube_video.mp4. It will generate commands for crops using ffmpeg. In order to use the script, face-alligment library is needed:
```  
git clone https://github.com/1adrianb/face-alignment
cd face-alignment
pip install -r requirements.txt
python setup.py install 
```

### Training
To train a model on specific dataset run:
``` 
CUDA_VISIBLE_DEVICES=0,1,2,3 python run.py --config config/dataset_name.yaml --device_ids 0,1,2,3 
```
The code will create a folder in the log directory (each run will create a time-stamped new directory). Checkpoints will be saved to this folder. To check the loss values during training see log.txt. You can also check training data reconstructions in the train-vis subfolder. By default the batch size is tunned to run on 2 or 4 Titan-X gpu (appart from speed it does not make much difference). You can change the batch size in the train_params in corresponding .yaml file.

#### To evaluate the reconstruction performance run:
```
CUDA_VISIBLE_DEVICES=0 python run.py --config config/dataset_name.yaml --mode reconstruction --checkpoint path/to/checkpoint
```
You will need to specify the path to the checkpoint, the reconstruction subfolder will be created in the checkpoint folder. The generated video will be stored to this folder, also generated videos will be stored in png subfolder in loss-less '.png' format for evaluation.

### Datasets
1. Taichi: Follow the instructions in [data/taichi-loading](path) or instructions from [https://github.com/AliaksandrSiarohin/video-preprocessing](Github).
2. VoxCeleb: follow the instruction from [https://github.com/AliaksandrSiarohin/video-preprocessing](Github).

### Training on your own dataset
1. Resize all the videos to the same size e.g 256x256, the videos can be in '.gif', '.mp4' or folder with images. We recommend the later, for each video make a separate folder with all the frames in '.png' format. This format is loss-less, and it has better i/o performance.

2. Create a folder ``` data/dataset_name``` with 2 subfolders ``` train and test```, put training videos in the train and testing in the test.

3. Create a config ``` config/dataset_name.yaml```, in dataset_params specify the root dir the ```root_dir: data/dataset_name```. Also adjust the number of epoch in train_params.
