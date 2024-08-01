# Navigating the maze with Phi-3-powered "System 2" logic

### Project Overview:
This repo demonstrates the use of Microsoft’s **Phi-3** SLM to power so-called “_System 2_” logic of Pioneer 3-DX mobile robot and navigate the maze.

As humans, we ourselves often operate in System 1 or System 2 modes:
- "_System 1_" is our auto-pilot, e.g. for routine tasks like driving a familiar route;
- "_System 2_" kicks in when something unexpected happens and it requires our conscious decision-making, e.g. braking to avoid hitting an obstacle.

In the robot's case:
- "_System 1_" is used for a “clear path” situations, which don’t require computational power and thus the robot simply moves forward;
- "_System 2_" is activated when the robot detects an “obstacle”, to feed then its telemetry to Phi-3 model and decide where to turn.

>Note: The maze itself was built in **Coppelia Robotics**’s simulation environment, while the compute power to run this demo was provided by **AMD**’s AI PC.

### Simulation Environment:
_Pioneer 3-DX_'s virtual robotic solution was provided by Coppelia Robotics in _CoppeliaSim_. Its default configuration was enhanced with 4 additional sensors, used in this demo:
- 1x Video camera;
- 3x Proximity sensors.

_CoppeliaSim_'s default 3D scene was also re-designed to build a maze, with internal and external walls made detectable to simulate real-time proximity sensing.

### Solution Implementation:
1. Programmatic interactions with CoppeliaSim require installation of ZMQ Python package. We'll re-use its _RemoteAPIClient_ class:
``` Python
from coppeliasim_zmqremoteapi_client import RemoteAPIClient
```
2. To run Phi-3 locally on AMD AI PC, you need to use Phi-3 model in ONNX format. Pre-trained versions of Phi-3 models can be found on this [HuggingFace page](https://huggingface.co/microsoft).
3. This solution was tested on AMD AI PC - **_Minisforum Venus UM790 PRO_** with CPU and GPU, kindly provided by **Hackster** and **AMD** teams. If your AMD hardware comes with NPU, you can use Vitis AI Quantizer to convert the model into INT8 quantisation, required for inference on Ryzen AI.
4. Phi-3 model is initialised from the provided ONNX files. Prompt generation is enabled by the Helper function:
``` Python
def system2_logic(distance_left, distance_right):
    prompt = f"<|system|><YOUR_SPECIFIC_PROMPT><|end|><|assistant|>"
    input_tokens = tokenizer.encode(prompt)
    params.input_ids = input_tokens
    response = ""

    generator = og.Generator(model, params)

    while not generator.is_done():
        generator.compute_logits()
        generator.generate_next_token()

        new_token = generator.get_next_tokens()[0]
        response += tokenizer_stream.decode(new_token)

    del generator

    return response
```
5. To handle potential inconsistencies between the model's output and the expected format, a Helper function is provided to use RegEx to extract speed values for the robot's actuators (left and right wheels):
``` Python
def extract_first_list(data):
    # Join the list into a single string
    data_str = ''.join(data)
    
    # Use regex to find the first list pattern
    match = re.search(r'\[\s*-?\d+,\s*-?\d+\]', data_str)
    
    if match:
        # Extract the matched string
        list_str = match.group(0)
        
        # Convert the string to a list of integers
        list_values = eval(list_str)
        
        return list_values
    else:
        return None
```
6. Obstacle detection is driven by the handling of robot's front-centre proximity sensor:
``` Python
response, distance, _, _, _ = sim.handleProximitySensor(proximitySensorHandle)
```
7. If no obstacles detected, the robot is using its _System 1_ logic to maintain the same speed on its left and right wheels:
``` Python
sim.setJointTargetVelocity(leftMotorHandle, <REQUIRED_SPEED>)
sim.setJointTargetVelocity(rightMotorHandle, <REQUIRED_SPEED>)
```
8. If the front-centre proximity sensor will detect any objects, the robot will stop immediately, read distance values from its left and right proximity sensors and request instruction from Phi-3 model as per defined _System 2_ logic:
``` Python
_, distance_left, _, _, _ = sim.handleProximitySensor(proximitySensorHandleLeft)
_, distance_right, _, _, _ = sim.handleProximitySensor(proximitySensorHandleRight)
phi3_recommendations = system2_logic(distance_left, distance_right)
phi3_recommendations = extract_first_list(phi3_recommendations)
```
9. Relevant speed instructions then will be send to left and right wheels to perform turning manoeuvre:
```
sim.setJointTargetVelocity(leftMotorHandle, left_wheel_speed)
sim.setJointTargetVelocity(rightMotorHandle, right_wheel_speed)
```

### Demo Video:
You can find short demo of the working solution [here](https://youtu.be/bX2gU0sx1bE) on YouTube.
