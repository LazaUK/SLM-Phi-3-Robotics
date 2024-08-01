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
_Pioneer 3-DX_'s virtual robotic solution was provided by Coppelia Robotics in _CoppeliaSim_. Its default configuration was equipped with 4 additional sensors, used in this demo:
- 1x Video camera;
- 3x Proximity sensors.

_CoppeliaSim_'s default 3D scene was also re-designed to build a maze, with internal and external walls made detectable to simulate real-time prximity sensing.

### Solution Implementation:

### Demo Video:
You can find short demo of the working solution [here](https://youtu.be/bX2gU0sx1bE) on YouTube.
