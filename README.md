# Sensore porta 2.0 | Arduino
![image](https://user-images.githubusercontent.com/117084283/216158204-8aa677cf-3be8-47aa-aecf-a785b7ce4140.png)

Rispetto alla vecchia versione [1.0](github.com/Lod34/Sensore-porta-Arduino/blob/main/README.md?plain=1#L212), l'unica differenza è che al posto di utilizzare il pulsante come accensione del sensore, utilizzeremo un sensore a infrarossi.

## Questa volta ci servirà:
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
    IRrecv irrecv(IR);     
    decode_results IR_r;      

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


    void loop()   
    /*----( INIZIO LOOP )----*/
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

      if(distanza > 5)                      //Se il sensore ad ultrasuoni rileva uno spostamento,
      {                                     //fa lampeggiare il led rosso e attiva il buzzer
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
    
    
**NOTA**: Per utilizzare questo codice bisogna usufruire della libreria **IRremote.h**, utilizzando la versione specifica **2.2.3**, che, purtroppo entra in conflitto con il nostro **buzzer passivo**. Per risolvere tale conveniente ci basterà editare un file di testo. La guida [qui](https://forum.arduino.cc/t/irremote-library-conflicts-with-tone-function/118426/6).
Se invece volessimo utilizzare una versione più recente dovremo far attenzione a che comandi usare, non sempre funzionanti. Ecco la [guida](https://github.com/Arduino-IRremote/Arduino-IRremote#readme).




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
- Un **Pulsante**
- Un **Sensore ad ultrasuoni HC-SR04**


Ora che abbiamo il nostro occorrente procediamo nel creare il nostro circuito, seguendo questo schema:
![image](https://user-images.githubusercontent.com/117084283/217087456-6550059f-2666-485e-8eaa-2fb930124d8c.png)


Una volta costruito il nostro circuito procediamo al codice:

    

    /*-----( DICHIARAZIONI )-----*/

    //Sensore ad ultrasuoni
    #define trigPin 9
    #define echoPin 8

    //pulsante
    int pulsante = 6;
    int val = 0;
    
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


    void setup()   
    /*----( INIZIO SETUP )----*/
    {
      Serial.begin(9600);
      Serial.println("inizio");

      //inizializzazione sensore INFRAROSSI
  
      pinMode(pulsante, INPUT);

      //inizializzazione sensore ULTRASUONI
      pinMode(trigPin, OUTPUT);
      pinMode(echoPin, INPUT);
      digitalWrite(trigPin, LOW);

      //inizializzazione LED RGB
      pinMode(LED_R, OUTPUT);
      pinMode(LED_V, OUTPUT);


    }/*--( FINE SETUP )---*/


    void loop()   
    /*----( INIZIO LOOP )----*/
    {

    val == digitalRead(pulsante);
    
      
      
    switch(val) {
      case 1:       digitalWrite(LED_R, HIGH);
                    digitalWrite(LED_V, LOW);
                    delay(1000);
                    digitalWrite(LED_R, LOW);
      break;

      case 0:       digitalWrite(LED_R, LOW);
                    digitalWrite(LED_V, HIGH);
                    delay(1000);
                    digitalWrite(LED_V, LOW);
                    
      break;
        
    }
   


    if(val==1) 
    {

      //Attivazione sensore US
      digitalWrite(trigPin, HIGH);
      delayMicroseconds(10);
      digitalWrite(trigPin, LOW);
  
      // calcolo del tempo attraverso il pin di echo
      long durata = pulseIn(echoPin, HIGH);
      long distanza = durata/58.31;

      Serial.println(distanza + "cm");

      if(distanza > 5)                      //Se il sensore ad ultrasuoni rileva uno spostamento,
      {                                     //fa lampeggiare il led rosso e attiva il buzzer
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



    


    }/* --( FINE LOOP )-- */
    
    
