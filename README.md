# hover_ESP32
That's a repository of a project using STM32 board from "Gen 1" hoverboards.

It uses the USART3 (Right sensor of the board) to communicate with an ESP32 that receives ASCII commands from the Bluetooth Serial Monitor.

All the credits of the base code to https://github.com/bipropellant

Use this code for ESP32, and the master branch for STM32
```
#include <BluetoothSerial.h>

BluetoothSerial SerialBT;
HardwareSerial SerialSTM(1);  // Utiliza UART1 para comunicação com a STM32

void setup() {
  // Inicializa a serial para comunicação com o computador
  Serial.begin(115200);
  
  // Inicializa a comunicação Bluetooth
  SerialBT.begin("ESP32_BT");  // Nome do dispositivo Bluetooth
  Serial.println("O dispositivo Bluetooth está pronto para emparelhamento.");

  // Inicializa a comunicação serial com a STM32
  SerialSTM.begin(115200, SERIAL_8N1, 16, 17);  // RX = GPIO16, TX = GPIO17

  // Espera que as conexões sejam estabelecidas
  while (!Serial && !SerialBT.connected()) {
    delay(100);
  }

  Serial.println("ESP32 está pronto para receber comandos via Bluetooth.");
}

void loop() {
  // Verifica se há dados disponíveis via Bluetooth
  if (SerialBT.available()) {
    String command = SerialBT.readStringUntil('\n');
    Serial.println("Comando recebido via Bluetooth: " + command);

    // Envia o comando para a STM32
    SerialSTM.println(command);
  }

  // Verifica se há resposta da STM32
  if (SerialSTM.available()) {
    String response = SerialSTM.readStringUntil('\n');
    Serial.println("Resposta da STM32: " + response);

    // Envia a resposta de volta via Bluetooth
    SerialBT.println(response);
  }

  delay(10);  // Pequeno atraso para evitar sobrecarregar o loop
}
```
