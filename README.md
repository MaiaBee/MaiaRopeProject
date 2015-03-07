# MaiaRopeProject
//Arduino code for SoundStrom 



/////////////////////////////////////////////////////////////////
///// Numbers in Var Names Relate to Position in the space. /////
///// 1 is Back Isobar                                      /////
///// 2 is Lexington Side                                   /////
///// 3 is Small 47th St. Side                              /////
/////////////////////////////////////////////////////////////////

// Difference in these speeds, for variation (check and begin), 
// and for length of travel (return)

// FOR Isobar 1, delays and travel times
int pauseBeforeCheck1 = 2000;
int pauseBeforeBegin1 = 4000;
int pauseBeforeReturn1 = 27000;

// FOR Isobar 2, delays and travel times
int pauseBeforeCheck2 = 3000;
int pauseBeforeBegin2 = 3000;
int pauseBeforeReturn2 = 19000;

// FOR Isobar 3, delays and travel times
int pauseBeforeCheck3 = 4000;
int pauseBeforeBegin3 = 2000;
int pauseBeforeReturn3 = 9000;

// Isobar Sensor PINS 
byte ropeSensePin1 = 4;
byte ropeSensePin2 = 2;
byte ropeSensePin3 = 3;

byte rope1PullPin = 5;
byte rope1PushPin = 6;
byte rope2PullPin = 7;
byte rope2PushPin = 8;
byte rope3PullPin = 14;
byte rope3PushPin = 15;

boolean isRope1AtReturn = false;
boolean isRope1ReturningHome = false;
boolean isRope1AtEnd = false;
boolean isRope2AtReturn = false;
boolean isRope2ReturningHome = false;
boolean isRope2AtEnd = false;
boolean isRope3AtReturn = false;
boolean isRope3ReturningHome = false;
boolean isRope3AtEnd = false;

boolean isRope1Triggered, isRope2Triggered, isRope3Triggered = false;

boolean debug = false;

long curTime1 = 0;
long curTime2 = 0;
long curTime3 = 0;

void setup(){
  Serial.begin(9600);
  
  pinMode(ropeSensePin1,INPUT);
  pinMode(ropeSensePin2,INPUT);
  pinMode(ropeSensePin3,INPUT);
  
  pinMode(rope1PullPin,OUTPUT);
  pinMode(rope1PushPin,OUTPUT);
  pinMode(rope2PullPin,OUTPUT);
  pinMode(rope2PushPin,OUTPUT);
  pinMode(rope3PullPin,OUTPUT);
  pinMode(rope3PushPin,OUTPUT);

  digitalWrite(rope1PushPin,HIGH);
  digitalWrite(rope2PushPin,HIGH);
  digitalWrite(rope3PushPin,HIGH);
  delay(2000);
  digitalWrite(rope1PullPin, HIGH);
  digitalWrite(rope2PullPin, HIGH);
  digitalWrite(rope3PullPin, HIGH);
  
  isRope1ReturningHome, isRope2ReturningHome, isRope3ReturningHome = true;
}

void loop(){
  
  checkRope(isRope1Triggered, ropeSensePin1, 1);
  checkRope(isRope2Triggered, ropeSensePin2, 2);
  checkRope(isRope3Triggered, ropeSensePin3, 3);
  
  if(!debug){
    
///////////////////////// COPY FROM HERE /////////////////////////  
///////////// Replace all instances of 1 w/ 2 or 3 ///////////////  

    if(isRope1ReturningHome){
      if(digitalRead(ropeSensePin1)){
        digitalWrite(rope1PullPin,LOW);
        isRope1ReturningHome = false;
        isRope1AtReturn = true;
        curTime1 = millis();
      }
    } 
    if(isRope1AtReturn){
      if(millis() > pauseBeforeBegin1 + curTime1){
        isRope1AtReturn = false;
        isRope1AtEnd = false;
        digitalWrite(rope1PushPin,HIGH);
        curTime1 = millis();
      }
    }
    
    if(!isRope1ReturningHome && !isRope1AtEnd && !isRope1AtReturn) {
      if(millis() > pauseBeforeReturn1 + curTime1){
        digitalWrite(rope1PushPin,LOW);
        curTime1 = millis();
        isRope1AtEnd = true;
        isRope1AtReturn = false;
        isRope1ReturningHome = false;
      }
    }
    
    if(isRope1AtEnd){
      if(millis() > pauseBeforeCheck1 + curTime1){
        isRope1AtEnd = false;
        isRope1ReturningHome = true;
        digitalWrite(rope1PullPin, HIGH);
      }
    }
    
///////////////////////// COPY TO HERE /////////////////////////
    
    
   
  }  // Place two copies before this closing brace
  
  
  
  
  if(Serial.available() > 0){
    int input = Serial.read();
    if( input == 'a') go(rope1PullPin,1000);
    if( input == 's') go(rope1PushPin,1000);
    if( input == 'd') go(rope2PullPin,1000);
    if( input == 'f') go(rope2PushPin,1000);
    if( input == 'z') go(rope3PullPin,1000);
    if( input == 'x') go(rope3PushPin,1000);
    if( input == 'j') Serial.println(digitalRead(ropeSensePin1));
    if( input == 'k') Serial.println(digitalRead(ropeSensePin2));
    if( input == 'l') Serial.println(digitalRead(ropeSensePin3));
    if( input == ' ') debug = !debug;
  }
}

void go(byte pin, int del){
  digitalWrite(pin,HIGH);
  delay(del);
  digitalWrite(pin,LOW);
  delay(250);
}

void checkRope(boolean ropeTrig, byte rope, int ropeNum){
  if(!ropeTrig){
    if(digitalRead(rope)){
      ropeTrig = true;
      Serial.print("Rope ");
      Serial.print(ropeNum);
      Serial.println(" Sensed");
    }
  } else {
    if(!digitalRead(rope)){
      ropeTrig = false;
    }
  } 
}
