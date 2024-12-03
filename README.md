# Trimaran - Georacing

# Laura Levraud - R&D Engineering internship 09/2023 - 01/2024 
> Optimization of real-time competitor tracking (bicycles and boats) via 2D video tracking, combined with GPS tracking.
> - Implementation of real-time 2D pattern tracking in a video based on a study and selection of publicly available open-source code.
> - Associate GPS positions to attempt to pre-position the pattern tracking area on a given competitor and improve 2D tracking quality.

### Results

<div align="justify"> 
After several studies and performance measurements of <b>pixel pointing</b> techniques, which track the trajectory of an object in a video by searching for characteristic pixels of that object, the conclusions are that it is too slow or imprecise for real-time tracking, i.e., with a constant video stream at more than 25 frames per second. Additionally, tracking by <b>pixel pointing</b> requires an operator's action to select the object - or the pixel area - in the first image to further track it in the video.  <br /> <br />

Since the primary objective is to enable real-time tracking and the secondary goal is to automate it, by requiring an operator only to control or choose whether or not to display the information for each competitor, <b>pixel pointing</b> was deemed inadequate.  <br /> <br />

The results obtained using <b>deep learning</b> methods were much more promising in terms of speed and accuracy. It was therefore decided to start developing a [tool](#Dev) that uses <b>deep learning</b> detection to simultaneously detect all objects in the image and an association algorithm to assign each object an identity and cross-reference the information between each frame to track objects throughout the video.  <br /> <br />

To use deep learning to identify competitors in an image, it was necessary to create a <b>machine learning model</b> that could learn from a database and then perform on new images during live broadcasts. Tests of learning from opensource databases had produced disappointing performance on live recordings, so it was decided to build our own database. Then I focused on the boats races for which I had the most images, with the best quality. After clipping and applying database enlargement algorithms on [Yolo](#YoloV8), I was able to obtain a database and build our model. <br /> <br />

Once the solution was built, I translated it into C# and incorporated it into the company's processes. The [GeoLiveDetect](https://github.com/lauralvd01/GeoLiveDectect.git) repo contains the software developed with a colleague to retrieve the video streams, apply the solution to them and retransmit the results to the web application used for the augmented application. <br /> <br />

<p align="center">
    <img src="RealTimeMOT\gif\deep_sort_Train14_bateau_2-ezgif.com-video-to-gif-converter.gif" width="49%"/> <img src="RealTimeMOT\gif\deep_sort_Train14_bateau_6-ezgif.com-video-to-gif-converter.gif" width="49%"/>
    <img src="RealTimeMOT\gif\deep_sort_Train14_vg_3-ezgif.com-video-to-gif-converter.gif" />
</p>

<hr/>

### YoloV8

For detection, the <b>YoloV8</b> model from [Ultralytics](https://docs.ultralytics.com/) is used. The online version was trained on the [COCO (Common Objects)](https://cocodataset.org/#home) dataset and is not suitable for the specific shapes of racing sailboats that need to be detected during [Vendée Globe](https://www.vendeeglobe.org/) or [52 Super Series](https://www.52superseries.com/) races. It might be suitable for detecting indoor racing bicycles during the [UCI TCL](https://uci.org/), but tracking these bicycles is significantly more challenging than tracking boats. Therefore, the current model training efforts have focused on sailboat detection. <br />
A database of approximately 13,500 images was created using [Roboflow](https://roboflow.com/) from [Vendée Globe](https://www.vendeeglobe.org/) videos and recorded [52 Super Series](https://www.52superseries.com/) live streams. These images were annotated to detect all the `sailboats` present. After exporting the database, a model based on `yolov8n.pt` was trained. <br />
This model is used through a C# code integrated into the implemented tool. <br /> <br />

<p align="center">
    <img src="detections_bateau_2-ezgif.com-video-to-gif-converter.gif" width="49%"/> <img src="detections_vg_1-ezgif.com-video-to-gif-converter.gif" width="49%"/>
</p>

### DeepSORT

For cross-referencing information between different video frames, a C# version of the [DeepSORT](https://github.com/nwojke/deep_sort) algorithm was adapted. It associates detections from one frame to the next using a Kalman filter and a measurement combining distance and appearance via another deep learning model specialized in identification. Once a boat has been detected in three consecutive frames (a configurable number), it is assigned an identity number, and the information related to that boat is stored until it disappears from the frame. <br /> <br />

<p align="center">
    <img src="deep_sort_Train14_bateau_2-ezgif.com-video-to-gif-converter (1).gif" width="49%"/> <img src="deep_sort_Train14_vg_1-ezgif.com-video-to-gif-converter.gif" width="49%"/>
</p>

The collected information enables locating all boats in the image throughout the video. Using this data, the implemented tool can display rectangles around detected boats in the video’s debug interface, each labeled with its identity number. This interface is linked to an online platform offering the same functionalities and will eventually be used to connect with augmented reality rendering systems. It also allows clicking on one (or more) detected boats to select which boats' information will be sent to the augmented reality system.

<p align="center">
    <img src="Capture2.PNG"/>
    <img src="Capture.PNG" width="49%"/> <img src="Capture1.PNG" width="49%"/>
</p>

<hr />

## Folder Structure

### Software for Sébastien
> Backup of the public repository [Github - Render Control Interface]([https://github.com/lauralvd01/Laura.git](https://github.com/lauralvd01/Render-Control-Interface)).
> Software developed for Trimaran's system engineer so that he can remotly (with SSH authentication) control and shut down all machines (called Renders).

<h3 id="DEMOS">DEMOS</h3>

> Selection of videos showing the results of different tested techniques.

<h3 id="Dev">Dev</h3>

> `README_R&D`, summary of all programs written during the internship and their locations or documentation. <br />
> PDF copies of each README from the Github repositories created during the internship:
> - [Github - GeoLiveDetect](https://github.com/lauralvd01/GeoLiveDectect.git)
> - [Github - RealTimeMOT](https://github.com/lauralvd01/RealTimeMOT.git)
> - [Github - Render Control Interface]([https://github.com/lauralvd01/Laura.git](https://github.com/lauralvd01/Render-Control-Interface))

### DOC
> Documentation on:
> - Roboflow and database creation
> - How to train a YoloV8 model
> - Installing and using the application for Sébastien
>
> and links to the READMEs in the [Dev](#Dev) folder.

### Research
> Collection of explored ideas, notes for each, and commented tests performed during the internship.

#### GEORACING - Pixel Tracking
> Summary table of tested techniques and evaluations.

#### README_Trimaran_Georacing
> A French version of this README, compiled with grip and saved as a PDF. To view the GIFs, see the corresponding folder in [DEMOS](#DEMOS).

</div>
