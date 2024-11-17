# Projeto: Otimização da Iluminação da Sala da FIAP

## **Objetivo do Projeto**
Criar um sistema de iluminação inteligente para uma sala de aula utilizando um ESP32, sensores de luz (LDR) e de proximidade (ultrassônico). O objetivo é garantir que o ambiente mantenha níveis mínimos de iluminação, especialmente durante a noite, por razões de segurança.

---

## **Descrição da Solução**
- **Sensores Utilizados**:
  - **LDR (Light Dependent Resistor):** Detecta os níveis de luz no ambiente.
  - **Sensor Ultrassônico:** Identifica a presença de movimento ou objetos próximos.
- **Atuador**:
  - **LED:** Representa a luz do ambiente que será controlada pelo sistema.
- **Controle**:
  - **ESP32:** Central de controle que processa os dados dos sensores e ativa/desativa o LED.

---

## **Funcionamento do Sistema**
- Durante o dia, caso a luz natural seja suficiente (medida pelo LDR), o LED permanece apagado.
- Durante a noite, se a sala estiver escura, o LED acende automaticamente para manter níveis mínimos de iluminação.
- O sensor ultrassônico serve como redundância, garantindo que o LED também acenda caso haja movimentação na sala, mesmo com níveis adequados de luz natural.

---

## **Materiais Necessários**
1. ESP32  
2. LDR  
3. Resistor (10 kΩ para o LDR)  
4. Sensor Ultrassônico (HC-SR04)  
5. LED e resistor (220 Ω para limitar a corrente)  
6. Protoboard e fios para conexões  

---

## **Montagem do Circuito**
1. **LDR:** Conectado em série com um resistor (10 kΩ) formando um divisor de tensão. O ponto intermediário conecta-se a um pino analógico do ESP32.  
2. **Sensor Ultrassônico:** Pinos TRIG e ECHO conectados a pinos digitais do ESP32.  
3. **LED:** Conectado ao ESP32 via resistor de 220 Ω.  
4. **ESP32:** Alimenta o sistema e faz o processamento das leituras dos sensores.  

---

## **Código-Fonte**
### Exemplo em Arduino IDE:
```cpp
#define LDR_PIN 34 // Pino do LDR
#define TRIG_PIN 26 // Pino TRIG do Ultrassônico
#define ECHO_PIN 27 // Pino ECHO do Ultrassônico
#define LED_PIN 25  // Pino do LED

int luzMinima = 300; // Valor mínimo de luz (ajustável)
int distanciaMaxima = 50; // Distância máxima para detecção de presença (em cm)

void setup() {
  pinMode(LED_PIN, OUTPUT);
  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);
  Serial.begin(115200);
}

void loop() {
  // Leitura do LDR
  int valorLDR = analogRead(LDR_PIN);
  Serial.println("Luminosidade: " + String(valorLDR));

  // Leitura do Ultrassônico
  long duracao = medirDistancia();
  int distancia = duracao * 0.034 / 2; // Converter duração para cm
  Serial.println("Distância: " + String(distancia) + "cm");

  // Lógica de controle
  if (valorLDR < luzMinima || distancia < distanciaMaxima) {
    digitalWrite(LED_PIN, HIGH); // Acende o LED
  } else {
    digitalWrite(LED_PIN, LOW);  // Apaga o LED
  }
  delay(1000); // Delay para leitura
}

long medirDistancia() {
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);
  return pulseIn(ECHO_PIN, HIGH);
}
