<link rel="stylesheet" href="path/to/css/retro.css">

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

## Line Follow Runs

![frequencvy](https://github.com/rayytsn9/ROBOTT/assets/79029536/62f541aa-aa8c-43f5-9ead-4b7a2e0d7c2a)


![drag](https://github.com/rayytsn9/ROBOTT/assets/79029536/03f11198-2099-43b7-9afa-62fd8a964548)


![first test](https://github.com/rayytsn9/ROBOTT/assets/79029536/affc948e-7007-4e54-9c02-0d4676050014)


![loop](https://github.com/rayytsn9/ROBOTT/assets/79029536/c35aae4e-ee51-477c-8de0-c97d28ab36d1)



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
