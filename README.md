# 🎮 Projeto Genius (Simon Game) com Arduino

Este é um jogo **Genius (Simon)** feito com **Arduino**, que desafia sua memória com sequências de luzes e sons. 

## 📦 Componentes necessários

- 4 Botões (cores: verde, vermelho, amarelo e azul)
- 4 LEDs (mesmas cores dos botões)
- 1 Buzzer
- 1 Arduino UNO (ou compatível)
- Jumpers
- Protoboard

## ⚙️ Conexões dos componentes

| Componente  | Pino Arduino |
|-------------|--------------|
| LED Verde   | 2            |
| LED Vermelho| 3            |
| LED Amarelo | 4            |
| LED Azul    | 5            |
| Botão Verde | 6            |
| Botão Vermelho | 7         |
| Botão Amarelo | 8          |
| Botão Azul  | 9            |
| Buzzer      | 10           |

> Obs: Os botões devem ser conectados com **pull-down resistors** ou configurados via `INPUT_PULLUP` com lógica invertida, caso preferir.
## ⚙️ Código do Projeto

```cpp
// Definindo os pinos dos LEDs e botões
const int ledVerde = 2;
const int ledVermelho = 3;
const int ledAmarelo = 4;
const int ledAzul = 5;

const int botaoVerde = 6;
const int botaoVermelho = 7;
const int botaoAmarelo = 8;
const int botaoAzul = 9;

const int buzzer = 10;

int sequencia[100];  // Sequência gerada pelo jogo
int tamanhoSequencia = 0; // Tamanho da sequência atual
int jogador = 1;  // Controle para o modo 2 jogadores

void setup() {
  // Configurando os pinos dos LEDs e botões
  pinMode(ledVerde, OUTPUT);
  pinMode(ledVermelho, OUTPUT);
  pinMode(ledAmarelo, OUTPUT);
  pinMode(ledAzul, OUTPUT);

  pinMode(botaoVerde, INPUT_PULLUP);
  pinMode(botaoVermelho, INPUT_PULLUP);
  pinMode(botaoAmarelo, INPUT_PULLUP);
  pinMode(botaoAzul, INPUT_PULLUP);
  pinMode(buzzer, OUTPUT);

  Serial.begin(9600);
}

void loop() {
  if (jogador == 1) {
    modoMemoria();
  } else {
    modo2Jogadores();
  }
}

void modoMemoria() {
  gerarSequencia();
  mostrarSequencia();
  if (verificarSequencia()) {
    tamanhoSequencia++;
    delay(500);
  } else {
    jogoPerdido();
  }
}

void gerarSequencia() {
  sequencia[tamanhoSequencia] = random(4);  // Escolhe um número aleatório entre 0 e 3
}

void mostrarSequencia() {
  for (int i = 0; i <= tamanhoSequencia; i++) {
    acionarLed(sequencia[i]);
    delay(1000);
    desligarLeds();
    delay(500);
  }
}

void acionarLed(int cor) {
  switch (cor) {
    case 0:
      digitalWrite(ledVerde, HIGH);
      tone(buzzer, 1000);  // Som associado ao verde
      break;
    case 1:
      digitalWrite(ledVermelho, HIGH);
      tone(buzzer, 1500);  // Som associado ao vermelho
      break;
    case 2:
      digitalWrite(ledAmarelo, HIGH);
      tone(buzzer, 2000);  // Som associado ao amarelo
      break;
    case 3:
      digitalWrite(ledAzul, HIGH);
      tone(buzzer, 2500);  // Som associado ao azul
      break;
  }
}

void desligarLeds() {
  digitalWrite(ledVerde, LOW);
  digitalWrite(ledVermelho, LOW);
  digitalWrite(ledAmarelo, LOW);
  digitalWrite(ledAzul, LOW);
  noTone(buzzer);
}

bool verificarSequencia() {
  for (int i = 0; i <= tamanhoSequencia; i++) {
    int botaoPressionado = aguardarEntrada();
    if (botaoPressionado != sequencia[i]) {
      return false;
    }
  }
  return true;
}

int aguardarEntrada() {
  while (true) {
    if (digitalRead(botaoVerde) == LOW) return 0;
    if (digitalRead(botaoVermelho) == LOW) return 1;
    if (digitalRead(botaoAmarelo) == LOW) return 2;
    if (digitalRead(botaoAzul) == LOW) return 3;
  }
}

void jogoPerdido() {
  for (int i = 0; i < 3; i++) {
    digitalWrite(ledVermelho, HIGH);
    tone(buzzer, 500);
    delay(300);
    digitalWrite(ledVermelho, LOW);
    noTone(buzzer);
    delay(300);
  }
  tamanhoSequencia = 0;  // Reinicia o jogo
  jogador = 2;  // Alterna para o modo 2 jogadores
}

void modo2Jogadores() {
  // Lógica do jogo para 2 jogadores
  Serial.println("Modo 2 Jogadores");

  // Similar à lógica de modo memória, mas alternando os jogadores
  if (jogador == 1) {
    // Jogador 1 joga
    if (!verificarSequencia()) {
      Serial.println("Jogador 1 perdeu!");
      jogador = 2;
    }
  } else {
    // Jogador 2 joga
    if (!verificarSequencia()) {
      Serial.println("Jogador 2 perdeu!");
      jogador = 1;
    }
  }
}
