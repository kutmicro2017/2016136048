# 2016136048
/** 자동차신호등에서 사용하는 LED 핀위치 */
#define pinCarG 13
#define pinCarY 12
#define pinCarR 11
 
/** 사람이 사용하는 LED 핀위치 */
#define pinHumanG 10
#define pinHumanR 9
/** 사람이 사용하는 스위치 핀위치 */
#define pinHumanSw 8
 
// 신호등 녹색등이 점등되는 시간
#define TIME_USE_HUMAN 2 // sec
// 신호등이 녹색이 점멸되는 시간 (초)
#define BLINK_COUNT 5
 
unsigned long prevTime;
 
void setup() {
  // 디버깅이 필요한 경우 시리얼로 데이터를 전달
  // Serial.begin(9600);
   
  pinMode(pinCarG, OUTPUT);
  pinMode(pinCarY, OUTPUT);
  pinMode(pinCarR, OUTPUT);
 
  pinMode(pinHumanG, OUTPUT);
  pinMode(pinHumanR, OUTPUT);
  pinMode(pinHumanSw, INPUT_PULLUP);
  
 
 
 
 // Serial.println("Start Traffic Sign System.");
 
 
 
  initLED();
}

void loop() {
  int sw = digitalRead(pinHumanSw);
  
 
  if (sw == 0 && digitalRead(pinCarG) == 1) {
  
    /*
     * 사람이 누르는 스위치는 자동차신호등이 녹색인 상태에서만
     * 작동하도록 구현함
     */
     
    changeLED();
  } 
  else {
    unsigned long currentTime = millis();
    if (currentTime > prevTime + 10 * 1000) {
      /*
       * 스위치를 작동하지 않아도 차 신호등이 10초간 유지되면
       * 보행자 신호로 자동 변환하도록 설계함
       */
      changeLED();
      
    }
  }
 
}
 
void changeLED() {
  // 버튼클릭후 1초 대기
  // Serial.println("Press Switch. wait 3 sec.");
  delay(1000);
 
  // 차량 신호등에 노란색 점등
  // Serial.println("Turn on car yellow led during 3 sec.");
  digitalWrite(pinCarG, LOW);
  digitalWrite(pinCarY, HIGH);
  delay(2000);
 
  // 자동차 신호등을 빨강으로 전환
  digitalWrite(pinCarY, LOW);
  digitalWrite(pinCarR, HIGH);
  // 사람용 신호등도 변경한다.
  digitalWrite(pinHumanR, LOW);
  digitalWrite(pinHumanG, HIGH);
 
  // 2초간 횡단보도사용
  delay(TIME_USE_HUMAN * 1000);
 
  // 5초동안 점멸
  // Serial.println("Remain 5 sec using cross line.");
  for(int i = 0; i < BLINK_COUNT; i++) {
    digitalWrite(pinHumanG, LOW);
    delay(500);
    digitalWrite(pinHumanG, HIGH);
    delay(500);
  }
 
  // Serial.println("Reset traffic sign");
  // 신호등을 원래 상태로 전환한다.
  initLED();
}
 
void initLED() {
  digitalWrite(pinCarG, HIGH);
  digitalWrite(pinCarY, LOW);
  digitalWrite(pinCarR, LOW);
 
  digitalWrite(pinHumanG, LOW);
  digitalWrite(pinHumanR, HIGH);
 
  prevTime = millis();
}
