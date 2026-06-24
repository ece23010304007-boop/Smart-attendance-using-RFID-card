# Smart-attendance-using-RFID-card


The main purpose of this project is to develop a Smart Attendance System using RFID and Arduino Uno that can automatically record student attendance in a fast, secure, and efficient manner. In this system, each student is provided with an RFID tag containing a unique identification number.

#features
(i) To develop a smart and automated attendance management system 

(ii) To improve attendance accuracy and eliminate errors caused by manual record 

(iii) To provide fast and reliable attendance recording through RFID scanning technology

(iv) To maintain attendance records efficiently in Excel sheet format

(v) To design a compact, low-cost, user-friendly, and reliable system using Arduino Uno, RFID Reader, and Zero PCB board.

#components

Hardware Description

(i)Arduino Uno & USB B Cable

(ii) RFID Reader RC522

(iii) RFID Tags

(iv) Buzzer and PCB Board

These are the components are required for the project

#circuit diagram 

RFID SDA pin → Arduino Uno D10 pin

RFID SCK pin → Arduino Uno D13 pin

RFID MOSI pin → Arduino Uno D11 pin

RFID MISO pin → Arduino Uno D12 pin

RFID RST pin → Arduino Uno D9 pin

RFID 3.3V pin → Arduino Uno 3.3V pin

RFID GND pin → Arduino Uno GND pin

Buzzer positive terminal → Arduino Uno digital output pin D7

Buzzer negative terminal → Arduino Uno GND pin

code/program 

#include <SPI.h> 
#include <MFRC522.h> 
#include <avr/pgmspace.h> 
#define SS_PIN 10 
#define RST_PIN 9 
#define BUZZER 8 
MFRC522 mfrc522(SS_PIN, RST_PIN);

struct Student { 
 const char* uid; 
 const char* name; 
 const char* roll; 
};

const Student students[] PROGMEM = { 
 {"8C D6 CB 06", "Aditiya", "23010304002"}, 
 {"57 09 DB 06", "Akash Guleriya", "23010304004"}, 
 {"14 68 E2 06", "Akshay", "23010304005"}, 
 {"0C 42 DB 06", "Akshit", "23010304006"}, 
 {"BB 9B DC 06", "Aman Sharma", "23010304007"}, 
 {"6C 35 DC 06", "Aman Thakur", "23010304008"}, 
 {"FE 57 E2 06", "Amit Katoch", "23010304009"}, 
 {"A6 7E E0 06", "Anchal Kumari", "23010304010"}, 
 {"1C 3C DB 06", "Aniket Mamania", "23010304011"}, 
 {"97 66 DB 06", "Anirudh Sudal", "23010304012"}, 
 {"55 51 E1 06", "Anita Devi", "23010304013"}, 
 {"01 AE E1 06", "Anjali Rai", "23010304014"}, 
 {"52 AF E1 06", "Ankush Mehta", "23010304015"}, 
 {"B3 BD E1 06", "Anshima Thakur", "23010304016"}, 
 {"95 68 E0 06", "Archna", "23010304017"}, 
 {"C9 62 E2 06", "Arman Sharma", "23010304018"}, 
 {"F6 EB E1 06", "Arpit Bugwan", "23010304019"}, 
 {"22 0F E1 06", "Ayush", "23010304020"}, 
 {"37 78 E0 06", "Ayush Dhiman", "23010304021"}, 
 {"12 58 E0 06", "Ayush Raj", "23010304022"}, 
 {"CB 6E DD 06", "Bhumika Guleria", "23010304023"}, 
 {"92 B6 E2 06", "Chahat Verma", "23010304024"}, 
 {"C6 02 E2 06", "Diksha Pathania", "23010304025"}, 
 {"E3 A1 E1 06", "Diksha Sharma", "23010304026"}, 
 {"19 5C E2 06", "Divyanshu Kamal", "23010304027"}, 
 {"3A 8F E0 06", "Gunjan", "23010304028"}, 
 {"B9 47 E2 06", "Harshit Jyoti", "23010304029"}, 
 {"CC DC E1 06", "Ishita Raj", "23010304030"}, 
 {"61 C7 E1 06", "Kanabh", "23010304031"}, 
 {"ED EE DB 06", "Kanika", "23010304032"}, 
 {"3E 63 DB 06", "Kanika Rana", "23010304033"}, 
 {"DB E4 E0 06", "Kartik Kumar", "23010304034"}, 
 {"91 39 DC 06", "Keshav Koundal", "23010304035"}, 
 {"2B 37 E1 06", "Khusbu Choudhary", "23010304036"}, 
 {"F1 9D E1 06", "Mayank", "23010304037"}, 
 {"0A 6C E0 06", "Minakshi", "23010304038"}, 
 {"6E 37 DC 06", "Muskan Sharma", "23010304039"}, 
 {"9B CE DC 06", "Naman Chopra", "23010304040"}, 
 {"6E BB E0 06", "Nikhil Kumar", "23010304041"}, 
 {"2A BD DC 06", "Nikhil Sundhu", "23010304042"} 
};

int totalStudents = sizeof(students) / sizeof(students[0]); 
bool present[50] = {false}; 
bool headerPrinted = false;

void beep(int duration) { 
 digitalWrite(BUZZER, HIGH); 
 delay(duration); 
 digitalWrite(BUZZER, LOW); 
} 

void setup() { 
 Serial.begin(9600); 
 SPI.begin(); 
 mfrc522.PCD_Init(); 
 pinMode(BUZZER, OUTPUT); 
 Serial.println("System Ready..."); 
} 

void loop() { 
 if (!headerPrinted) { 
 Serial.println("Time,Name,Roll No,Status");
 headerPrinted = true; 
 } 
 if (!mfrc522.PICC_IsNewCardPresent()) return; 
 if (!mfrc522.PICC_ReadCardSerial()) return; 
 String content = "";
 for (byte i = 0; i < mfrc522.uid.size; i++) { 
 content += String(mfrc522.uid.uidByte[i] < 0x10 ? "0" : "");
 content += String(mfrc522.uid.uidByte[i], HEX); 
 if (i != mfrc522.uid.size - 1) content += " "; 
 } 
 content.toUpperCase();
 bool found = false; 
 Student temp; 
 for (int i = 0; i < totalStudents; i++) { 
 memcpy_P(&temp, &students[i], sizeof(Student));
 if (content == String(temp.uid)) { 
 found = true; 
 if (!present[i]) { 
 present[i] = true; 
 Serial.print(millis()/1000); 
 Serial.print(","); 
 Serial.print(temp.name); 
 Serial.print(",");
 Serial.print(temp.roll); 
 Serial.println(",Present"); 
 beep(200); 
 } else { 
 Serial.print(millis()/1000); 
 Serial.print(","); 
 Serial.print(temp.name); 
 Serial.print(","); 
 Serial.print(temp.roll); 
 Serial.println(",Already"); 
 beep(100); 
 delay(100); 
 beep(100);
 } 
 } 
 } 
 if (!found) { 
 Serial.print(millis()/1000); 
 Serial.println(",Unknown,---,Invalid"); 
 beep(500); 
 } 
 delay(2000); 
} 

