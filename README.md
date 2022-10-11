# Defense-Adversarial-in-ML-CAN-IDS
# Carla command

1. One terminal for pythonAPI

```bash
make PythonAPI
```

1. Another terminal for Carla server

```bash
make launch
```

1. It will pop up an CarlaUE4 Engine Windows, then press “Launch” and “Play”.
2. autopilot 不能用 while 收data client 會crash
3. 用 buffer 收一樣會經過幾次attack 後crash  
4. SteerRightmax {0000014A#F3A8000000F3A801] 最後一個bit應為0, 用來label 為attack msg 使用（training 記得改回來）

# **CAN IDS Implementation**

[CAN IDS Training Result Record](https://www.notion.so/CAN-IDS-Training-Result-Record-4cac85e87e6e410185e5d56d4574a272)

1. Real traffic not only ML f1-score
    1. method 1: save traffic log as an input file 
    2. method 2: directly dump the traffic

```python
model.predict(np.array(row.loc[$which row][element with feature].reshape(1,-1)
```

1. Carla ECU ID
    1. Throttle → 0x1F5 → 501
    2. Steering → 0x14A → 330
    3. Brake → 0x1E7→ 487
    4. Gear → 0x191 → 401
    5. Speed → 0x309 → 777
2. **要做Binary Classification or Multinomial Classification ?**
    1. Jehoshua → Binary 
    2. Multinomial → DoS, Spoofing, Replay
    3. 嘗試Spoofing 變成Binary dataset → GOOD
3. **Replay attack 如何Implement**
4. Using Models
    1. Decision Tree
    2. Logistic Regression
    3. XGB
    4. SVM
    5. KNN
    6. DNN
    7. CNN 
    8. Random Forest 
5. 有需要做cross validation?

# CAN IDS Dataset

1. Dataset contain 5 ECU but only inject Throttle,Steering,Brake regard as attack msg.
2. Gear, Speed regard as background noise
    
    ### # of Inject msgs
    
    1. Throttle → 9912(label:1)
    2. Steering → 2400(right,label:4) & 36999(left,label:2)
    3. Brake → 30000(label:3)

# CAN bus command

1. Create a virtual can
    
    ```python
    sudo modprobe vcan
    sudo ip link add dev vcan0 type vcan
    sudo ip link set up vcan0
    ```
    
2. can-utils 
    - Install can-utils on Linux
    
    ```bash
    $sudo apt-get install can-utils
    ```
    
    - Dump msg on vcan0
    
    ```bash
    $candump vcan0
    ```
    
    - Dump msg on vcan0 to log file
    
    ```bash
    $candump vcan0 -l
    ```
    
    - Dump msg on vcan0 and decode it
    
    ```bash
    $candump vcan0 | cantools decode "dbc file"
    ```
    
    - Replay the log file
    
    ```bash
    $canplayer -I "file.log"
    ```
    
    - Send CA Message
    
    ```bash
    $cansend can0 123#1122334455667788
    ```
    
    - See the Bus state
    
    ```bash
    $ip -details -statistics link show vcan0
    ```
    
3. cantools
