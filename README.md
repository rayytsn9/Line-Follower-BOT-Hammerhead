# __ECE5 Robot__

## To Do

- [x] Make core parts
- [x] Prototyping
- [x] Solder
- [x] Photoresistor Check
- [x] Potentiometer Check
- [x] Motordriver Check
- [x] Calibration
- [x] SPID 

## Authors

- Brandon Colvin    (brcolvin@ucsd.edu)
- Yolanda Liu       (yul192@ucsd.edu)
- Sofia Arroyo      (soarroyo@ucsd.edu)
- Raynor Ananta     (rananta@ucsd.edu)

<image src="https://github.com/rayytsn9/Line-Follower-BOT-Hammerhead/assets/79029536/32318eb7-bc3f-4322-8c93-a01640e690f9" width="400"></image>

## Table of Contents

[Prototyping]
[Final Look]
[Track Runs]
[Basic Code]
[SPID Values]

## Prototyping

Figuring out placement     |  Final Idea 
:-------------------------:|:-------------------------:
![](https://github.com/rayytsn9/Line-Follower-BOT-Hammerhead/assets/79029536/f412a0d4-6074-4d7c-b5cd-49b374564946)  | ![](https://github.com/rayytsn9/Line-Follower-BOT-Hammerhead/assets/79029536/f72352ea-f2c9-4f78-acc9-36c4e74c6833)


## Final FORM of our Robot:Hammerhead

<image src="https://github.com/rayytsn9/ROBOTT/assets/79029536/a35f01ac-10e0-483e-9046-68278bccd44f" width="300"></image>

## Line Follow Runs

>Click on each below to play video

[![frequency](https://i9.ytimg.com/vi/RAdKmHk7j30/mqdefault.jpg?sqp=CISxmKQG-oaymwEoCMACELQB8quKqQMcGADwAQH4AYwCgALgA4oCDAgAEAEYYCBgKGAwDw==&rs=AOn4CLCBzPTzDY5CZj3DWiLwFq2Je9ktqg)](https://youtube.com/shorts/RAdKmHk7j30?feature=share)

The frequency track was the one where we struggled the most. We couldnt find a set PID values as we were constantly changing it throughout the track.

[![loop](https://i9.ytimg.com/vi/nh014s09uxA/mqdefault.jpg?sqp=CISxmKQG-oaymwEmCMACELQB8quKqQMa8AEB-AH-CYAC0AWKAgwIABABGGAgYChgMA8=&rs=AOn4CLD6gP7_2kr8I-Iawa7rJEax6lWvOw)](https://youtu.be/nh014s09uxA)

For the loop track, we up P to around 40, I to about half of the potentiometer , and D to be relatively small

[![drag](https://i9.ytimg.com/vi/cw63CbNxSWM/mqdefault.jpg?sqp=CISxmKQG-oaymwEmCMACELQB8quKqQMa8AEB-AHUBoAC4AOKAgwIABABGGUgZShlMA8%3D&rs=AOn4CLD5u37Sq7N9b8_OaJJ2ZBmH0CHEAQ&retry=4)](https://youtu.be/cw63CbNxSWM)

We kept the same PID value for the drag race but we up the speed up to about 250

## C0DE

> Photoresistor

    /* Photoresistor Code UCSD ECE 5 Lab 4 */
    /* Variables for Light Sensors*/

    // Initialize Photo Resistor Array
    int LDR_Pin[] = {A8, A9, A10, A11, A12, A13, A14}; 

    int totalPhotoResistors = sizeof(LDR_Pin) / sizeof(LDR_Pin[0]);  

    int LDR[99];

    void setup() {
      Serial.begin(9600); // For serial communication set up

      for (int i = 0; i < totalPhotoResistors; i++) 
        pinMode(LDR_Pin[i], INPUT);

    }

    void loop() {
      ReadPhotoResistors(); // Read photoresistors and map to 0-100 based on calibration

      Print(); // Print values to serial monitor
    }

    // ************************************************************************************************* //
    // function to read photo resistors
    void ReadPhotoResistors() {
      // looping through analog pins A8 to A14 and storing their values into our LDR array
      for (int i = 0; i < totalPhotoResistors; i++) {
        LDR[i] = analogRead(LDR_Pin[i]); /** FIX ME: which function to put here to read analog pins? **/
        delay(2);
      }
    }

    // ************************************************************************************************* //
    // function to print values of interest
    void Print() {
      for (int i = 0; i < totalPhotoResistors; i++) {
        // Printing the photo resistor reading values one by one
        Serial.print(LDR[i]);
        Serial.print(" ");
      }
      Serial.println(); // This just prints a new line, allowing the next set of readings to be on a new line

      delay(200); //just here to slow down the output for easier reading if desired
    }

> Potentiometer

    /* Potentiometer Code UCSD ECE 5 Lab 4*/
    /* Declare Variables for Potentiometer */
    const int S_pin = A0; // proportional - analog pin 0
    const int P_pin = A1; // proportional - analog pin 1
    const int I_pin = A2; // integral - analog pin 2
    const int D_pin = A3; // derivative - analog pin 3
    int Sp = 0;           // speed gain coeficient
    int kP = 0;           // proportional gain coeficient
    int kI = 0;           // integral gain coeficient
    int kD = 0;           // derivative gain coeficient

    void setup() { /* Setup - runs once (when power is supplied or after reset) */

      Serial.begin(9600); // For serial communication set up
    }

    void loop() { /* Loop - loops forever (until unpowered or reset) */

      ReadPotentiometers();

      Print(); // Call on user-defined function to print values from potentiometers
    }

    // ************************************************************************************************* //
    // function to read and map values from potentiometers

    void ReadPotentiometers() {
      // Call on user-defined function to read Potentiometer values
      Sp = ReadPotentiometerHelper(S_pin, 0, 1023, 0, 100);
      kP = ReadPotentiometerHelper(P_pin, 0, 1023, 0, 100);
      kI = ReadPotentiometerHelper(I_pin, 0, 1023, 0, 100);
      kD = ReadPotentiometerHelper(D_pin, 0, 1023, 0, 100); 

    }
    int ReadPotentiometerHelper(int pin, int min_resolution, int max_resolution, int min_potentiometer, int max_potentiometer) {
      return map(analogRead(pin), min_resolution, max_resolution, min_potentiometer, max_potentiometer);
    }

    // ************************************************************************************************* //
    // function to print values of interest
    void Print() {

      Serial.print(Sp);
      Serial.print(" ");
      Serial.print(kP);
      Serial.print(" ");
      Serial.print(kI);
      Serial.print(" ");
      Serial.println(kD);

      delay(200); //just here to slow down the output for easier reading if desired
    }

> Motordriver

    /* Motor Driver Code UCSD ECE 5 Lab 4*/

    // Include the Motor driver library
    #include <L298NX2.h>

    enum side {LEFT, RIGHT};

    // Initialize Motors

    // Taken from LEFT TO RIGHT of the robot ****** Orient yourself so that you are looking from the rear of the robot (photoresistors are farthest away from you, wheels are closest to you)
    // Create in stance of motors

    //                  Left Motors   Right motors 
    L298NX2 DriveMotors(  2, 3, 4,      7, 5, 6); // Digital Pins on Arduino
    //                 ENA, IN1, IN2, ENB, IN3, IN4

    //Set Initial Speed of Motors
    int speed = 255;

    // A function that commands a specified motor to move towards a given direction at a given speed
    void runMotorAtSpeed(side _side, int speed) {

      if (_side == LEFT) {
        DriveMotors.setSpeedA(abs(speed));
        if (speed > 0)                // swap direction if speed is negative
          DriveMotors.forwardA();           // sets the direction of the motor from arguments
        else
          DriveMotors.backwardA();          // sets the direction of the motor from arguments
      }
      if (_side == RIGHT) {
        DriveMotors.setSpeedB(abs(-speed));
        if (speed < 0)                // swap direction if speed is negative
          DriveMotors.forwardB();           // sets the direction of the motor from arguments
        else
          DriveMotors.backwardB();          // sets the direction of the motor from arguments
      }
    }

    // setup - runs once
    void setup() {
      delay(1500); // gives you a moment before tank actually moves 
    }

    // loop - loops forever
    void loop() {

      // Start Motors in forward direction
      runMotorAtSpeed(LEFT, speed);
      runMotorAtSpeed(RIGHT, speed);
      delay(3000); // let run forward for 3 seconds

      // Start Motors in backward direction
      //speed = -60;
      runMotorAtSpeed(LEFT, -speed);      /** FIX ME: set motor 1 at M1Sp in the BACKWARD direction (HINT: Very similar to forward direction) **/
      runMotorAtSpeed(RIGHT, -speed);     /** FIX ME: set motor 2 at M2Sp in the BACKWARD direction **/
      delay(3000); // let run backward for 3 seconds

      // Stop Motors

      runMotorAtSpeed(LEFT, 0);      /** FIX ME: stop motor 1 **/
      runMotorAtSpeed(RIGHT, 0);      /** FIX ME: stop motor 2 **/
      delay(3000); // stop for 3 seconds
    }

## SPID Values

| Element | Value |
| ----------- | ----------- |
| S | Drag: 250, Loop: Lower than drag, Frequency: Experiment |
| P | Drag: 40, Loop: 40, Frequency: Experiment|
| I | Drag: Half of potentiometer, Loop: Half of potentiometer, Frequency: Experiment|
| D | Drag: relatively small, Loop: relatively small, Frequency: Experiment |
