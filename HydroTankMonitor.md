// Work In Progress
#include <WiFi.h>
#include <WiFiClient.h>

int FloatSensor = 35;
int ledG = 12;
int ledR = 4;
int buttonState = LOW;  //reads pushbutton status

// Your WiFi credentials.
// Set password to "" for open networks.
char ssid[] = "";
char pass[] = "";
// You should get Auth Token in the Blynk App.
// Go to the Project Settings (nut icon).
char auth[] = "";

// pH variables
const int analogInPin = 35; 
int sensorValue = 0; 
unsigned long int avgValue; 
float b;
int buf[10],temp;

void setup() {
  Serial.begin(9600);
  Blynk.begin(auth, ssid, pass);
  pinMode(FloatSensor, INPUT_PULLUP);  //Arduino Internal Resistor 10K
  pinMode(ledG, OUTPUT);
  pinMode(ledR, OUTPUT);

  // Initial test on LEDs
  digitalWrite(ledG, HIGH);
  digitalWrite(ledR, HIGH);

  delay(10);
  Serial.print("Connecting to ");
  Serial.println(ssid);

  WiFi.begin(ssid, pass);
  int wifi_ctr = 0;
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println("WiFi connected");  
}

void loop() {
  buttonState = analogRead(FloatSensor);
  if (buttonState == LOW) {
    digitalWrite(ledG, HIGH);
    digitalWrite(ledR, LOW);
    Serial.println(buttonState);
    delay(2000);
    }
  else {
    digitalWrite(ledR, HIGH);
    digitalWrite(ledG, LOW);
    Serial.println(buttonState);
    buttonState = LOW;
    delay(2000);
  }
  for(int i=0;i<10;i++) 
  { 
    buf[i]=analogRead(analogInPin);
    Serial.print("AD = ");
    Serial.println(buf[i]);
    delay(10);
  }
  for(int i=0;i<9;i++)
    {
    for(int j=i+1;j<10;j++)
      {
      if(buf[i]>buf[j])
        {
        temp=buf[i];
        buf[i]=buf[j];
        buf[j]=temp;
      }
    }
  }
  avgValue=0;
  for(int i=2;i<8;i++)
    avgValue+=buf[i];
  //float pHVol=(float)avgValue*5.0/1024/6;
  float pHVol=(float)avgValue/6*3.4469/4095;
  Serial.print("v = ");
  Serial.println(pHVol);

  float phValue = -5.70 * pHVol + 21.34;    
  //float phValue = 7 + ((2.5 - pHVol) / 0.18);
  Serial.print("Ph=");
  Serial.println(phValue);
}
