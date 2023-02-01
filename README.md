# Sensore porta 1.0 | Arduino
![image](https://user-images.githubusercontent.com/117084283/216158204-8aa677cf-3be8-47aa-aecf-a785b7ce4140.png)

## Per creare il nostro sensore ci servirà:
- La nostra scheda **Arduino Uno**
- Una **Breadboard**
- **1 Led verde**
- **1 Led rosso**
- **Cavi** (16)
- **2 Resistori da 220 Ohm**
- Un **Buzzer passivo**
- Un **KY-022 Modulo ricevitore IR**
- Un **Sensore ad ultrasuoni HC-SR04**


Ora che abbiamo il nostro occorrente procediamo nel creare il nostro circuito, seguendo questo schema:
![image](https://user-images.githubusercontent.com/117084283/216169501-a4ba4705-f99d-4c62-b4fb-4be3aee183ff.png)


Una volta costruito il nostro circuito procediamo al codice:

    

    #include "IRremote.h" 


    /*-----( DICHIARAZIONI )-----*/

    //Sensore ad ultrasuoni
    #define trigPin 9
    #define echoPin 8

    //Sensore ad infrarossi
    int IR = 6; 
    IRrecv irrecv(IR);     // create instance of 'irrecv'
    decode_results IR_r;      // create instance of 'decode_results'

    //Led RGB

    int LED_R = 2;
    int LED_V = 4;

    //buzzer
    int T=400;

    //altre variabili
    bool IR_o = false;
    int i=0;
    int i1=0;
    int i2=0;
    int v;




    
    void translateIR() 
    /*-----( INIZIO TRADUTTORE IR )-----*/

    {

      switch(IR_r.value)

      {
      case 0xFFA25D: Serial.println("POWER"); break;
      case 0xFFE21D: Serial.println("FUNC/STOP"); break;
      case 0xFF629D: Serial.println("VOL+"); break;
      case 0xFF22DD: Serial.println("FAST BACK");    break;
      case 0xFF02FD: Serial.println("PAUSE");    break;
      case 0xFFC23D: Serial.println("FAST FORWARD");   break;
      case 0xFFE01F: Serial.println("DOWN");    break;
      case 0xFFA857: Serial.println("VOL-");    break;
      case 0xFF906F: Serial.println("UP");    break;
      case 0xFF9867: Serial.println("EQ");    break;
      case 0xFFB04F: Serial.println("ST/REPT");    break;
      case 0xFF6897: Serial.println("0");    break;
      case 0xFF30CF: Serial.println("1");    break;
      case 0xFF18E7: Serial.println("2");    break;
      case 0xFF7A85: Serial.println("3");    break;
      case 0xFF10EF: Serial.println("4");    break;
      case 0xFF38C7: Serial.println("5");    break;
      case 0xFF5AA5: Serial.println("6");    break;
      case 0xFF42BD: Serial.println("7");    break;
      case 0xFF4AB5: Serial.println("8");    break;
      case 0xFF52AD: Serial.println("9");    break;
      case 0xFFFFFFFF: Serial.println("REPEAT");break;  

      default: 
        Serial.println(" other button   ");
        Serial.println(IR_r.value);
      }

      delay(500);

    /*-----( FINE TRADUTTORE IR )-----*/
    } 


    void setup()   
    /*----( INIZIO SETUP )----*/
    {
      Serial.begin(9600);
      Serial.println("inizio");

      //inizializzazione sensore INFRAROSSI
  
      irrecv.enableIRIn(); // Attiviamo la ricezione IR

      //inizializzazione sensore ULTRASUONI
      pinMode(trigPin, OUTPUT);
      pinMode(echoPin, INPUT);
      digitalWrite(trigPin, LOW);

      //inizializzazione LED RGB
      pinMode(LED_R, OUTPUT);
      pinMode(LED_V, OUTPUT);


    }/*--( FINE SETUP )---*/


    void loop()   /*----( INIZIO LOOP )----*/
    {

    if (irrecv.decode (&IR_r)) // L'IR ha ricevuto un segnale?
    {
  
      translateIR();

      switch(IR_r.value)
  
      {
        /*POWER*/
        case 0xFFA25D: 
     
      Serial.println("IR ricevuto: " + IR_r.value);

      switch(IR_o) {
        case true:  digitalWrite(LED_R, HIGH);
                    digitalWrite(LED_V, LOW);
                    IR_o = false;
        break;

        case false: digitalWrite(LED_R, LOW);
                    digitalWrite(LED_V, HIGH);
                    IR_o = true;
        break;
        
        default: Serial.println("Nessun cambiamento rilevato");
        break;
      }
    break;

      }


  
    } 


    if(IR_o == true ) 
    {

      //Attivazione sensore US
      digitalWrite(trigPin, HIGH);
      delayMicroseconds(10);
      digitalWrite(trigPin, LOW);
  
      // calcolo del tempo attraverso il pin di echo
      long durata = pulseIn(echoPin, HIGH);
      long distanza = durata/58.31;

      Serial.println(distanza + "cm");

      if(distanza > 5) 
      {
        for (i2=0;i2<20;i2++) {
          digitalWrite(LED_R, HIGH);
          for(i=0;i<500;i++) {
            tone(11, T);
            T=T+1;
          }
          digitalWrite(LED_R, LOW);
          for(i1=0;i1<500;i1++) {
            tone(11, T);
            T=T-1;
          }
        }
  
      noTone(11);  
      }
      distanza = 0;
    }



    irrecv.resume(); // ricevi il prossimo valore IR


    }/* --( FINE LOOP )-- */

