# How to Use UR5e?

작성자 : 21700469 유진수

참조
- https://github.com/UniversalRobots/Universal_Robots_ROS_Driver <br>
- https://github.com/chaochao77/ROS_neuromeka_tutorial

<br>

## 0. ROS Setting - moveIt, Gazebo

Linux에서 ctrl+alt+t 로 터미널(cmd)창을 열고 아래의 코드를 순서대로 입력합니다. ROS key 설정하는 코드입니다. <br>

- ROS 홈페이지에 접속하여 ROS key관련 최신 리스트를 받아오는 코드입니다.

  `sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'`

  `sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654`

- 최신 정보 업데이트를 진행합니다.

  `sudo apt-get update`

1. **ROS 설치 코드**입니다. 터미널(cmd)에 아래와 같이 입력하시면 됩니다.

   본 튜토리얼에서는 ROS1 melodic 버전을 사용합니다.

   `sudo apt-get install ros-melodic-desktop-full`

   주의 : WIFI나 인터넷 연결 상태가 안좋거나, 랜선 자체에 Block 기능이 활성화 되어 있을 경우 완전히 다운로드 되지 않습니다.

2. ROS 초기화 명령어 입니다. **처음 설치시**에만 사용됩니다.

- ROS 의존성 파일 설치 코드입니다.

  `sudo apt-get install python-rosdep`

  `sudo rosdep init`

  `sudo rosdep update`

1. **로봇의 역기구학 계산 및 제어**를 위한 **Moveit 패키지 및 필요 파일 설치**입니다 .

   `sudo apt-get install ros-melodic-moveit`

   `sudo apt-get install ros-melodic-industrial-core`

   `sudo apt-get install ros-melodic-joint-state-publisher`

   `sudo apt-get install ros-melodic-trac-ik`

   `sudo apt-get install ros-melodic-moveit-visual-tools`

2. 시뮬레이션을 위한 **Gazebo 설치** 입니다.

   `sudo apt-get install ros-melodic-effort-controllers`

   `sudo apt-get install ros-melodic-joint-trajectory-controller`

3. **터미널(cmd)이 실행될 때**마다 bashrc에 **ROS관련 환경구성을 추가**해 ROS를 실행 할 때 마다 **자동적으로 환경구성을 로드**해 진행을 수월하게 합니다. 이 명령어는 한번만 해주면 됩니다.

   `echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc`

   `source ~/.bashrc`
   
<br>

## 1. 환경 설정

<br>

#### 1. 작업 공간 생성

아래 코드를 copy & paste 하여 작업 공간을 생성합니다.

```
# Go home directory
cd ~ 


# 작업 공간 생성
mkdir -p ur5e_ws/src
```

#### 2. src 파일 다운로드 & Catkin_make

본 Repository에 업로드 되어 있는, src 폴더 내 모든 파일을 다운로드합니다.
- [Click Here to Download](https://github.com/Yjinsu/MIP2022-UR5e_Pick_and_place_ROS_Simulation/tree/main/src)

파일 다운로드 이후, **1. 작업 공간 생성** 을 통해 만든 src 폴더 안에 넣어줍니다. <br>
깃허브 업로드 용량 제한 문제로 인해, src 내부의 **universal_robot** 파일을 zip 형태로 업로드하였습니다. <br>
따라서, 동일한 폴더 이름으로 압축을 해제하여 주시기 바랍니다.

![image](https://user-images.githubusercontent.com/84503980/206854708-a4a9af44-2ed1-4b40-87e6-df4187645608.png)

위와 같은 형태가 되도록 폴더를 만들어주시면 되겠습니다.

압축 해제가 끝나면, 아래의 코드를 copy & paste 합니다.

```
# Install dependencies
sudo apt update -qq
rosdep update
rosdep install --from-paths src --ignore-src -y

# Build the workspace
catkin_make
```

#### 2-2. Git Clone (Skip)

본 md파일의 작성자가 src.zip 파일을 만드는 과정에서 수행한 과정을 기록하였습니다. <br>
**2. src 파일 다운로드 & Catkin_make** 를 잘 수행했다면, 이미 해당 과정이 종료된 것이므로 이 부분은 생략해도 됩니다.

```
 # Clone the driver
git clone https://github.com/UniversalRobots/Universal_Robots_ROS_Driver.git src/Universal_Robots_ROS_Driver


# Clone the description. Currently, it is necessary to use the melodic-devel branch.
git clone -b melodic-devel https://github.com/ros-industrial/universal_robot.git src/universal_robot


# Install dependencies
sudo apt update -qq
rosdep update
rosdep install --from-paths src --ignore-src -y
```


#### 3. 경로 설정
```
# 터미널 실행 시 해당 경로에 접근, 쉘 활성화 (환경설정 초기 1회만 수행)
source devel/setup.bash 

# 현재 경로 확인이 필요하다면?
cat ~/.bashrc

#경로 수정이 필요하다면?
sudo gedit ~/.bashrc 
```

#### 4. 조인트를 조절할 수 있는 컨트롤러 다운로드 (Skip)

해당 부분 또한, 본 md파일의 작성자가 src.zip 파일을 만드는 과정에서 수행한 과정을 기록한 것입니다. <br>
zip 파일 안에 이미 포함된 부분이므로, 생략해도 좋습니다.

```
sudo apt install ros-melodic-rqt-joint-trajectory-controller 

sudo apt-get upgrade
```

#### 5. Moveit Setup Assistant (moveit 설정 바꾸는 소프트웨어)

ROS로 로봇을 제어하기 위한 패키지 **Moveit** 에 추가적인 설정을 하고 싶을 경우, 아래의 코드를 터미널에 입력합니다.

로봇에 그리퍼를 추가하거나, 로봇의 특정 자세를 정의하는 등 다양한 설정을 할 수 있습니다.

```
# Simulation
roslaunch ur5e_sim_moveit_config setup_assistant.launch 

# Real
roslaunch ur5e_real_moveit_config setup_assistant.launch 
```

<br>

## 2. 시뮬레이션

<br>

#### UR Gazebo 실행

가상 환경에서 로봇의 상태를 시각화하고 싶다면, 아래의 코드를 copy & paste 합니다.

```
roslaunch ur_gazebo ur5e_bringup.launch

roslaunch ur5e_sim_moveit_config move_group.launch

# 조인트 컨트롤러가 잘 동작되는지 시뮬레이션으로 확인하고 싶다면?
rosrun rqt_joint_trajectory_controller rqt_joint_trajectory_controller 
```


#### 코드 실행 & 시뮬레이션
```
rosrun ur_interface ur_move_demo
```

<br>

## 3. 실제 환경에서 로봇 구동

피치 펜던트를 비롯한 추가적인 외부 환경 설정이 필요합니다.

#### 1. UR5e 컨트롤 박스 & 노트북 간 이더넷 통신 (랜선 연결)

- 랜선을 연결해줍니다.

#### 2. URCaps External Control 설정

- 피치펜던트 상단의 "설치" 탭 클릭 -> URCaps -> External Control
- Host IP: 192.168.0.10
- Host name: 192.168.0.10

이때 설정하는 IP는 노트북의 IP주소와 동일해야 합니다.
![Screenshot from 2022-11-29 19-04-39](https://user-images.githubusercontent.com/84503980/204499773-c9cb2cb6-c6e2-4204-947b-4f774e254f5a.png)

IP가 다르다면, 아래와 같이 직접 바꾸어 주시면 됩니다.
![Screenshot from 2022-11-29 19-06-01](https://user-images.githubusercontent.com/84503980/204500051-3de2099c-51c5-4480-a4d1-3e4e028132dd.png)




#### 3. UR5e IP 설정

- 피치펜던트 상단의 최우측 탭 클릭 -> 시스템 -> 네트워크
- IP 주소 = 192.168.0.2
- 서브넷 마스크 = 255.255.255.0

#### 4. UR5e와 컴퓨터 간 IP 연결

아래의 코드를 터미널에 입력합니다.
```
roslaunch ur_robot_driver ur5e_bringup.launch robot_ip:=192.168.0.2

roslaunch ur5e_real_moveit_config move_group.launch
```

#### 5. 피치펜던트 로봇 프로그램 설정
- 피치펜던트 상단의 "프로그램" 탭 클릭 -> URCaps -> External Control
- 로봇 프로그램 내부에, **Control by 192.168.0.10** 만 떠 있다면, 잘 설정한 것입니다.
- 설정 이후에는, 피치펜던트 하단의 재생 버튼을 누르면, 외부 코드 입력으로 로봇을 제어할 준비가 모두 끝납니다.
- 주의사항 : 재생 버튼을 누르기 전에는 4번의 과정 (터미널에 코드 입력)이 반드시 선행되어야 합니다. 그렇지 않을 경우 에러가 발생합니다.


#### 6. 실제 로봇 구동

로봇 제어를 위한 예제 코드를 실행합니다.

```
rosrun ur_interface ur_move_demo
```


