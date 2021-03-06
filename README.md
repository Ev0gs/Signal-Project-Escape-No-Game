# Signal-Project-Escape-No-Game
This project is one I've done for my studies. It explains the fact I was in a project team of 3 classmates. The subject of this project is Signal and Signal Modeling with Python code on Jupyter Notebook (anaconda 3).

# Introduction
### Context :
During a previous undercover mission, an agent of an independent cybercrime agency lost an agent because he could no longer communicate with the agency. Indeed, in this situation, he only had a pad and the microphone of the conference room in which he had hidden. This agency recruited us in order to find and elaborate a solution so that this situation would not happen again, and that the agent could continue to communicate with the listening unit.

### Problematic :
How can communication between an agent and the listening unit be made possible when the agent is in a situation similar to the one of the missing agent?

# Our solution
Our solution is to allow the agent to communicate via the microphone of the conference room when it is on, that is to say when people are present in this room. As the agent must not be spotted, he will send ultrasounds via his miniature pad special spy. Indeed, ultrasounds are very practical because they are inaudible to the human ear and therefore undetectable.\
This is because ultrasound is a type of sound whose frequency is higher than that of sounds audible to the human ear (i.e. sounds between 20Hz and 20KHz).\
Moreover, the ultrasound message of the agent will be encrypted (in Morse code for example) so that the message cannot be understood immediately if it is intercepted.

<p align="center">
  <img width="600" height="400" src="https://user-images.githubusercontent.com/93186642/138861528-ad8ab9f4-54f8-4a3a-ad65-b6464fbf773c.png">
</p>

So, to sum up, the undercover agent will write the message on his pad, then it will be encrypted by the pad and diffused in ultrasound. Finally, the microphone of the conference room will receive the ultrasounds and transform it back into electric power which will be received and decrypted by the listening unit.

<p align="center">
  <img width="400" height="400" src="https://user-images.githubusercontent.com/93186642/138862289-1daa907c-1a9d-45db-887c-4427fb7d47f4.png">
</p>

# Python Code
## Sending phase
### Module importation
First of all, we had to import all the packages needed to make the code functional.
```
#Importation des modules
%matplotlib inline
%pip install sounddevice
import numpy as np                   #Ce module permet d???effectuer des calculs num??riques avec Python
import matplotlib.pyplot as plt      #Ce module permet de cr??er et personnaliser des graphiques
import sounddevice as sd             #Ce module permet de jouer un son
```
### Undercover Agent writing the message
Then, the pad will ask the undercover agent to enter his message if he has one. Then, if he write a message the pad will store it in the Message variable.
```
#Ecriture du message de l'Agent sous forme de texte
Message = str(input('Entrez le message ?? envoyer : '))
```

### Conversion text message to binary sequence
If we want the message to be in a binary form we need to convert it by using the ASCII table.
```
total_binary=''                         #Cr??ation d'une cha??ne de caract??re vide "total_binary" qui va contenir le message binaire complet
for i in range(len(Message)):           #Cr??ation d'une boucle for qui se r??p??tera jusqu'?? atteindre la valeur de la taille du message en partant de 0
    binary=''                           #Cr??ation d'une cha??ne de caract??re vide "binary" qui va stocker les 7 bits correspondant ?? chaque lettre du message
    string_ord = ord(Message[i])        #Renvoie le d??cimal correspondant ?? chaque carat??re de la cha??ne de caract??re
    while string_ord>0:                 #Boucle tant que se r??p??tant tant que la d??cimale du caract??re est sup??rieure ?? 0
        x = string_ord % 2              #Stockage du reste de la division euclidienne de la d??cimale par 2
        string_ord = string_ord // 2    #Stockage du r??sultat de la division euclidienne de la d??cimale par 2 sans le reste.
        binary = str(x) + str(binary)   #Stockage de chaques reste correcpondants ?? un bit (0 ou 1)
    total_binary += binary              #Stockage et ajout du code binaire (7 bits) correspondant ?? chacune des lettres du message

print(total_binary)                     #Affichage du message binaire complet
```
The conversion of the text message into binary is an important step allowing us to transform the text written by the Agent into a binary character string. In order to realize this step, we used a for loop and a while loop. The for loop allows us to transform the message into decimal for each letter using the ord() function. Then, the while loop allows us to convert the decimal of each letter into a binary code of 7bits thanks to the modulo 2 (that is to say the remainder of the euclidian division of the decimal by 2). Finally, we add all the binary codes of each letter in a string and we find the final binary code representing the sent message.

### CRC encryption
CRC (Cyclic Redundancy Check) is a checksum algorithm to detect inconsistency of data, e.g. bit errors during data transmission. A checksum, calculated by CRC, is attached to the data to help the receiver to detect such errors.\
CRC is based on division. The actual input data is interpreted as one long binary bit stream (divident) which is divided by another fixed binary number (divisor). The remainder of this division is the checksum value.\
_cf. http://www.sunshine2k.de/articles/coding/crc/understanding_crc.html_
```
#D??finition de la fonction XOR utilis??e pour la division
def xor(a,b):
    resultat = []                                     #Initialisation du r??sultat sous forme d'une liste vide
    for i in range(1,len(b)):                         #Cr??ation d'une boucle pour appliquer la fonction XOR (si les bits sont les m??me, alors XOR vaut 0, sinon XOR vaut 1)
        if a[i]== b[i]:                               #Cr??ation d'un conditionnel if qui va comparer 2 bits et appliquer la fonction XOR
            resultat.append('0')                      #Ajout de le caract??re '0' ?? la liste resultat
        else:
            resultat.append('1')                      #Ajout de le caract??re '1' ?? la liste resultat
    return ''.join(resultat)                          #On retourne les donn??es de la fonction XOR sous forme d'une cha??ne de caract??res sans espaces

#D??finition de la fonction division euclidienne
def Division_eucl(divident,diviseur):
    val = len(diviseur)                               #Stockage de la taille du diviseur dans la variable "val"
    tmp = divident[0 : val]                           #Stockage des val-premi??res donn??es du divident
    while val<len(divident):                          #Cr??ation boucle while qui va s'executer tant que val est inf??rieur ?? la taille du divident
        if tmp[0]=='1':                               #Cr??ation conditionnel if qui va comparer le caract??re '1' ?? la premi??re valeur du divident
            tmp = xor(diviseur, tmp)+divident[val]    #Stockage du r??sultat de l'addition de la val-i??me valeur du divident et de xor(diviseur,tmp)
        else:
            tmp = xor('0'*val, tmp)+divident[val]     #Stockage du r??sultat de l'addition de la val-i??me valeur du divident et de xor('0'*val, tmp)
        val+=1                                        #Incr??mentation de la variable "val"
    if tmp[0]=='1':                                   #Cr??ation d'un conditionnel if qui va comparer le caract??re '1' ?? la premi??re valeur du divident en dehor de la boucle while 
        tmp = xor(divisor, tmp)                       #Stockage du r??sultat de xor(divisor,tmp)
    else:
        tmp = xor('0'*val, tmp)                       #Stockage du r??sultat de xor('0'*val, tmp)
    check = tmp                                       #Stockage du r??sultat final de tmp dans la variable "check"
    return check                                      #On retourne la valeur de check

#D??finition de la fonction d'encodage CRC (total_binary + 'N-bits 0', G (la cl??/polynom g??n??rateur))
def encodage(data, key):
    length_key = len(key)                             #Stockage de la taille de la cl?? dans une variable
    data_ajout = data + '0'*(length_key-1)            #Ajout des (length_key-1)'0' correspondant au nombre de '0' par rapport ?? la taille de la cl??-1
    reste = Division_eucl(data_ajout, key)            #Stockage du reste de la division euclidienne des donn??es par la cl??
    data_encode = data + reste                        #Stockage du message binaire ?? envoyer
    return data_encode                                #On retourne le message binaire

#Fonction Main
data = total_binary                                   #Ajout des 0 correspondant ?? la taille de la cl??
key = '11010'                                         #Initialisation de la cl??
print("Les donn??es ?? envoyer sont :", data)           #Affichage des donn??es ?? envoyer
print("La cl?? CRC est:", key)                         #Affichage de la cl??

data_crc = encodage(data,key)                         #Encodage CRC du message avec la cl??
print("Les donn??es encod??es avec la cl?? key sont :", data_crc) #Affichage du message encod?? avec la cl??
```
In order to allow the Agent in charge of listening to check if the received message does not contain any error, we have implemented a CRC encoding at transmission. Indeed, to carry out this code, we defined several functions such as the function "xor" (allowing to allot a binary code corresponding to the action of a xor gate), "Division_eucl" (allowing to carry out a Euclidean division) and "encoding" (allowing to encode the message in CRC via the function "Division_eucl"). Finally, we find ourselves with the display of the data to be sent, the CRC key and the data encoded with the key which represent the message to be processed throughout the code until the CRC verification.

### Manchester encoding
In telecommunication and data storage, Manchester code is a line code in which the encoding of each data bit is either low then high, or high then low, for equal time.

<p align="center">
  <img width="600" height="400" src="https://user-images.githubusercontent.com/93186642/138866973-82bcbd98-ec14-4b85-9db2-4d9c0d928ea8.png">
</p>

```
message_bool = []                          #Initialisation d'une liste vide
for i in range(0,len(data_crc),1):         #Boucle for allant de 0 ?? la valeur de la taille du message cod?? en CRC par pas de 1
    if data_crc[i]=='1':                   #Conditionnel if qui compare le caract??re '1' ?? la i-??me valeur du message cod?? en CRC
        message_bool.append(True)          #Ajout d'un bool??en True ?? la liste "message_bool" si condition v??rifi??e
    if data_crc[i]=='0':                   #Conditionnel if qui compare le caract??re '0' ?? la i-??me valeur du message cod?? 
        message_bool.append(False)         #Ajout d'un bool??en False ?? la liste "message_bool" si condition v??rifi??e
#Codage Manchester du message
message_code=[]                            #Initialisation d'une liste vide
for ii in range(0,len(message_bool)):      #Boucle for allant de 0 ?? la valeur de la taille de "message_bool" par pas de 1
    if message_bool[ii]==True:             #Conditionnel if qui compare le bool??en "True" ?? la ii-??me donn??e de la liste "message_bool"
        message_code.extend([int(1)])      #Ajout d'un entier 1 dans la liste "message_code" si la condition est v??rifi??e
        message_code.extend([int(0)])      #Ajout d'un entier 0 ?? la suite du 1 dans la liste "message_code" si la condition est v??rifi??e
    if message_bool[ii]==False:            #Conditionnel if qui compare le bool??en "False" ?? la ii-??me donn??e de la liste "message_bool"
        message_code.extend([int(0)])      #Ajout d'un entier 0 dans la liste "message_code" si la condition est v??rifi??e
        message_code.extend([int(1)])      #Ajout d'un entier 1 ?? la suite du 0 dans la liste "message_code" si la condition est v??rifi??e
print(message_code)                        #Affichage du message cod?? en Manchester
```
This part of the code represents the Manchester coding. Indeed, it represents all the steps allowing to pass from a simple message to a coded message via "for" loops and to apply the Manchester method (1->10 / 0->01). In order to double the message data, we used twice the extend() function of the "message_code" list in the "if" conditionals.

### ASK Modulation (on signal)
Amplitude-shift keying is a form of amplitude modulation that represents digital data as variations in the amplitude of a carrier wave. In an ASK system, a symbol, representing one or more bits, is sent by transmitting a fixed-amplitude carrier wave at a fixed frequency for a specific time duration.

<p align="center">
  <img width="600" height="400" src="https://user-images.githubusercontent.com/93186642/138867333-8a2163ca-4125-4bf2-af49-b21aa5bfe1ad.png">
</p>

```
#Modulation ASK du message
M = message_code                           #Stockage du mesage dans une variable "M"
Fe = 88200                                 #Initialisation de la fr??quence d'??chantillonnage ?? 88 200Hz (soit le double de 44 100Hz)
Fp = 35000                                 #Initialisation de la fr??quence de la porteuse ?? 35 000Hz (Pour que le son sot inaudible)
baud = 600                                 #Initialisation du d??bit souhait?? sur le canal de transmission ?? 600bit/s
Nbits = len(M)                             #Initialisation du nombre de bits initial
Ns = Fe/baud                               #Initialisation du nombre de symboles par bit (Fr??quence d'??chantillonnage/d??bit binaire)
N = Nbits*Ns                               #Initialisation du nombre de bits total ?? moduler (Nombre de symbole par bits*Nombre de bits)

M_duplique=np.repeat(M,Ns)                 #On g??n??re le message binaire dupliqu??

t = np.arange (0.0,N)/Fe                   #On g??n??re le vecteur temps

Ap=1
Porteuse = Ap*np.sin(2*np.pi*Fp*t)         #On g??n??re la porteuse P(t)

ASK = Porteuse*M_duplique                  #On r??alise la modulation en amplitude (ASK)

#Affichage Message binaire
plt.figure (figsize = (10,6))
plt.plot(t,M_duplique,'Black')
plt.title('Message M(t)')
plt.xlabel('temps (s)')
plt.ylabel('Amplitude')
plt.grid()

#Affichage du signal modul??
plt.figure (figsize = (10,6))
plt.plot(t,ASK,'b')
plt.title('Modulation AM(t)')
plt.xlabel('temps (s)')
plt.ylabel('Amplitude')
plt.grid()
```

<p align="center">
  <img width="600" height="400" src="https://user-images.githubusercontent.com/93186642/138867449-b97c7cc4-bd87-403a-86b2-754cdbdab6aa.png">
</p>

This step is the ASK modulation. This one was set up via data that we defined ourselves such as "baud" being the desired rate on the transmission channel (in bit/s), "Nbits" being the number of initial bits (size of the message), "Ns" being the number of symbols per bit (Sampling frequency / Bit rate), "N" being the total number of bits to be modulated (Number of symbol per bit * Number of bits). However, as we had chosen a carrier frequency of 35 000Hz to make the message inaudible, we decided to double the value of 44100Hz for the sampling frequency (88200Hz) because according to Shannon Nyquist's theorem, the value of the sampling frequency must be at least twice the value of the signal frequency. Finally, via the ASK modulation, we transform the binary message in the form of a digital signal into an analog signal of frequency 35,000Hz, i.e. an inaudible sound.

### FSK Modulation (on signal)
Frequency-shift keying is a frequency modulation scheme in which digital information is transmitted through discrete frequency changes of a carrier signal.

<p align="center">
  <img width="600" height="400" src="https://user-images.githubusercontent.com/93186642/138867668-95c3340f-e382-4228-902d-dca3fd1af647.png">
</p>

```
#Modulation FSK
M = message_code                           #Stockage du mesage dans une variable "M"
Fe = 88200                                 #Initialisation de la fr??quence d'??chantillonnage ?? 88 200Hz (soit le double de 44 100Hz)
Fp = 35000                                 #Initialisation de la fr??quence de la porteuse ?? 35 000Hz (Pour que le son sot inaudible)
baud = 600                                 #Initialisation du d??bit souhait?? sur le canal de transmission ?? 600bit/s
Nbits = len(M)                             #Initialisation du nombre de bits initial
Ns = Fe/baud                               #Initialisation du nombre de symboles par bit (Fr??quence d'??chantillonnage/d??bit binaire)
N = Nbits*Ns                               #Initialisation du nombre de bits total ?? moduler (Nombre de symbole par bits*Nombre de bits)

M_duplique = np.repeat(M,Ns)                                                  # On g??n??re le message binaire dupliqu??

t = np.arange (0.0,N)/Fe                                                      #On g??n??re le vecteur temps

# G??n??ration des 2 porteuse P1 pour le bit 1 et P2 pour le bit 0
A1 =  1                                        
A2 =  1                  
fp1 =  35000                
fp2 =  36000 
P1 =  A1*np.sin(2*np.pi*fp1*t)                                               
P2 =  A2*np.sin(2*np.pi*fp2*t)

FSK=[P1[i] if M_duplique[i]==1 else P2[i] for i in range(len(M_duplique))]    #Dans une boucle for allant de 0 ?? la taille du message binaire dupliqu??, on applique la valeur de p1[i] ?? FSK[i] si la i-??me valeur du message binaire dupliqu?? est ??gale ?? 1 sinon on lui applique la valeur de p2[i]

#Affichage du message
plt.figure (figsize = (10,6))
plt.plot(t,M_duplique,'Black')
plt.title('Message M(t)')
plt.xlabel('temps (s)')
plt.ylabel('Amplitude')
plt.grid()

#Affichage du signal modul??
plt.figure (figsize = (10,6))
plt.plot(t,FSK,'r')
plt.title('Modulation FM(t)')
plt.xlabel('temps (s)')
plt.ylabel('Amplitude')
plt.axis([0,0.009,-1,1])
plt.grid()
```

<p align="center">
  <img width="600" height="400" src="https://user-images.githubusercontent.com/93186642/138867779-5b719f77-18f4-4f9b-aed1-1344ce2a5524.png">
</p>

In order to show our skills, we decided to implement an additional feature to our code such as a second alternative modulation, the FSK modulation. This one is based on the same values as the ASK modulation but here we have two carriers (of frequency 34 000Hz and 35 000Hz) which will allow to realize this type of modulation. However, we find that the FSK modulation is still a type of modulation to be avoided in our case because it could have problems and make the signal audible even if it is very unlikely.

### Sending message in sound form

```
#??mission du message sous forme de signal (Jouer le son)
sd.play(ASK, Fe)
```

The transmission of the message in the form of sound is a fundamental step of the code because it allows the infiltrated Agent to send his message in the form of sound through the microphone of the conference room so that it is then received by the computer of the Agent in charge of listening.

## Receiving phase
### ASK demodulation of the received signal
```
#Initialisation des donn??es n??cessaires ?? la d??modulation ASK
Fe = 88200
Fp = 35000
baud = 600
N = len(ASK)
Ns = Fe/baud

t = np.arange (0.0,N)/Fe                                       #G??n??ration du vecteur temps

Porteuse=np.sin(2*np.pi*Fp*t)                                  #G??n??ration de la porteuse
Produit=ASK*Porteuse

#Int??gration
y=[]                                                           #Initialisation d'une liste vide
for i in range(0,int(N),int(Ns)):                              #Boucle for allant de 0 ?? N par pas de NS
    y.append(np.trapz(Produit[i:i+int(Ns)],t[i:i+int(Ns)]))    #Ajout du r??sultat de l'int??grale des donn??es du Produit de l'ASK avec la Porteuse en fonction du temps allant de i ?? i+Ns

#Affichage de l'ASK
plt.figure (figsize = (10,6))
plt.plot(t,ASK,'b')
plt.title('ASK')
plt.xlabel('temps (s)')
plt.ylabel('Amplitude')
plt.grid()

#Affichage des donn??es de y
print("Les valeurs de y sont :",y)
```

The ASK demodulation is the first stage of the reception phase. Indeed, this one allows via the code above to carry out the reverse of the ASK modulation carried out at the time of the emission of the message and thus to find values in a variable, "y" in our case, being the results of the integral of the product of the ASK and the carrier. These values will then allow us to carry out the Manchester decoding that we will see next.


### FSK Demodulation

```
#Initialisation des donn??es n??cessaires ?? la d??modulation FSK
Fp1 = 35000
Fp2 = 36000
S1 = np.sin(2*np.pi*Fp1*t)                           #G??n??ration de la premi??re porteuse S1
S2 = np.sin(2*np.pi*Fp2*t)                           #G??n??ration de la deuxi??me porteuse S2

Produit1 = S1*FSK
Produit2 = S2*FSK

Res1=[]                                              #Initialisation d'une liste vide
Res2=[]                                              #Initialisation d'une liste vide

i=0                                                  #Initialisation de i ?? 0
for i in range(0,int(N),int(Ns)):                    #Boucle for allant de 0 ?? N par pas de Ns
    Res1.append(np.trapz(Produit1[i:i+int(Ns)]))     #Ajout des r??sultat de l'int??grale du Produit(Porteuse1(S1)*FSK) ?? la liste "Res1" allant de i ?? i+Ns
    Res2.append(np.trapz(Produit2[i:i+int(Ns)]))     #Ajout des r??sultat de l'int??grale du Produit(Porteuse2(S2)*FSK) ?? la liste "Res2" allant de i ?? i+Ns

y=[]                                                 #Initialisation d'une liste vide
for i in range(0,len(Res1)):                         #Boucle for allant de 0 ?? la taille de "Res1"
    if Res1[i]>Res2[i]:                              #Conditionnel if qui v??rifie si la i-??me valeur de Res1 est sup??rieure ?? la i-??me valeur de Res2
        y.append(1)                                  #Ajout de la valeur 1 ?? la liste "y" si la condition est v??rifi??e
    if Res1[i]<Res2[i]:                              #Conditionnel if qui v??rifie si la i-??me valeur de Res1 est inf??rieure ?? la i-??me valeur de Res2
        y.append(0)                                  #Ajout de la valeur 0 ?? la liste "y" si la condition est v??rifi??e

#Affichage de FSK
plt.figure (figsize = (10,6))
plt.plot(t,FSK,'b')
plt.title('FSK')
plt.xlabel('temps (s)')
plt.ylabel('Amplitude')
plt.axis([0,0.009,-1,1])
plt.grid()

#Affichage des valeurs de y
print("Les valeurs de y sont :",y)
```

Just like ASK demodulation, FSK is used to carry out the reverse of this type of modulation, allowing the ASK signal to be transformed into a list of binary values corresponding to the message sent, which only has to be decoded in Manchester. The difference with the ASK demodulation, it is that in this case there, one finds finally a binary message in the form of list instead of a list of approximate values not being binary.

### Manchester decoding

```
#D??codage Manchester
message_demodule = np.array(y)>0                                         #Si >0 renvoie True, sinon renvoie False

message_recu_decode=[]                                                   #Initialisation d'une liste vide
for iii in range(0,len(message_demodule)):                               #Boucle for allant de 0 ?? la valeur de la taille de "message_demodule"
    if message_demodule[iii]==True:                                      #Conditionnel if qui v??rifie si la iii-??me valeur de "message_demodule" est ??gale ?? "True"
        message_recu_decode.extend([int(1)])                             #Ajout de la valeur 1 ?? la liste "message_recu_decode" si la condition est v??rifi??e
    if message_demodule[iii]==False:                                     #Conditionnel if qui v??rifie si la iii-??me valeur de "message_demodule" est ??gale ?? "False"
        message_recu_decode.extend([int(0)])                             #Ajout de la valeur 0 ?? la liste "message_recu_decode" si la condition est v??rifi??e

message_recu_bin=[]                                                      #Initialisation d'une liste vide
for iiii in range(0, len(message_demodule),2):                           #Boucle for allant de 0 ?? la valeur de la taille de "message_demodule" par pas de 2 afin de revenir ?? un bit et non 2 bits par ??tat
    if message_recu_decode[iiii]==1 and message_recu_decode[iiii+1]==0:  #Conditionnel if qui v??rifie si la iiii-??me valeur de "message_recu_decode" est ??gale ?? 1 et si la (iiii+1)-??me valeur de "message_recu_decode" est ??gale ?? 0
        message_recu_bin.extend([str(1)])                                #Ajout d'un caract??re 1 ?? la liste mesage_recu_bin si la condition est v??rifi??e
    if message_recu_decode[iiii]==0 and message_recu_decode[iiii+1]==1:  #Conditionnel if qui v??rifie si la iiii-??me valeur de "message_recu_decode" est ??gale ?? 0 et si la (iiii+1)-??me valeur de "message_recu_decode" est ??gale ?? 1
        message_recu_bin.extend([str(0)])                                #Ajout d'un caract??re 0 ?? la liste mesage_recu_bin si la condition est v??rifi??e
print(message_recu_bin)                                                  #Affichage de la liste de caract??res "message_recu_bin" contenant le message initial envoy?? par l'agent apr??s avoir ??t?? cod?? en CRC
```

Like most of the steps in the reception phase, Manchester decoding is used to perform the reverse of Manchester encoding. Therefore, in our code, we have made sure to convert the message into Boolean and then into binary (initial message of the infiltrated agent with Manchester encoding). In fact, by taking a step of two in the second "for" loop we can divide the list by two and thus return to the initial message, because during the Manchester coding, the bits of the list must be doubled according to the binary code. This code works just as well for the results of ASK demodulation as for FSK demodulation.

### Conversion of the binary list into a character string
 
```
 #conversion liste en chaine de caract??re
message_r??ceptionn??=''.join(message_recu_bin)
print(message_r??ceptionn??)
```

As you can see, we decided to convert the binary list obtained during the Manchester decoding into a character string so that it can be exploited by the CRC decoding and the binary-to-text conversion that follow.

### CRC decoding and error verification

```
#D??finition de la fonction de d??codage
def decodage(data_crc, key):
    length_key = len(key)                                                #Stockage de la taille de la cl??
    data_ajout = data_crc + '0'*(length_key-1)                           #Ajout des '0' en fonction de la taille de la cl??-1
    reste = Division_eucl(data_ajout, key)                               #Stockage du reste de la division euclidienne du message auquel on ajout?? les 0 par la cl??
    return reste                                                         #On retourne la valeur du reste

key = '11010'                                                            #Initialisation de la cl?? (c'est la m??me que dans la phase d'??mission)
check = decodage(message_r??ceptionn??, key)                               #R??cup??ration du reste de la division euclidienne du message r??ceptionn?? par la cl??
print("Le reste de la division apr??s d??codage est ->" +check)            #Affichage du reste de la division apr??s d??codage
temp = "0" * (len(key) - 1)                                              #R??cup??ration du message de base
if check == temp:                                                        #Conditionnel if qui v??rifie si check est ??gal ?? temp
    print("Les donn??es -> "+message_r??ceptionn?? +"<- sont bien re??ues!") #Affichage d'un message de confirmation de la validit?? du message si la condition est v??rifi??e
else:       
    print("Erreur de r??ception")                                         #Affichage d'un message d'erreur si la condition n'est pas v??rifi??e
```

In order to carry out the CRC decoding of the message received by the computer of the agent in charge of listening, we have created a decoding function which allows us to carry out the Euclidean division of the message via the key used during the CRC encoding and to recover the remainder. Indeed, if this remainder is equal to 0, the message has no error. However, if this remainder is different from 0, then there is a reception error. Therefore, we have displayed the outputs of this code above to be able to visualize it better.

### Conversion of the binary message into text via the ASCII table

```
#D??finition de la fonction de conversion du binaire en d??cimale
def BinaryToDecimal(binary):  
    #initialisation des variables
    decimal = 0
    i = 0
    n = 0
    while(binary != 0):                            #Boucle tant que qui s'execute tant que le code binaire est diff??rent de 0
        dec = binary % 10                          #On r??cup??re le reste de la division euclidienne du code binaire par 10
        decimal = decimal + dec * pow(2, i)        #On r??cup??re la d??cimale correspondant au code binaire
        binary = binary//10                        #On effectue la division euclidienne du code binaire par 10 sans r??cup??rer le reste
        i += 1                                     #On incr??mente i de 1
    return (decimal)                               #On retourne la valeur d??cimale du code binaire entr??

#Conversion du message binaire en texte via la table ASCII
Total_texte=''                                     #Initialisation d'une cha??ne de caract??re vide
for i in range(0,len(message_r??ceptionn??),7):      #Boucle for allant de 0 ?? la taille de "message_r??ceptionn??"
    bin_data=int(message_r??ceptionn??[i:i+7])       #Stockage des 7 bits correspondant ?? une lettre
    decimal_data = BinaryToDecimal(bin_data)       #Conversion du code binaire (7 bits) en d??cimal
    Total_texte = Total_texte + chr(decimal_data)  #Conversion de la d??cimale en texte puis ajout du caract??re ?? la liste "Total_teste"
```

During this step of conversion of the binary message into text via the ASCII table, we defined a function "BinaryToDecimal" which takes as argument the binary code of 7 bits corresponding to a character (i.e. to a letter). Thus, the "for" loop allows us to select the 7 bits we want, to convert them into decimal, and then to convert them into text via the chr() function.

### Display of the message

```
#Affichage du message
print(Total_texte)
```

In this step we simply display the result obtained when converting the binary message to text so that the Agent in charge of listening can see the message.

### Acknowledgement of receipt

```
verification = int(check,2)             #Conversion de "check" binaire en d??cimal provenant du d??codage CRC
FeA=44100                               #Initialisation de la fr??quence d'??chantillonnage pour un signal de fr??quence comprise dans le spectre de l'audible par l'oreille humaine
t=np.arange(0,0.2,1/FeA)                #G??n??ration d'un vecteur temps de dur??e 0.2s (ce temps nous parrait suffisant pour un accus?? de r??ception(bip sonore) mais il reste mdifiable)
Accus??_r??ception=np.sin(2*np.pi*5000*t) #G??n??ration d'un signal quelconque de fr??quence 5 000Hz
if verification == 0:                   #Conditionnel if qui v??rifie si le message est bien re??u
    sd.play(Accus??_r??ception, FeA)      #??mission du message sous forme de signal (Jouer le son) si la condition est v??rifi??e, sinon pas d'accus?? de r??ception
```

In this final stage of the code, we carried out an acknowledgement as in the half-duplex links (communications). To realize this code, we initialized a new sampling frequency corresponding to that of a signal whose frequency is in the spectrum of the audible by the human ear. This is because the agent in charge of listening will send an audible beep to the infiltrated agent so that he knows if his message has been correctly received. For this reason, we have created a 5000Hz signal and a condition that allows the acknowledgement to be sent (beep) only if the message does not contain any error on reception (see CRC decoding). Finally, the infiltrated agent will be able to know if it should send its message or not, and thus know if it should communicate or not.

# Conclusion
Finally, if the next agents of the infiltration cell use this code, we can guarantee that a situation like the one experienced by Agent K57 will not happen again.

