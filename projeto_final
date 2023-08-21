#include <Keypad.h>  // BIBLIOTECA PARA O FUNCIONAMENTO DO TECLADO DE MEMBRANA
#include <Servo.h> // BIBLIOTECA PARA O FUNCIONAMENTO DO SERVO
#include <Ethernet.h>

int cont = 0;
Servo servo_Motor; //OBJETO DO TIPO SERVO
char password[5] = "2115";
int position = 0; //VARIÁVEL PARA LEITURA DE POSIÇÃO DA TECLA PRESSIONADA
const byte ROWS = 4; //NUMERO DE LINHAS DO TECLADO
const byte COLS = 4; //NUMERO DE COLUNAS DO TECLADO
char keys[ROWS][COLS] = { //DECLARAÇÃO DOS NUMEROS, LETRAS E CARACTERES DO TECLADO
  {'1','4','7','*'},
  {'2','5','8','0'},
  {'3','6','9','#'},
  {'A','B','C','D'}
};
 
byte rowPins[ROWS] = {3, 2, 9, 8}; //connect to the row pinouts of the keypad
byte colPins[COLS] = {7, 6, 5, 4}; //connect to the column pinouts of the keypad

Keypad keypad = Keypad( makeKeymap(keys), rowPins, colPins, ROWS, COLS );//AS VARIAVEIS rowPins E colPins RECEBERÃO O VALOR DE LEITURA DOS PINOS DAS LINHAS E COLUNAS RESPECTIVAMENTE
 
const int ledVermelho = A1; //PINO EM QUE ESTÁ CONECTADO O LED VERMELHO
const int ledVerde = A3; //PINO EM QUE ESTÁ CONECTADO O LED VERDE


byte mac[] = {
  0xDE, 0xAD, 0xBE, 0xEF, 0xFE, 0xED };
IPAddress ip(192,168,1,7);          //Define o endereco IP
IPAddress gateway(192,168,1,1);     //Define o gateway
IPAddress subnet(255, 255, 255, 0); //Define a máscara de rede
 
//Inicializa o servidor web na porta 80
EthernetServer server(80);

void setup()
{
  Serial.print("começou");
  //Inicializa a interface de rede
  Ethernet.begin(mac, ip, gateway, subnet);
  server.begin(); 
  Serial.begin(9600);
  pinMode(ledVermelho, OUTPUT); //DECLARA O PINO COMO SAÍDA
  pinMode(ledVerde, OUTPUT); //DECLARA O PINO COMO SAÍDA
  servo_Motor.attach(A2); //PINO DE CONTROLE DO SERVO MOTOR
  char key = keypad.getKey();
  setLocked(true); //ESTADO INICIAL DA FECHADURA (TRANCADA)
  Serial.print(key);
  
}

void setLocked(int locked){ //TRATANDO O ESTADO DA FECHADURA
if (locked){ //SE FECHADURA TRANCADA, FAZ
    digitalWrite(ledVermelho, HIGH);// LED VERMELHO ACENDE
    digitalWrite(ledVerde, LOW);// LED VERDE APAGA
    servo_Motor.write(0); //POSIÇÃO DO SERVO FICA EM 0º (FECHADURA TRANCADA)
    char key = keypad.getKey();
    Serial.print(key);
}
else{ //SENÃO, FAZ
    digitalWrite(ledVerde, HIGH);// LED VERDE ACENDE
    digitalWrite(ledVermelho, LOW);// LED VERMELHO APAGA
    char key = keypad.getKey();
    Serial.print(key);
    cont = cont+1;
    servo_Motor.write(180);// SERVO GIRA A 82º (FECHADURA DESTRANCADA)
}
}

void loop(){
  char key = keypad.getKey(); //LEITURA DAS TECLAS PRESSIONADAS 
  if (key == '*' || key == '#') { //SE A TECLA PRESSIONADA POR IGUAL A CARACTERE "" OU "#", FAZ
      position = 0; //POSIÇÃO DE LEITURA DA TECLA PRESSIONADA INICIA EM 0
      setLocked(true); //FECHADURA TRANCADA
      Serial.print(key);
      Serial.println("Porta Esta Fechada");
      
}
if (key == password[position]){ //SE A TECLA PRESSIONADA CORRESPONDER A SEQUÊNCIA DA SENHA, FAZ
      position ++;//PULA PARA A PRÓXIMA POSIÇÃO
      char key = keypad.getKey();
      Serial.print(key);
}
if (position == 4){ // SE VARIÁVEL FOR IGUAL A 3 FAZ (QUANDO AS TECLAS PRESSIONADAS CHEGAREM A 3 POSIÇÕES, SIGNIFICA QUE A SENHA ESTÁ CORRETA)
      setLocked(false); //FECHADURA DESTRANCADA
      char key = keypad.getKey();
      Serial.print(key);
      Serial.println("Porta Esta Aberta");
}
delay(100);//INTERVALO DE 100 MILISSEGUNDOS


//Aguarda conexao do browser

EthernetClient client = server.available();
  if (client) {
    
    Serial.println("new client");
    // an http request ends with a blank line
    boolean currentLineIsBlank = true;
    while (client.connected()) {
      if (client.available()) {
        char c = client.read();
        Serial.write(c);
        // if you've gotten to the end of the line (received a newline
        // character) and the line is blank, the http request has ended,
        // so you can send a reply
        if (c == 'n' && currentLineIsBlank) {
          // send a standard http response header
          client.println("HTTP/1.1 200 OK");
          client.println("Content-Type: text/html");
          client.println("Connection: close");
          client.println("Refresh: 2"); 
          client.println();
          client.println("<!DOCTYPE HTML>");
          client.println("<html>");
          //Configura o texto e imprime o titulo no browser
          client.print("<font color=#FF0000><b><u>");
          client.print("Envio de informacoes pela rede utilizando Arduino");
          client.print("</u></b></font>");
          client.println("<br />");
          client.println("<br />");
          client.println("Estado da Porta: ");
          if (digitalRead(ledVerde)==HIGH){
           client.println("Porta Esta Aberta");
           client.println("<br />");
           client.println("Senha esta igual a Definida");
           Serial.print("abriu");  
           client.println("<br />");
          }
          else {
            client.println("Porta Esta Fechada");
            client.println("<br />");
          }
          client.println("Porta Foi Aberta : ");
          client.print(cont);
          client.print(" vezes");
          client.println("</b>");
          client.println("<br />");
          client.print("<b>");
          client.println("</b></html>");
   
          break;
        }
        if (c == 'n') {
          // you're starting a new line
          currentLineIsBlank = true;
        } 
        else if (c != 'r') {
          // you've gotten a character on the current line
          currentLineIsBlank = false;
        }
      }
    }
    // give the web browser time to receive the data
    delay(1);
    // close the connection:
    client.stop();  
  }
}
