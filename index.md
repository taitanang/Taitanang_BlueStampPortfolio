# Gesture Controlled Robot
<!-- Replace this text with a brief description (2-3 sentences) of your project. This description should draw the reader in and make them interested in what you've built. You can include what the biggest challenges, takeaways, and triumphs from completing the project were. As you complete your portfolio, remember your audience is less familiar than you are with all that your project entails! -->

<!-- You should comment out all portions of your portfolio that you have not completed yet, as well as any instructions: -->
```HTML 
<!--- This is an HTML comment in Markdown -->
<!--- Anything between these symbols will not render on the published site -->
```

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Tai T | Leigh High School | Mechanical Engineering | Incoming Sophomore

![Headstone Image](Screenshot 2025-06-20 at 14.40.58.png)
  
# Final Milestone

<!--**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/F7M7imOVGug" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your final milestone, explain the outcome of your project. Key details to include are:
- What you've accomplished since your previous milestone
- What your biggest challenges and triumphs were at BSE
- A summary of key topics you learned about
- What you hope to learn in the future after everything you've learned at BSE -->



# Second Milestone

<!-- **Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/y3VAmNlER5Y" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your second milestone, explain what you've worked on since your previous milestone. You can highlight:
- Technical details of what you've accomplished and how they contribute to the final goal
- What has been surprising about the project so far
- Previous challenges you faced that you overcame
- What needs to be completed before your final milestone -->

# First Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/YEfgV6-EvwA?si=-ewlYNdKjjLRfh9_" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

### Description:

My project is the Gesture Controlled Robot. For the first milestone, I completed the hardware for both the car and the glove. I soldered wires to the four motors, allowing them to connect to the L298 motor driver. This component is wired to the Arduino Uno, which is the 'brain' of the robot. By uploading code to it, the Arduino can tell the motor driver whether to spin forward or backward and at what speed (refer to Milestone 1 code). A battery case is also connected, with an on/off switch, for power. Finally, I added a Bluetooth Module to be able to communicate with the glove. Figure 1 in the schematics section shows the setup. The glove hardware is much simpler, only including an Arduino Nano, the second Bluetooth Module, and an accelerometer. The accelerometer will be used to measure the tilt of the glove. The glove hardware can be seen in Figure 2. 

### Challenges:

A challenge I had was pairing the two Bluetooth Modules together, because they weren't going into 'AT Mode'. But after some code and hardware changes, they were able to pair. I also figured out that after setting up the modules, the EN connection should be removed to let the module start pairing. 

### Next steps:

After this milestone, I will work on the software portion of the project, utilizing the Bluetooth connection to be able to steer the robot. 

# Schematics 
![Main Car Schematic](Screenshot 2025-06-20 at 14.08.30.png)
Figure 1: Schematic of the Gesture Controlled Robot car.

![Glove Schematic](Screenshot 2025-06-20 at 14.37.05.png)
Figure 2: This is a schematic of the glove circuits. 

# Milestone 1 Code
<!-- Here's where you'll put your code. The syntax below places it into a block of code. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize it to your project needs. -->

### Driving code:
```c++

int enA = 5;
int in1 = 6;
int in2 = 7;
int in3 = 8;
int in4 = 9;
int enB = 10;

void driveforward(){
  digitalWrite(in1, HIGH);
  digitalWrite(in2, LOW);
  analogWrite(enA, 255);
  digitalWrite(in3, LOW);
  digitalWrite(in4, HIGH);
  analogWrite(enB, 255);
  delay(1000);
}

void drivebackward(){
  digitalWrite(in1, LOW);
  digitalWrite(in2, HIGH);
  analogWrite(enA, 255);
  digitalWrite(in3, HIGH);
  digitalWrite(in4, LOW);
  analogWrite(enB, 255);
  delay(1000);
}

void left(){
  digitalWrite(in1, HIGH);
  digitalWrite(in2, LOW);
  analogWrite(enA, 255);
  digitalWrite(in3, LOW);
  digitalWrite(in4, LOW);
  delay(1000);
}

void right(){
  digitalWrite(in1, LOW);
  digitalWrite(in2, LOW);
  analogWrite(enA, 255);
  digitalWrite(in3, LOW);
  digitalWrite(in4, HIGH);
  delay(1000);
}

void stop(){
  digitalWrite(in1, LOW);
  digitalWrite(in2, LOW);
  digitalWrite(in3, LOW);
  digitalWrite(in4, LOW);
  delay(1000);
}


void setup() {
  Serial.begin(9600);
  pinMode(enA, OUTPUT);
  pinMode(in1, OUTPUT);
  pinMode(in2, OUTPUT);
  pinMode(in3, OUTPUT);
  pinMode(in4, OUTPUT);
  pinMode(enB, OUTPUT);

}

void loop() {
  
  driveforward();
  stop();
  drivebackward();
  stop();
  //left();
  //stop();
  //right();
  //stop();

}
```

### Get data from accelerometer:
```c++
#include <Wire.h>

const int MPU = 0x68; // MPU6050 I2C address
float AccX, AccY, AccZ;

void setup() {
  Wire.begin();
  Wire.beginTransmission(MPU);
  Wire.write(0x6B); // PWR_MGMT_1 register
  Wire.write(0);     // set to zero (wakes up the MPU6050)
  Wire.endTransmission(true);
  Serial.begin(9600);
}

void loop() {
  Wire.beginTransmission(MPU);
  Wire.write(0x3B); // Start with register 0x3B (ACCEL_XOUT_H)
  Wire.endTransmission(false);
  Wire.requestFrom(MPU, 6, true); // request a total of 6 bytes
  
  AccX = (Wire.read() << 8 | Wire.read());
  AccY = (Wire.read() << 8 | Wire.read());
  AccZ = (Wire.read() << 8 | Wire.read());

  AccX = map(AccX, -17000, 17000, 0, 180);
  AccY = map(AccY, -17000, 17000, 0, 180);
  AccZ = map(AccZ, -17000, 17000, 0, 180);


  Serial.print("X: ");
  Serial.print(AccX);
  Serial.print("  Y: ");
  Serial.print(AccY);
  Serial.print("  Z: ");
  Serial.println(AccZ);
  delay(100);
}
```

### Set up AT Commands for Bluetooth Modules:
```c++
include <SoftwareSerial.h>
SoftwareSerial Bluetooth(2,3);

void setup() {
  Serial.begin(38400);
  // put your setup code here, to run once:
  //Serial.println("test");
  Bluetooth.begin(38400);
}

void loop() {
  // put your main code here, to run repeatedly:
  if (Serial.available()){
    Bluetooth.write(Serial.read());
  }
  if (Bluetooth.available()){
    Serial.write(Bluetooth.read());
  }
  
}
```

# Bill of Materials
<!-- Here's where you'll list the parts in your project. To add more rows, just copy and paste the example rows below.
Don't forget to place the link of where to buy each component inside the quotation marks in the corresponding row after href =. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize this to your project needs. -->

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |

# Other Resources/Examples
<!-- One of the best parts about Github is that you can view how other people set up their own work. Here are some past BSE portfolios that are awesome examples. You can view how they set up their portfolio, and you can view their index.md files to understand how they implemented different portfolio components.
- [Arduino to Motor Driver](https://www.youtube.com/watch?v=Ey4xoG970Go)
- [Bluetooth Setup](https://www.youtube.com/watch?v=I2qFXSe0W3w)
- [Example 3](https://arneshkumar.github.io/arneshbluestamp/) -->

# Starter Milestone
<iframe width="560" height="315" src="https://www.youtube.com/embed/UMIgmopNEKk?si=oT5W1rL70Gnku_Bd" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

### Description:

My starter project was the Weevil Eye. I chose this project because it made me work on my soldering skills, and I got something cool to take home as well. The function of the Weevil Eye is that when it senses that it is dark, two LED 'eyes' turn on, and when there is light, it turns off.

### Challenges:

I faced numerous challenges while soldering to complete this project. It was my first day soldering, so my technique wasn't the best, resulting in the Weevil Eye not functioning properly. After a bit more practice soldering, I restarted with a new Weevil Eye, and that time it worked as intended. 

### Next steps:

The primary purpose of this project was for me to learn soldering, and it was successful. If I need to solder for my intensive project, I will know how to do it. 
