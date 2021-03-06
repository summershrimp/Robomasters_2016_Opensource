#RoboMasters2016_Open_Source
This project is all the code I wrote as NUAA AIT team member for Robomasters 2016 Session. This project contains 3 sub project now.

1.  BaseSonarControl
2.  HeroCommandBoard
3.  M100ExtServo
4.  GimbalAutoControl

## BaseSonarControl
We have 4 sonar module around our base to avoid the wall in base area. And the control board is Nucleo-F401RE with espruino.

The sonars will trig every 20ms, and update all the values contains in global variables.

The main control function `DealGoing` will run every 5ms. The init state will lead base go to the left back corner, then the control board will decide to go along x-axis or y-axis, and go to the next corner.

The Communicate Protocal is DJI DBus Protocal. The class `DBus` will generate byte codes in DBus Protocal, and send these data towards USART1.

##  HeroCommandBoard
Our Hero has a up-down motor to extend the supply module and a gear motor to supply 17mm ammo, both motor are connected to a L298N motor controller board. L298N is connected to a Arduino Uno board.

The main control board will send the key press message towards USART3 on T-Board (supplied by DJI) to the command board. And the command board will open the corresponding motor. The key press message is plain ASCII text, and every key will have a non-blocking 10ms delay after release.

## M100ExtServo
Firstly we want to let the M100 drone carry 42mm golf ball to attack the opposite Base, but at last we decide to cut this decision.

The glof ball carry module is drived by a servo, but the N1 Flight Controller (Used By DJI M100) doesn't have any PWM output channel. So we add a external arduino board to read RC data and drive the servo. The arduino board is connected to the M100 UART port.

You need to open the API Control function in DJI Assistant and set RC data frequence to 50/100Hz, and MUST disabled any other data output.

The arduino board will use the gear switcher to open and close the servo

## GimbalAutoControl
GimbalAutoControl is a set of gimbal control codes for video recognization and controlling.

You can include `RCControl.cpp` to use these set of codes in main program.

Please init global variable `controller` and `RCThread` first
```
RCControl controller;
pthread_t RCThread;
```
And before your image loop, use the code above to start communicate through serial port.
```
controller.setPoint(320, 240);
pthread_create(&RCThread, NULL, controller.runner, &controller);
```
Once you find the target and finish tracking the target, use these codes to move the target to your central point.
```
controller.measure(trackBox.center.x, trackBox.center.y);
```
If you want to change the serial device name, please edit `RCControl.cpp` directly.

###  void RCControl::setPoint(int x, int y)

`setPoint` is a function to tell RCControl what is the central point in the video.

### void RCControl::measure(int x, int y)

`measure` is a function to tell RCControl to move your gimbal to the central point

### static void RCControl::runner(void *them)

`runner` is a loop function to send control data throuth serial port, you should use `pthread` in pthread.h to start it. `them` is a pointer to your RCControl instance.

### options.env
```
xp = 1.1
xi = 0.0
xd = 0.0
yp = 1.1
yi = 0.0
yd = 0.0

```
Must have a new line in the end of file.

You can change the PID values for PID Regulator in this file, or just change it in `RCControl.cpp`

