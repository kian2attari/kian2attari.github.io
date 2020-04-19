---
title: Final project ideas
tags: Final-Project
---

# Enter the discount matrix: reading brains on a budget

## First test of data transmission

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

Note that the output on the serial monitor is gibberish because the Arduino code tells it to expect 'char', or character data. Therefore, it's trying to map the brain wave readings to actual letters. Silly computer.


