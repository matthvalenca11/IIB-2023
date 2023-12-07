#Arquivo do programa 

#include <BLEDevice.h>
#include <BLEUtils.h>
#include <BLEServer.h>

BLEServer* pServer = NULL;
BLECharacteristic* pCharacteristic = NULL;

#define SERVICE_UUID        "8fafc201-1fb5-459e-8fcc-c5c9c331914b" 
#define CHARACTERISTIC_UUID "4eb5483e-36e1-4688-b7f5-ea07361b26a8" 

void setup() {
  Serial.begin(115200);
  
  BLEDevice::init("MotorMonitor"); // Replace with your own device name
  pServer = BLEDevice::createServer();
  BLEService *pService = pServer->createService(SERVICE_UUID);
  pCharacteristic = pService->createCharacteristic(
                      CHARACTERISTIC_UUID,
                      BLECharacteristic::PROPERTY_READ |
                      BLECharacteristic::PROPERTY_WRITE
                    );
  pService->start();
  BLEAdvertising *pAdvertising = pServer->getAdvertising();
  pAdvertising->start();
}

void loop() {
 
  int sensorValue = analogRead(A0); 

  Serial.println(sensorValue);

  byte data[2];
  data[0] = sensorValue & 0xFF; 
  data[1] = (sensorValue >> 8) & 0xFF; 

  pCharacteristic->setValue(data, sizeof(data));
  pCharacteristic->notify();
  
  delay(25); 
}
