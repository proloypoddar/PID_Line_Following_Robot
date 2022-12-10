
//LFR WITH PID


#include <QTRSensors.h>
#define AIN1 3
#define BIN1 7
#define AIN2 4
#define BIN2 8
#define PWMA 5
#define PWMB 6
#define STBY 9
#define NUM_SENSORS             6    
#define NUM_SAMPLES_PER_SENSOR  4    
#define EMITTER_PIN             11  
#define LED     13     

float KP = 0.01;
float KD = 1.0;
float Ki = 0.006;


int Velmax = 80;


int error1=0;
int error2=0;
int error3=0;
int error4=0;
int error5=0;
int error6=0;

         
QTRSensorsAnalog qtra((unsigned char[]) 
  {A0, A1, A2, A3, A4, A5}
, NUM_SENSORS, NUM_SAMPLES_PER_SENSOR, EMITTER_PIN);

unsigned int sensorValues[NUM_SENSORS];


void Motoriz(int value)
{
  if ( value >= 0 )
  {
    digitalWrite(BIN1,HIGH);
    digitalWrite(BIN2,LOW);
  }
  else
  {
    digitalWrite(BIN1,LOW);
    digitalWrite(BIN2,HIGH);
    value *= -1;
  }
  analogWrite(PWMB,value);
}

void Motorde(int value)
{  
  if ( value >= 0 )
  {
    digitalWrite(AIN1,HIGH);
    digitalWrite(AIN2,LOW);
  }
  else
  {
    digitalWrite(AIN1,LOW);
    digitalWrite(AIN2,HIGH);
    value *= -1;
  }    
  analogWrite(PWMA,value);
}

void Motor(int left, int righ)
{
  digitalWrite(STBY,HIGH);
  Motoriz(left);
  Motorde(righ);
}




void freno(boolean left, boolean righ, int value)
{
  digitalWrite(STBY,HIGH);
  if ( left )
  {
    digitalWrite(BIN1,HIGH);
    digitalWrite(BIN2,HIGH);
    analogWrite (PWMB, value);
  }
  if ( righ )
  {
    digitalWrite(AIN1,HIGH);
    digitalWrite(AIN2,HIGH);
    analogWrite (PWMA, value);
  }
}







void setup()
{

  pinMode(LED   ,OUTPUT);
  pinMode(BIN2  ,OUTPUT);
  pinMode(STBY  ,OUTPUT);
  pinMode(BIN1  ,OUTPUT);
  pinMode(PWMB  ,OUTPUT);
  pinMode(AIN1  ,OUTPUT);
  pinMode(AIN2  ,OUTPUT);
  pinMode(PWMA  ,OUTPUT);

  for ( int i=0; i<70; i++)
  {
    digitalWrite(LED, HIGH); delay(20);
    qtra.calibrate();
    digitalWrite(LED, LOW);  delay(20);
  }
  delay(3000);
}


unsigned int position = 0; 

int proporcional = 0;         
int integral = 0;           
int derivativo = 0;          
     



int diferencial = 0;  
int last_prop;         
int Target = 2500; 

void loop()
{   
 
  position = qtra.readLine(sensorValues, true, true);
  proporcional = ((int)position) - 2500;


  if ( proporcional <= -Target )
  {
    Motorde(0);
    freno(true,false,255);
  }
  else if ( proporcional >= Target )
  {
    Motoriz(0);
    freno(false,true,255);
  }
  
  derivativo = proporcional - last_prop; 
  integral = error1+error2+error3+error4+error5+error6;
  last_prop = proporcional;
  

  error6=error5;
  error5=error4;  
  error4=error3;  
  error3=error2;
  error2=error1;
  error1=proporcional;

 
  int diferencial = ( proporcional * KP ) + ( derivativo * KD )+ (integral*Ki) ;
  
  if ( diferencial > Velmax ) diferencial = Velmax; 
  else if ( diferencial < -Velmax ) diferencial = -Velmax;

  ( diferencial < 0 ) ? 
    Motor(Velmax+diferencial, Velmax) : Motor(Velmax, Velmax-diferencial);
}
