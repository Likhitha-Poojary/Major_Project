# AI-POWERED MULTI-SENSORY FRUIT SORTING AND QUALITY GRADING SYSTEM

An embedded, vision-based banana classification and sorting prototype using **Raspberry Pi 4 Model B**, **Picamera2**, a custom-trained **YOLOv8 image-classification model**, and an **SG90 servo-operated diverter**.

## 1. Project Overview

The project aims to develop a compact prototype that captures banana images, classifies them into three predefined categories, and directs the item using a servo-controlled sorting mechanism.

A Raspberry Pi Camera captures the banana image. The custom-trained YOLOv8 classification model processes the image locally on the Raspberry Pi and predicts one of the following project-defined classes:

| Class | Meaning in this project |
|---|---|
| `SAFE` | Banana image assigned to the SAFE class by the trained model |
| `UNSAFE` | Banana image assigned to the UNSAFE class by the trained model |
| `ARTIFICIAL` | Banana image assigned to the ARTIFICIAL class by the trained model |

The Python application checks the model confidence and prediction stability before initiating the sorting action. An SG90 servo motor moves a diverter to the position associated with the predicted category. A Tkinter-based graphical user interface displays the camera feed and classification information.

> **Important scope clarification:** The implemented classifier is image-based. It does not directly detect or measure chemical residues, pesticide concentration, calcium carbide, or other chemical substances. The `ARTIFICIAL` output is a visual model classification and must not be interpreted as laboratory confirmation of artificial ripening or food safety. Chemical composition requires appropriate laboratory analysis.

## 2. Project Objectives

- Develop an image-based banana classification prototype using deep learning.
- Prepare and use a labelled banana image dataset for three project-defined classes.
- Train a custom YOLOv8 image-classification model.
- Deploy the trained model for local inference on a Raspberry Pi 4 Model B.
- Capture banana images using a Raspberry Pi Camera.
- Apply confidence-based and consecutive-frame checks before sorting.
- Operate an SG90 servo motor to direct the item according to the predicted class.
- Develop a graphical interface to display camera and classification information.
- Evaluate the classification and physical sorting operation through practical testing.

## 3. System Architecture and Working

The system follows the sequence below:

1. **Image acquisition:** The Raspberry Pi Camera captures the item placed in front of the camera.
2. **Image processing and classification:** The application obtains the camera frame and passes it to the custom-trained YOLOv8 model.
3. **Confidence verification:** The predicted class is checked against the configured confidence threshold of 60%.
4. **Stable prediction verification:** The application checks for a consistent prediction across three consecutive frames before proceeding.
5. **Category decision:** A valid and stable prediction is mapped to one of the three output categories.
6. **Servo actuation:** The SG90 servo moves the diverter to the angle assigned to the predicted category.
7. **Item clearance and reset:** After the sorting action and item-clearance check, the servo returns to its home position.
8. **GUI update:** The application displays the live camera feed, predicted category, confidence and status information.

Low-confidence or unsupported inputs should not trigger a category-specific sorting movement. The application uses status messages to indicate cases where a valid classification is not available.

## 4. Hardware Requirements

| Component | Function |
|---|---|
| Raspberry Pi 4 Model B | Main processing and control unit |
| Raspberry Pi Camera Module | Captures banana images |
| SG90 servo motor | Operates the sorting diverter |
| Diverter / sorting structure | Directs the item to the selected output path |
| Suitable power supply and connecting wires | Provides power and electrical connections |

Use the actual circuit diagram and hardware arrangement implemented in the prototype when documenting the connections.

### Servo Configuration

The current project configuration uses the following servo settings:

| Parameter | Configuration |
|---|---|
| Servo | SG90 |
| Raspberry Pi GPIO numbering | BCM |
| GPIO pin | GPIO 4 |
| PWM frequency | 50 Hz |
| SAFE position | 50° |
| UNSAFE position | 100° |
| ARTIFICIAL position | 140° |
| Home position | 90° |

The servo angles are the configured positions in the application. The physical diverter should be checked and calibrated to ensure that each position directs the item correctly.

## 5. Software and Technologies

| Technology | Purpose | Documentation |
|---|---|---|
| Python | Main application programming language | [Python](https://www.python.org/) |
| Ultralytics YOLOv8 | Banana image classification | [Ultralytics Documentation](https://docs.ultralytics.com/) |
| PyTorch | Deep-learning framework used by the model | [PyTorch](https://pytorch.org/) |
| OpenCV | Image handling and processing | [OpenCV](https://opencv.org/) |
| Picamera2 | Raspberry Pi Camera interface | [Picamera2](https://github.com/raspberrypi/picamera2) |
| Tkinter / TTK | Graphical user interface | [Tkinter Documentation](https://docs.python.org/3/library/tkinter.html) |
| Pillow | Image conversion and display in the GUI | [Pillow](https://python-pillow.org/) |
| RPi.GPIO | Raspberry Pi GPIO and servo control | [RPi.GPIO on PyPI](https://pypi.org/project/RPi.GPIO/) |
| Google Colab | Environment used for model training | [Google Colab](https://colab.research.google.com/) |
| Raspberry Pi OS | Operating system for the Raspberry Pi | [Raspberry Pi Software](https://www.raspberrypi.com/software/) |

Install versions compatible with the Raspberry Pi OS and Python environment used by the project. A `requirements.txt` should list the exact packages and versions tested with the final code.

## 6. Dataset

### Dataset Location

The banana image dataset is stored in Google Drive:

**[Open the Banana Dataset on Google Drive](https://drive.google.com/drive/folders/11dLwNRsRUxwzHsaguaowylcHSCBKpkJW?usp=sharing)**

The Drive folder is identified as `Indian_banana_dataset`.

### Dataset Description

The dataset is intended for the three-class banana image-classification task:

- `SAFE`
- `UNSAFE`
- `ARTIFICIAL`

The images are used to train and evaluate the custom classification model. The dataset should be organised into the class folders and training/validation/test splits expected by the training pipeline.

### Dataset Statistics

Enter the counts from the actual dataset folders and training configuration before publishing. Do not estimate these values.

| Dataset information | Value |
|---|---|
| Dataset folder | `Indian_banana_dataset` |
| Target object | Banana |
| Number of classes | 3 |
| Class names | SAFE, UNSAFE, ARTIFICIAL |
| Total number of images | 300 |
| SAFE image count | 100 |
| UNSAFE image count | 100 |
| ARTIFICIAL image count | 100 |
| Training split | 70% |
| Validation split | 10% |
| Test split | 20% |

If the dataset contains images from public sources as well as images collected by the project team, document those sources and any applicable licences in the dataset description.

## 7. Model Training and Deployment

A custom YOLOv8 image-classification model was trained using the prepared and labelled banana image dataset. Google Colab was used as the model-training environment.

The trained weights used by the application are:

```text
best_banana.pt
```

The model is loaded by the Python application and used for local inference on the Raspberry Pi. The model predicts a class and its associated confidence for the input image.

### Prediction Validation

The application uses the following configured checks:

- **Confidence threshold:** 60%.
- **Stable-frame requirement:** 3 consecutive frames.

A prediction below the configured confidence threshold is not accepted for category-specific sorting. The application also checks prediction stability to reduce sorting actions caused by momentary changes between frames.

The confidence threshold and stable-frame count are application settings. They do not, by themselves, establish the model's overall accuracy or reliability.

## 8. Sorting Mechanism

After a valid and stable category prediction is obtained, the application controls the SG90 servo motor.

| Predicted class | Configured servo angle |
|---|---:|
| SAFE | 50° |
| UNSAFE | 100° |
| ARTIFICIAL | 140° |
| Home / reset | 90° |

The servo-operated diverter changes position to guide the item toward the corresponding output path. After the item has cleared the sorting area, the mechanism returns to its home position for the next item.

The sorting mechanism is a servo-operated diverter. Do not describe the prototype as having a motorised conveyor belt unless one is actually installed and controlled by the project.

## 9. Graphical User Interface

The application uses Python Tkinter/TTK to provide a graphical interface for operating and observing the prototype.

The interface is intended to present:

- Camera feed.
- Predicted banana category.
- Prediction confidence.
- Current system status.
- Sorting operation controls provided by the implementation.

The GUI and camera operation are integrated with the classification and servo-control workflow.

## 10. Repository Structure

The following is a suggested GitHub repository layout. Include only files that exist in the final project.

```text
AI-Powered-Fruit-Sorting-and-Quality-Grading/
│
├── README.md
├── final.py
├── model.py
```

The dataset is hosted separately on Google Drive and linked in this README. Avoid committing a large dataset directory to GitHub. If the model weights are too large for ordinary GitHub file storage, provide a separate download link or use Git LFS where appropriate.

## 11. Installation and Execution

The commands below are a general run outline. Confirm the dependencies and paths against the final Raspberry Pi setup.

1. Install Raspberry Pi OS on the Raspberry Pi 4 Model B.
2. Set up the Python environment required by the project.
3. Install the compatible versions of the libraries listed in `requirements.txt`.
4. Connect the Raspberry Pi Camera and verify that Picamera2 can access it.
5. Connect the SG90 servo using the project's verified circuit. Ensure that the servo has a suitable power supply and a common ground with the Raspberry Pi.
6. Place `best_banana.pt` in the model path expected by the application.
7. Run the main application from the project directory:

```bash
python3 final.py
```

If the main application file has a different name, use that filename instead. Do not run the application until the GPIO wiring and servo power connections have been checked.

## 12. Testing and Evaluation

The prototype should be tested using representative banana samples and unsupported inputs.

Suggested test areas:

- Classification of samples belonging to each of the three trained classes.
- Camera availability and image acquisition.
- Behaviour when no item is visible.
- Behaviour when confidence is below 60%.
- Stable prediction across three consecutive frames.
- Prevention of category-specific servo movement for invalid or unsupported inputs.
- Servo movement to each configured category angle.
- Return of the diverter to the 90° home position.
- GUI display of the camera feed, category, confidence and status.

Record actual test observations and measured performance in the project report. Do not add unmeasured accuracy, precision, recall, processing speed or sorting-success percentages to this repository.

## 13. Limitations

- The current classification task is designed for banana images and the three classes represented in the training dataset.
- Classification performance can be affected by lighting, camera position, image quality, background and variation in banana appearance.
- A model prediction is not a chemical test and cannot establish the presence or concentration of chemical residues.
- The `SAFE`, `UNSAFE` and `ARTIFICIAL` labels are dataset/model categories; they are not a substitute for laboratory food-safety assessment.
- Further testing with more diverse images and physical samples is needed to evaluate generalisation and real-world performance.

## 14. Future Enhancements

Possible future improvements include:

- Expanding the dataset with more banana varieties, lighting conditions, backgrounds and stages of ripeness.
- Evaluating the model on an independent test dataset and reporting measured performance.
- Improving camera positioning and lighting consistency.
- Improving the mechanical diverter for more reliable item movement.
- Optimising inference time and application responsiveness on the Raspberry Pi.
- Investigating suitable additional sensing or laboratory methods if chemical assessment is required.

These are proposed enhancements and should not be presented as features already implemented.

## 15. Project Scope

This repository documents an academic prototype integrating image acquisition, deep-learning-based banana image classification, a Raspberry Pi edge-computing platform, a servo-operated sorting diverter and a graphical user interface.

The system demonstrates a possible approach to automated visual classification and physical sorting. It does not certify fruit as chemically safe or unsafe.

---

**Dataset:** [Indian_banana_dataset – Google Drive](https://drive.google.com/drive/folders/11dLwNRsRUxwzHsaguaowylcHSCBKpkJW?usp=sharing)
