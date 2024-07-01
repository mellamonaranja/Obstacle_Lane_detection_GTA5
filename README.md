# Lane and Obstacle detection on GTA V

## 1. Find the Vanishing point
<img width="402" alt="image" src="https://github.com/mellamonaranja/GTA5-Self-Driving-Car/assets/55770526/d0374c5d-456a-4407-9f12-3e105526c01e">

## 2. Lane detection for active assistance during driving.

![Lane_detect2-ezgif com-video-to-gif-converter](https://github.com/mellamonaranja/GTA5-Self-Driving-Car/assets/55770526/d6035de5-fbc0-4d7c-8dd1-a157d44132bb)


## 3. Lane and obstacle detection for active assistance during driving.

![Lane_object_detect-ezgif com-video-to-gif-converter](https://github.com/mellamonaranja/GTA5-Self-Driving-Car/assets/55770526/7257ee97-7aaa-4630-97d2-434a8e59e0c6)

## requirements

- python 3.9.18
- conda

## development

1. Create conda environment

```
conda create -n "GTA" python=3.9.18 ipython
```

2. Download code
```
git clone https://github.com/mellamonaranja/Obstacle_Lane_detection_GTA5.git
```

3. Install packages

```
pip install -r requirements.txt
```

### Notes

- `requirements.txt` includes the required dependencies
- `requirements_f.txt` includes the freeze dependencies after installing
- If you receive the warning **dlerror: cudart64_110.dll not found** (https://github.com/tensorflow/tensorflow/issues/57103), execute the following command
```
conda install -c conda-forge cudatoolkit=11.0 cudnn=8.0
```
- Fix *object_detection* import issue
https://wikidocs.net/80978

```
wget https://raw.githubusercontent.com/protocolbuffers/protobuf/main/python/google/protobuf/internal/builder.py -O [YOUR_LOCAL_PATH]/google/protobuf/internal/builder.py
```

### USAGE EXAMPLE

```python
if __name__ == "__main__":


    bbox = (0, 40, 1280,720)
    fps = 30000
    yolo = YOLO()

    try:
        while True:
            screen = np.array(ImageGrab.grab(bbox=bbox))
            image = cv2.cvtColor(screen, cv2.COLOR_BGR2RGB)

            detected_boxes = yolo.make_predictions(image=image, plot=False)
            obstacles = [OBSTACLE(box, _id=i) for i, box in enumerate(detected_boxes)]

            # Process the frame with your lane detection
            ld = LANE_DETECTION(image, fps, 
                                yellow_lower=np.uint8([20, 50, 110]),
                                yellow_upper=np.uint8([35, 255, 255]),
                                white_lower=np.uint8([0, 140, 0]), 
                                white_upper=np.uint8([255, 255, 100]), 
                                lum_factor=110,
                                lane_start=[0.2, 0.5])
            processed_frame = ld.process_image(image, obstacles=obstacles)

            # Display the processed frame on the monitor
            cv2.imshow('Lane Detection', processed_frame)

            # Exit if 'q' is pressed
            if cv2.waitKey(1) & 0xFF == ord('q'):
                break

    except KeyboardInterrupt:
        print("Video processing stopped by user")
    finally:
        # Release resources and close all OpenCV windows
        cv2.destroyAllWindows()
        print("Resources released and video processing completed.")
```

### Ref:
https://towardsdatascience.com/copilot-driving-assistance-635e1a50f14
