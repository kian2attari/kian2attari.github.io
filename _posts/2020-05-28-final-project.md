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

<video width="320" height="240" controls>
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

<video width="320" height="240" controls>
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







