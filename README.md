# Navigating the maze with Phi-3-powered "System 2" logic

### Project Overview

Large Language Models (LLMs) like OpenAI’s GPT-4o have democratised the use of AI. You don’t need to train your own Machine Learning models from scratch anymore, and instead can consume pre-trained Generative AI models to process / generate text, speech or images. This has lowered the barrier of AI adoption and made it easily available to businesses of all sizes.

Next stage in AI revolution is about Small Language Models (SLMs). Companies like Microsoft, Google and Meta released various SLMs (Phi-3, Gemma and LLaMa), which can provide performance comparable to LLMs, but with a much smaller footprint. You can run such models on your computer or even smartphone to meet security and privacy requirements, or power offline solutions which don’t have Internet access.

This is especially appealing in robotics and autonomous navigation use-case scenarios, where you can process all the sensor telemetry locally to increase performance and reduce dependency on remote services. This repo demonstrates the use of Microsoft’s Phi-3 SLM to power so-called “System 2” logic of Pioneer 3-DX mobile robot and navigate the maze.

As humans, we ourselves often operate in System 1 or System 2 modes:
- "System 1" is our auto-pilot, e.g. for routine tasks like driving a familiar route;
- "System 2" kicks in when something unexpected happens and it requires our conscious decision-making, e.g. braking to avoid hitting an obstacle.

In the above demo, “clear path” doesn’t require computational power and robot simply moves forward. However, when it detects an “obstacle”, sensor telemetry is fed to Phi-3 mini model locally to decide where to turn.

The maze itself was built in Coppelia Robotics’s simulation environment, while the compute power to run this demo was provided by AMD’s AI PC.
