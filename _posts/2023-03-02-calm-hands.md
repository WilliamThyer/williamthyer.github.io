---
title: "Calm Hands, help reduce nail-biting using computer vision and AI"
date: 2023-03-02
permalink: /posts/2023/3/calm-hands/
tags:
  - data science
  - ai
  - computer vision
  - python
---

<img src="https://williamthyer.github.io/images/calm-hands/app_demo.gif" alt="drawing" width="800"/>

Calm Hands helps the user reduce nail-biting during computer use. It provides realtime feedback about nail-biting habits using a deep neural net that monitors images from your webcam stream. This process is entirely local and images are never saved. Feedback is provided through audio and visual cues to alert you of when you are biting your nails. Realtime data visualization is provided as well. Check out the [full repo here](https://github.com/WilliamThyer/calm-hands).

Built with: Fastai, OpenCV, Tkinter, CustomTkinter, Matplotlib.

## How I Made This

### Step 1. Collect training and heldout test images

First I had to collect several hundred images of my biting my nails and not biting my nails. So I created `camera.py` and the `Cam` class. I call this in `collect_training_data.ipynb`. This allowed me to collect hundreds of photos in a variety of locations, lighting setups, and angles very easily.

```python
from camera import Cam

cam = Cam()
# Collect 2 frames per second for 60 seconds.
cam.write_frame_stream(length = 60, wait = .5, path = 'frames/biting')
```

![](https://williamthyer.github.io/images/calm-hands/biting.png)

### Step 2. Train the image classifier in Google Colab with fastai

I trained an `edgenext_small` model (imported from the `timm` library) using `fastai`. I used the proven method of finetuning a pretrained image classifier on this specific task.

```python
learn = vision_learner(dls, 'edgenext_small', metrics=error_rate)
learn.fine_tune(3,base_lr = .001)
```

![](https://williamthyer.github.io/images/calm-hands/model_training.png)

With ~1000 images and 3 cycles of training, I was at >90% accuracy. But I found there were specific positions and angles that the model was getting wrong.

![](https://williamthyer.github.io/images/calm-hands/mispreds.png)

### Step 3. Collecting more data based on model mistakes

I created the `realtime_model_preds.ipynb` to collect more data quickly, based on the predictions of the existing model. First I made a couple of functions to make predictions and print them out.

```python
def do_prediction(frame):
    with learn.no_bar(), learn.no_logging():
        return learn.predict(frame)

def print_pred(pred):

    conf = round(float(pred[2][pred[1]])*100,2)
    output = f'The model is {conf}% confident that you are {pred[0]}'
    print(output)
```

Then, I fed the functions a stream of frames from the webcam. This would print out the model predictions of each frame from my webcam.

```python
    # If I press 1, save the frame to the 'good' folder.
    # If I press 2, save it to the biting folder
    key_press_dict = {49:'train/good',50:'train/bad'}
    path = cam.get_path_from_keypress(key_press_dict)
    cam.write_frame(frame,labelled_folder_path=path)
```

I moved around until I found positions that the model was incorrectly predicting. Then, I pressed either '1' or '2' on the keyboard to save that frame to the correct folder and add it to the training set. After collecting several hundred more photos, I retrained the model. Now it was performing much better on the edge cases.

### Step 4. Creating the app

I used `tkinter` and `customtkinter` to create the GUI for the app. It displays the webcam feed on one side, and displays an `matplotlib` plot of the predictions of the model. It also provides instant auditory feedback if I'm biting my nails.

Creating the GUI was much more complicated than I thought. I've never really created a user interface before, and getting all of the positioning and callbacks working correctly took a while. Also, getting the plot to update smoothly and correctly took a lot of effort. But I think the final product looks and functions well!

<img src="https://williamthyer.github.io/images/calm-hands/app_demo.gif" alt="drawing" width="800"/>
