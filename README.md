# What is this repository for?
Train DRL agents on ROS compatible simulations for autonomous navigation in highly dynamic environments. Flatland-DRL integration is based on Ronja Gueldenring's repo: drl_local_planner_ros_stable_baselines. Following features are included:
* Ros-melodic compatible version of drl_local_planner_ros stable_baselines (see [Franklins notes](#franklin-notes-for-old-repository) at the end for specific modification details)
* Setup to train a local planner with reinforcement learning approaches from [stable baselines](https://github.com/hill-a/stable-baselines) integrated ROS
* Training in a simulator fusion of [Flatland](https://github.com/avidbots/flatland) and [pedsim_ros](https://github.com/srl-freiburg/pedsim_ros)
* Local planner has been trained on static and dynamic obstacles: [video](https://www.youtube.com/watch?v=nHvpO0hVnAg)
* Combination with arena2d levels for highly randomized training and better generalization


## Installation
0. Standart ROS setup (Code has been tested with ROS-melodic on Ubuntu 18.04) with catkin_ws
1. Clone this repo into your catkin_ws 
````
mkdir -P catkin_ws/src
cd catkin_ws && catkin_make
cd src
git clone https://github.com/ignc-research/arena-flatland
````

2. Copy Install file and install forks
````
mv .rosinstall ../ 
rosws update
```` 

3. Install virtual environment and wrapper on your local pc (without conda activated) to be able to use python3 with ros
   ```
    pip3 install — upgrade pip
    pip3 install virtualenv
    pip3 install virtualenvwrapper
    which virtualenv # should output /usr/local/bin/virtualenv  
    ```

      
4. Create venv folder inside hom directory
```
cd $HOME
mkdir python_env # create a venv folder in your home directory 
```

Add this into your .bashrc/.zshrc :
```
export WORKON_HOME=/home/linh/python_env  #path to your venv folder
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3 #path to your python3 
export VIRTUALENVWRAPPER_VIRTUALENV=/usr/local/bin/virtualenv
source /usr/local/bin/virtualenvwrapper.sh
```
Create a new venv
```
mkvirtualenv --python=python3.6 arena-flatland-py3
workon arena-flatland-py3
```

Install packages inside your venv:
```
    virtualenv <path_to_venv>/venv_p3 --python=python3
    source <path_to_venv>/venv_p3/bin/activate
    <path_to_venv>/venv_p3/bin/pip install \
        pyyaml \
        rospkg \
        catkin_pkg \
        exception \
        numpy \
        tensorflow=="1.13.1" \
        gym \
        pyquaternion \ 
        mpi4py \
        matplotlib
   ```     
   
5. Install additional packages from drl_forks
```
cd <path_to_catkin_ws>/src/drl_local_planner_forks/stable_baselines/ 
workon arenapy3 
pip install -e .
```
4. Set system-relevant variables 
    * Modify all relevant pathes rl_bringup/config/path_config.ini
    
5. Activate venv and run python code ppo_train.py


# Example Usage

1. Train agent
    * Open first terminal (roscore): 
    ```
    roscore
    ```
    * Open second terminal (simulationI:
    ```
    roslaunch rl_bringup setup.launch ns:="sim1" rl_params:="rl_params_scan"
    ```
    * Open third terminal (DRL-agent):
     ```
    source <path_to_venv>/bin/activate 
    python rl_agent/scripts/train_scripts/train_ppo.py
    ```
    * Open fourth terminal (Visualization):
     ```
    roslaunch rl_bringup rviz.launch ns:="sim1"
    ```

2. Execute self-trained ppo-agent
    * Copy your trained agent in your "path_to_models"
    * Open first terminal: 
    ```
    roscore
    ```
    * Open second terminal: 
    ```
    roslaunch rl_bringup setup.launch ns:="sim1" rl_params:="rl_params_scan"
    ```
    * Open third terminal:
    ```
    source <path_to_venv>/venv_p3/bin/activate 
    roslaunch rl_agent run_ppo_agent.launch mode:="train"
    ```
    * Open fourth terminal: 
    ```
    roslaunch rl_bringup rviz.launch ns:="sim1"
    ```
    * Set 2D Navigation Goal in rviz

# Examples: Run pretrained Agents
Note: To be able to load the pretrained agents, you need to install numpy version 1.17.0.
```
<path_to_venv>/venv_p3/bin/pip install numpy==1.17
```

### Run agent trained on raw data, discrete action space, stack size 1
1. Copy the example_agents in your "path_to_models"
2. Open first terminal: 
    ```
    roscore
    ```
3. Open second terminal for visualization: 
    ```
    roslaunch rl_bringup rviz.launch ns:="sim1"
    ```
4. Open third terminal: 
    ```
    roslaunch rl_bringup setup.launch ns:="sim1" rl_params:="rl_params_scan"
    ```
5. Open fourth terminal:
    ```
    source <path_to_venv>/venv_p3/bin/activate 
    roslaunch rl_agent run_1_raw_disc.launch mode:="train"
    ```
### Run agent trained on raw data, discrete action space, stack size 3
1. Step 1 - 4 are the same like in the first example
2. Open fourth terminal:
    ```
    source <path_to_venv>/venv_p3/bin/activate 
    roslaunch rl_agent run_3_raw_disc.launch mode:="train"
    ```

### Run agent trained on raw data, continuous action space, stack size 1
1. Step 1 - 4 are the same like in the first example
2. Open fourth terminal:
    ```
    source <path_to_venv>/venv_p3/bin/activate 
    roslaunch rl_agent run_1_raw_cont.launch mode:="train"
    ```

### Run agent trained on image data, discrete action space, stack size 1
1. Step 1 - 3 are the same like in the first example
4. Open third terminal: 
    ```
    roslaunch rl_bringup setup.launch ns:="sim1" rl_params:="rl_params_img"
    ```
5. Open fourth terminal:
    ```
    source <path_to_venv>/venv_p3/bin/activate 
    roslaunch rl_agent run_1_img_disc.launch mode:="train"
    ```


    
### Franklin's notes for old repository:

### in folder: flatland
#### replace
CV_LOAD_IMAGE_GRAYSCALE
#### to
cv::IMREAD_GRAYSCALE



### in folder: pedsim
#### replace
shared_ptr
#### to
boost:: shared_ptr

#### replace
const function<void (boost:: shared_ptr
#### to
const boost:: function<void (boost:: shared_ptr

### install baseline
cd <path_to_catkin_ws>/src/drl_local_planner_forks/stable_baselines/
workon arenapy3
pip install -e .

# in folder: ws_drl_my/src/drl_local_planner_forks/flatland/flatland_plugins/include/flatland_plugins

"-std=c++11" is not enough , add #include<bits/stdc++.h>

    
    
