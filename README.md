# Projeto Braço Robotico
#include <Servo.h>

// ======== SERVOS =========
Servo base;
Servo braco1;
Servo braco2;
Servo garra;

// ======== ÂNGULOS =========
int base_0 = 0;
int base_180 = 180;

int braco_alto_1 = 180;   // Ajuste se necessário
int braco_baixo_1 = 45;

int braco_alto_2 = 50;    // Ajuste se necessário
int braco_baixo_2 = 180;

int garra_aberta  = 0;
int garra_fechada = 140;

// ======== VELOCIDADE =========
int tempo = 10;

// ======== FUNÇÃO SUAVIZADA =========
void moverServo(Servo &s, int inicio, int fim) {
  inicio = constrain(inicio, 0, 180);
  fim    = constrain(fim,    0, 180);

  if (inicio < fim) {
    for (int i = inicio; i <= fim; i++) {
      s.write(i);
      delay(tempo);
    }
  } else {
    for (int i = inicio; i >= fim; i--) {
      s.write(i);
      delay(tempo);
    }
  }
}

void setup() {
  base.attach(3);
  braco1.attach(5);
  braco2.attach(6);
  garra.attach(9);

  // Posição inicial
  base.write(base_0);
  braco1.write(braco_alto_1);
  braco2.write(braco_alto_2);
  garra.write(garra_aberta);

  delay(1500);
}

void loop() {

  // 1) Base em 0 (alto já está)
  moverServo(base, base.read(), base_0);

  // 2) Abrir garra
  moverServo(garra, garra.read(), garra_aberta);

  // 3) Ir para 180
  moverServo(base, base_0, base_180);

  // 4) Descer braço
  moverServo(braco1, braco_alto_1, braco_baixo_1);
  moverServo(braco2, braco_alto_2, braco_baixo_2);

  // 5) Fechar garra (pegar objeto)
  moverServo(garra, garra_aberta, garra_fechada);

  // 6) Subir braço
  moverServo(braco1, braco_baixo_1, braco_alto_1);
  moverServo(braco2, braco_baixo_2, braco_alto_2);

  // 7) Voltar base para 0
  moverServo(base, base_180, base_0);

  // 8) Empurrar braço para frente de forma suave
  moverServo(braco2, braco_alto_2, braco_alto_2 + 25);
  delay(500); // tempo parado com o braço avançado

  // 9) Abrir garra (soltar objeto)
  moverServo(garra, garra_fechada, garra_aberta);
  delay(500); // tempo para garantir que soltou

  // 10) Voltar suavemente para a posição inicial do braço
  moverServo(braco2, braco_alto_2 + 25, braco_alto_2);
  delay(300); // pausa para evitar tranco
}
