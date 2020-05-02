---
title: Final project
tags: Final-Project
---

# Enter the discount matrix: reading brains on a budget


## The EEG headset component

To read brain signals, I purchased this Star Wars Force Trainer toy off eBay for $20.

![Image](https://i.imgur.com/3BYftl7.jpg){:.border.rounded.shadow.image--xl}

It contains a Neurosky chip used by far more expensive headsets that allows for accurate readings.

![Image](https://i.imgur.com/IH121vd.jpg){:.border.rounded.shadow.image--xl}

![Image](https://i.imgur.com/hQPFXeI.jpg){:.border.rounded.shadow.image--xl}

To read the data from the headset, I'm going to solder a wire to the 'T' pin on the chip, and another to the negative terminal of its battery. Unfortunately, the current coronavirus situation has left me at home without access to my college lab and equipment, so forgive the rough soldering job. I only had a $10 Amazon soldering iron to work with. To maintain my commitment to safety, I built this soldering fan with a carbon filter.

![Image](https://i.imgur.com/bkTqsnv.jpg){:.border.rounded.shadow.image--xl}

And here's the soldering job:

![Image](https://i.imgur.com/VcEwhXm.jpg){:.border.rounded.shadow.image--xl}

The hot glue serves to support the extremely cheap solder I used and prevents the wire from being yanked out.


I popped out the LED on the headset case so I could neatly pull the wires through its hole.

![Image](https://i.imgur.com/IjmziDT.jpg){:.border.rounded.shadow.image--xl}


![Image](https://i.imgur.com/XXG64JC.jpg){:.border.rounded.shadow.image--xl}

![Image](https://i.imgur.com/zdBk7VR.jpg){:.border.rounded.shadow.image--xl}

Now its time to test the data transmission.


### First test of data transmission

To see if the connection was successful, I wrote up a simple program to read the data from the serial RX pin.
```c
void setup() {
  Serial.begin(9600);
  Serial.println("serial delimit test 1.0"); // so I can keep track of what is loaded
}

void loop() {

  if (Serial.available())  {
    char c = Serial.read();  //gets one byte from serial buffer
    Serial.println(c);
  }
}
```

Success!

<video width="640" height="480" controls>
  <source src="https://i.imgur.com/V2gOB7n.mp4" type="video/mp4">
Your browser does not support the video tag.
</video>

Note that the output on the serial monitor is gibberish because the Arduino code tells it to expect 'char', or character data. Therefore, it's trying to map the brain wave readings to actual letters. Silly computer.

### Getting some real readings

To turn the gibberish into real readings, I modified the code from the very helpful [Brain Library](https://github.com/kitschpatrol/Brain) and [Neurosky Hacking Library](https://github.com/JimRoskind/NeuroskyHacking) to work with the particular headset I had. 

I built this circuit to give me control of which reading I want to graph. The Neurosky chip records many different kind of brain waves like Theta, Alpha, Beta, Gamma, and also some interesting measurements like Meditation and Attention. Each of the 10 buttons toggles one of these readings. If you want to read more about how this works, you can refer to the Neurosky docs [here](http://developer.neurosky.com/docs/doku.php?id=thinkgear_connector_tgc).

![Image](https://i.imgur.com/3wfuwkF.jpg){:.border.rounded.shadow.image--xl}

![Image](https://i.imgur.com/sdhMAa2.jpg){:.border.rounded.shadow.image--xl}

![Image](https://i.imgur.com/YaZTC2K.jpg){:.border.rounded.shadow.image--xl}

I put the second circuit onto a protoboard for smaller form factor. I neatly twist tied everything.

![Image](https://i.imgur.com/Cp2PHnZ.jpg){:.border.rounded.shadow.image--xl}

Resulting in:


![Image](https://i.imgur.com/kZghgbG.jpg){:.border.rounded.shadow.image--xl}

Once this was hooked up the Arduino, the readings worked beautifully.

<video width="640" height="480" controls>
  <source src="https://i.imgur.com/3PlUUYP.mp4" type="video/mp4">
Your browser does not support the video tag.
</video>

## The controller

For the controller portion, I purchased a cheap controller breakout board that had a joystick and four buttons wired up to convenient port sockets. I would've loved to make it completely from scratch, but unforunately, the current circumstances limit that creative freedom. As you can see, the right button was broken on arrival.


![Image](https://i.imgur.com/nFcnxpo.jpg){:.border.rounded.shadow.image--xl}

I came up with a quick fix for it using some leftover plastic bumps from some random package. 

![Image](https://i.imgur.com/HRMqKrm.jpg){:.border.rounded.shadow.image--xl}

![Image](https://i.imgur.com/beOMYol.jpg){:.border.rounded.shadow.image--xl}

### Testing the controller

I hope to make the controller work wirelessly with the computer. Whether this is better done over bluetooth or radio will be decided later. For today, I'm going to set up the controller to work over Bluetooth Low Energy. Since I do not have a bluetooth module, I'll use a AdaFruit Feather Huzzah ESP32 as the bluetooth transiever to communicate with my computer.

I first used this code to make sure the button presses were being read by the Huzzah.

```c
#define BUTTON_UP 2

#define BUTTON_RIGHT 3

#define BUTTON_DOWN 4

#define BUTTON_LEFT 5

#define BUTTON_E 6

#define BUTTON_F 7



#define DELAY 500



void setup() {

 Serial.begin(9600);



 // to enable pull up resistors first write pin mode

 // and then make that pin HIGH

 pinMode(BUTTON_UP, INPUT);

 digitalWrite(BUTTON_UP, HIGH);



 pinMode(BUTTON_RIGHT, INPUT);

 digitalWrite(BUTTON_RIGHT, HIGH);



 pinMode(BUTTON_DOWN, INPUT);

 digitalWrite(BUTTON_DOWN, HIGH);



 pinMode(BUTTON_LEFT, INPUT);

 digitalWrite(BUTTON_LEFT, HIGH);



 pinMode(BUTTON_E, INPUT);

 digitalWrite(BUTTON_E, HIGH);



 pinMode(BUTTON_F, INPUT);

 digitalWrite(BUTTON_F, HIGH);

}



void loop() {

 if(digitalRead(BUTTON_UP) == LOW) {

   Serial.println("Button A is pressed");

   delay(DELAY);

 }

 else if(digitalRead(BUTTON_RIGHT) == LOW) {

   Serial.println("Button B is pressed");

   delay(DELAY);

 }

 else if(digitalRead(BUTTON_DOWN) == LOW) {

   Serial.println("Button C is pressed");

   delay(DELAY);

 }

 else if(digitalRead(BUTTON_LEFT) == LOW) {

   Serial.println("Button D is pressed");

   delay(DELAY);

 }

 else if(digitalRead(BUTTON_E) == LOW) {

   Serial.println("Button E is pressed");

   delay(DELAY);

 }

 else if(digitalRead(BUTTON_F) == LOW) {

   Serial.println("Button F is pressed");

   delay(DELAY);

 }

}
```

Then I used this code to make sure the joystick movements were being read by the Huzzah:

```c
// define global variables for analog pins.
// X values will be read from pin 0 and Y from pin 1

#define PIN_ANALOG_X 0

#define PIN_ANALOG_Y 1

 

void setup() {

 // Start serial because we will observe values at serial monitor

 Serial.begin(9600);

}



void loop() {

 // Print x axis values

 Serial.print("x: ");

 Serial.println(analogRead(PIN_ANALOG_X));

 // Print y axis values

 Serial.print("y: ");

 Serial.println(analogRead(PIN_ANALOG_Y));

 

 // Some delay to clearly observe  values on the serial monitor.

 delay(200);

}
```

Once these produced an output, it was time to make the controller wireless using Bluetooth LE.

I used a [gamepad library](https://github.com/lemmingDev/ESP32-BLE-Gamepad) here to setup the Huzzah as a broadcaster. 

Here's the code I wrote up for the Huzzah. I will optimize it when I get the chance:

```c
/*
 * A simple sketch that maps a single pin on the ESP32 to a single button on the controller
 */

#include <BleGamepad.h> 

BleGamepad bleGamepad("Sexy Controller", "Attari INC", 100);

int previousButton1State = HIGH;
int previousButton2State = HIGH;
int previousButton3State = HIGH;
int previousButton4State = HIGH;

int topButton = 12;
int leftButton = 33;
int lowButton = 15;
int rightButton = 32;


void setup() {
  pinMode(topButton, INPUT_PULLUP); // Top button
  pinMode(leftButton, INPUT_PULLUP); // Left button
  pinMode(lowButton, INPUT_PULLUP); // Low button
  pinMode(rightButton, INPUT_PULLUP); // Right button
  bleGamepad.begin();
}

void loop() {
  if(bleGamepad.isConnected()) {

    int currentButton1State = digitalRead(topButton);
    int currentButton2State = digitalRead(leftButton);
    int currentButton3State = digitalRead(lowButton);
    int currentButton4State = digitalRead(rightButton);

    if (currentButton1State != previousButton1State)
    {
      if(currentButton1State == LOW)
      {
        bleGamepad.press(BUTTON_1);
      }
      else
      {
        bleGamepad.release(BUTTON_1);
      }
    }
    previousButton1State = currentButton1State;
    

    if (currentButton2State != previousButton2State)
    {
      if(currentButton2State == LOW)
      {
        bleGamepad.press(BUTTON_2);
      }
      else
      {
        bleGamepad.release(BUTTON_2);
      }
    }
    previousButton2State = currentButton2State;
    

    if (currentButton3State != previousButton3State)
    {
      if(currentButton3State == LOW)
      {
        bleGamepad.press(BUTTON_3);
      }
      else
      {
        bleGamepad.release(BUTTON_3);
      }
    }
    previousButton3State = currentButton3State;


    if (currentButton4State != previousButton4State)
    {
      if(currentButton4State == LOW)
      {
        bleGamepad.press(BUTTON_4);
      }
      else
      {
        bleGamepad.release(BUTTON_4);
      }
    }
    previousButton4State = currentButton4State;
  }
}

```

Here's the intitial rough circuit I'm using to test this first run of the Bluetooth controller. It's a bulky mess just because I've snapped the controller onto the Uno as the power source just to test everything.

![Image](https://i.imgur.com/d2q6FPc.jpg){:.border.rounded.shadow.image--xl}


And it works!


<video width="640" height="480" controls>
  <source src="https://i.imgur.com/VLUWOQD.mp4" type="video/mp4">
Your browser does not support the video tag.
</video>

To reduce the bulk, I wanna replace the portable battery with a smaller battery. In a quest for the ideal battery, I took apart an old PS3 controller.

![Image](https://i.imgur.com/gRppvKn.jpg){:.border.rounded.shadow.image--xl}

The battery here looked to be the perfect size at 3.7V.

![Image](https://i.imgur.com/Cm5KSsz.jpg){:.border.rounded.shadow.image--xl}

The Huzzah can run off a LiPo of this size (and charge it too!). Here's the 'slim' version of the bluetooth controller!

![Image](https://i.imgur.com/5mXxcO7.jpg){:.border.rounded.shadow.image--xl}

Since the Huzzah was charging the battery, the original power bank is still there but once the LiPo is charged, it won't be needed anymore. 

I can slim this down even further by getting rid of the breadboard and connecting the Huzzah directly to the joystick.

### Adding the IMU

This processing sketch I wroteup uses the ToxicLib library to rotate a 3D object (a beautiful airplane model I found [here](https://github.com/jrowberg/i2cdevlib)) in terms of quaternions. 

```java
import processing.serial.*;
import processing.opengl.*;
import toxi.geom.*;
import toxi.processing.*;


ToxiclibsSupport gfx;

Serial port;                         // The serial port

String val;

float[] q = new float[4];
Quaternion quat = new Quaternion(1, 0, 0, 0);
float q0; 
float q1;
float q2; 
float q3;

//float[] gravity = new float[3];
//float[] euler = new float[3];
//float[] ypr = new float[3];

void setup() {
    // 600px square viewport using OpenGL rendering
    size(600, 600, OPENGL);
    gfx = new ToxiclibsSupport(this);

    // setup lights and antialiasing
    lights();
    smooth();
  
    // display serial port list
    println(Serial.list());

    // get a specific serial port 
    String portName = "COM7";
    
    // open the serial port
    port = new Serial(this, portName, 115200);
    
    port.bufferUntil('\n');
    
    port.write('r');
}

void draw() {
    
    // black background
    background(255);
    
    // translate everything to the middle of the viewport
    pushMatrix();
    translate(width / 2, height / 2);


    float[] axis = quat.toAxisAngle();
    rotate(axis[0], -axis[1], axis[3], axis[2]);

    // draw main body in red
    fill(255, 0, 0, 200);
    box(10, 10, 200);
    
    // draw front-facing tip in blue
    fill(0, 0, 255, 200);
    pushMatrix();
    translate(0, 0, -120);
    rotateX(PI/2);
    drawCylinder(0, 20, 20, 8);
    popMatrix();
    
    // draw wings and tail fin in green
    fill(0, 255, 0, 200);
    beginShape(TRIANGLES);
    vertex(-100,  2, 30); vertex(0,  2, -80); vertex(100,  2, 30);  // wing top layer
    vertex(-100, -2, 30); vertex(0, -2, -80); vertex(100, -2, 30);  // wing bottom layer
    vertex(-2, 0, 98); vertex(-2, -30, 98); vertex(-2, 0, 70);  // tail left layer
    vertex( 2, 0, 98); vertex( 2, -30, 98); vertex( 2, 0, 70);  // tail right layer
    endShape();
    beginShape(QUADS);
    vertex(-100, 2, 30); vertex(-100, -2, 30); vertex(  0, -2, -80); vertex(  0, 2, -80);
    vertex( 100, 2, 30); vertex( 100, -2, 30); vertex(  0, -2, -80); vertex(  0, 2, -80);
    vertex(-100, 2, 30); vertex(-100, -2, 30); vertex(100, -2,  30); vertex(100, 2,  30);
    vertex(-2,   0, 98); vertex(2,   0, 98); vertex(2, -30, 98); vertex(-2, -30, 98);
    vertex(-2,   0, 98); vertex(2,   0, 98); vertex(2,   0, 70); vertex(-2,   0, 70);
    vertex(-2, -30, 98); vertex(2, -30, 98); vertex(2,   0, 70); vertex(-2,   0, 70);
    endShape();
    
    popMatrix();
}

void serialEvent(Serial port) {
    try {
        // read serial buffer as string
        String reading = port.readString();
         
        // if we have any other bytes
        if (reading != null) 
        {
        // trim crap
        reading = trim(reading);
        
        // split the string at commas 
        // and convert sections into integers. 
        String quaternion[] = split(reading, ',');
        
        println(quaternion);
         if (quaternion.length == 4) {
           q0 = float(quaternion[0]); 
           q1 = float(quaternion[1]);
           q2 = float(quaternion[2]); 
           q3 = float(quaternion[3]);
         }
        
        // get quaternion from data packet
        q[0] = q0;
        q[1] = q1;
        q[2] = q2;
        q[3] = q3;
        
        // set our toxilibs quaternion to new data
        quat.set(q0, q1, q2, q3);
        }
      }
    catch(RuntimeException e) {
       e.printStackTrace();
   }
}

void drawCylinder(float topRadius, float bottomRadius, float tall, int sides) {
    float angle = 0;
    float angleIncrement = TWO_PI / sides;
    beginShape(QUAD_STRIP);
    for (int i = 0; i < sides + 1; ++i) {
        vertex(topRadius*cos(angle), 0, topRadius*sin(angle));
        vertex(bottomRadius*cos(angle), tall, bottomRadius*sin(angle));
        angle += angleIncrement;
    }
    endShape();
    
    // If it is not a cone, draw the circular top cap
    if (topRadius != 0) {
        angle = 0;
        beginShape(TRIANGLE_FAN);
        
        // Center point
        vertex(0, 0, 0);
        for (int i = 0; i < sides + 1; i++) {
            vertex(topRadius * cos(angle), 0, topRadius * sin(angle));
            angle += angleIncrement;
        }
        endShape();
    }
  
    // If it is not a cone, draw the circular bottom cap
    if (bottomRadius != 0) {
        angle = 0;
        beginShape(TRIANGLE_FAN);
    
        // Center point
        vertex(0, tall, 0);
        for (int i = 0; i < sides + 1; i++) {
            vertex(bottomRadius * cos(angle), tall, bottomRadius * sin(angle));
            angle += angleIncrement;
        }
        endShape();
    }
}

// Press r to reset the gyro
void keyPressed() {
  if (key == 'r') {
     port.write('r');
  }
  println("r pressed!");
}


```





