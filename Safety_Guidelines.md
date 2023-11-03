## Safety in Lab

The combination of torque sensing in the robot's joints and impedance controllers allow for research into human robot collaboration and interaction in the lab. It also has the benefit of not requiring the robot to be in a cage or to require safety equipment such as a light fence to deal with when using the robots for other research. This combination of torque sensing and impedance control help with safety but do not guarantee it. 
Below, we outline some of the main safety guidelines and conditions for using the robots in the lab setting:

### Robot operating conditions
To use the robot in a collaborative way that allows safe human robot interaction, the robot **must** be run with the follow list of conditions. If you need to run the robot with conditions that vary from these, you will need to get permission from your daily supervisor.

- You run the default `cartesian_variable_impedance_controller` from the 'franka_human_friendly_robotics' library.
- Never use the robot alone, always have someone else with you while operating the robot. 
- The joint velocity limit should be set to: 
    - Start with a very low velocity/gains/forces/stiffnesses/range of movenments etc., if that works safely and reliably, ramp it up iteratively until the final desired values (or restart at low values if it becomes unsafe/unreliable and you fix something).
- stay away from moving robots as much as possible, i.e., only touch them if needed for experiments like Human Robot Interaction.
- Someone has their hand on the emergency stop button at all time that the robot is unlocked and live.
- Under no circumstances can you leave the robot operating in the lab without supervision. 
- No controllers with integral gains e.g. position or velocity based controllers from `franka_ros`. There is a very versatile impedance controller in this repo (or another) that is default for all projects. 
- No MOVEIT!!... or any RRT or random sampling algorithms to carry out trajectory planning. Due to the torus nature of the robots joint space, these algorithms can give very unintuitive paths for the robot joint to follow and cause it to whip around the long way. There are examples of point-to-point linear interpolation in this repo, these should be the default trajectory planner. MSc students, talk to your Daily supervisor if this is not sufficient for the scope of your project. 
- If possible, test in simulation first. There is instructions on how to use the `franka_human_friendly_controller` in the README.md. 
- No modifications of the robot (including mounting and unmounting sensors etc.) unless previously approved.
- Don't change the software on the computers. If this is neccessary, ask permission from your daily supervisor and document and undo all configuration changes

### General lab safety

- No loose cables going between the robots and areas other people can walk. If you need to run cables along the floor, ask the technicians for a cover to safely conceal the cables.
- No open drinks and food close to the robot and computers
