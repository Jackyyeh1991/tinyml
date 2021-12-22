# tinyml
TinyML training and deploying

## Subjects:
This project aims to use voice commands to active devices like "Hello Google" or "Hey Siri".

In the previous project, I successfully used the model that is built to detect "Yes", or "No".

In this project, I train a new model to detect "left" and "right" words. There are 3 sections below. The first is Training a model by using Colab. The second is deployed the generated C array from Colab on Arduino. Third, test the application. Last, final demo.

### Training a model on Colab:
1. Configure different words for wake world:

For example, Change the "Yes, No" to "left, right". We can also configure the training step and learning rate to allow the network to quickly coverage. Instead, I take 15000 and 3000 training steps.

2. Installing the Tensorflow pop package:

To include training operations and load TensorBoard to visualize the accuracy and loss as training proceeds.

3. Begin training and the result will show on the TensorBoard-scalar as below.

The "accuracy" in the TensorBoard shows how many steps it will take to detect a word correctly and the "Cross entropy graph" shows the difference between predictions and correct values.

The input data can also be so showed in IMAGES sections as below

4. My training result is:

Accuracy achieves 91.6%

5. Freeze the graph:

After the training is complete, there are weights, biases, and a graph of operations. Therefore, I need to create a static graph with trained weights and biases by using "Freeze the graph".

6. Use TensorFlow Lite Converter:

To convert models into a space-efficient format that can be used on memory constraint devices.

Then, Convert the TensorFlow lite model into C array.

### Deploy generated C array on the Arduino device
In Arduino IDE's libraries/Arduino_TensorFlowLite/examples/micro_speech

There are 3 files that need to be modified:

1. arduino_command_responder.cpp

Since we change the word from "Yes" to "left", Therefore, we change the found command[0] from "Y" to "l". "N" to "r"r.

if (found_command[0] == 'l') {                       // modify from 'Y' to 'l' for left 
      last_command_time = current_time;
      digitalWrite(LEDG, LOW);                       // Green for left
    }

    if (found_command[0] == 'r') {                   // modify from 'N' to 'r' for right 
      last_command_time = current_time;
      digitalWrite(LEDR, LOW);  // Red for right
    }
2. micro_features_model.cpp

Since we change the word from "Yes" to "left" and "No" to right. Therefore, we change the word to the correspondent word as follows.

const char* kCategoryLabels[kCategoryCount] = {
    "silence",
    "unknown",
    "left",                        // modify from 'Yes' to 'left'
    "right",                       // modify from 'No' to 'right'
};
3. micro_features_micro_model_settings.cpp：

Replace the value of const unsigned char g_model[] DATA_ALIGN_ATTRIBUTE and the value of const int g_model_len with generated C array from Colab model training.

const unsigned char g_model[] DATA_ALIGN_ATTRIBUTE = { 
0x20, 0x00, 0x00, 0x00, 0x54, 0x46, 0x4c, 0x33, 0x00, 0x00, 0x00, 0x00,   0x00, 0x00, 0x12, 0x00, 0x1c, 0x00, 0x04, 0x00, 0x08, 0x00, 0x0c, 0x00,
...
0x04, 0x00, 0x00, 0x00, 0x00, 0x00, 0x0a, 0x00, 0x0c, 0x00, 0x07, 0x00,
0x00, 0x00, 0x08, 0x00};
const int g_model_len = 18712;
4. upload micro_speech.ino to the Arduino hardware

### Test the application:
First, open the serial monitor on the Arduino IDE.

Say the word "left" toward the Arduino, the LED light on the Arduino should flash a green light and show the"left" word on the serial monitor

Say the word "left" toward the Arduino, the LED light on the Arduino should flash the red light and show the"right" word on the serial monitor



The final demo is as follow:

