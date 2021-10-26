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
import numpy as np                   #Ce module permet d’effectuer des calculs numériques avec Python
import matplotlib.pyplot as plt      #Ce module permet de créer et personnaliser des graphiques
import sounddevice as sd             #Ce module permet de jouer un son
```
### Undercover Agent writing the message
Then, the pad will ask the undercover agent to enter his message if he has one. Then, if he write a message the pad will store it in the Message variable.
```
#Ecriture du message de l'Agent sous forme de texte
Message = str(input('Entrez le message à envoyer : '))
```

### Conversion text message to binary sequence
If we want the message to be in a binary form we need to convert it by using the ASCII table.
```
total_binary=''                         #Création d'une chaîne de caractère vide "total_binary" qui va contenir le message binaire complet
for i in range(len(Message)):           #Création d'une boucle for qui se répètera jusqu'à atteindre la valeur de la taille du message en partant de 0
    binary=''                           #Création d'une chaîne de caractère vide "binary" qui va stocker les 7 bits correspondant à chaque lettre du message
    string_ord = ord(Message[i])        #Renvoie le décimal correspondant à chaque caratère de la chaîne de caractère
    while string_ord>0:                 #Boucle tant que se répétant tant que la décimale du caractère est supérieure à 0
        x = string_ord % 2              #Stockage du reste de la division euclidienne de la décimale par 2
        string_ord = string_ord // 2    #Stockage du résultat de la division euclidienne de la décimale par 2 sans le reste.
        binary = str(x) + str(binary)   #Stockage de chaques reste correcpondants à un bit (0 ou 1)
    total_binary += binary              #Stockage et ajout du code binaire (7 bits) correspondant à chacune des lettres du message

print(total_binary)                     #Affichage du message binaire complet
```
The conversion of the text message into binary is an important step allowing us to transform the text written by the Agent into a binary character string. In order to realize this step, we used a for loop and a while loop. The for loop allows us to transform the message into decimal for each letter using the ord() function. Then, the while loop allows us to convert the decimal of each letter into a binary code of 7bits thanks to the modulo 2 (that is to say the remainder of the euclidian division of the decimal by 2). Finally, we add all the binary codes of each letter in a string and we find the final binary code representing the sent message.

### CRC encryption
CRC (Cyclic Redundancy Check) is a checksum algorithm to detect inconsistency of data, e.g. bit errors during data transmission. A checksum, calculated by CRC, is attached to the data to help the receiver to detect such errors.\
CRC is based on division. The actual input data is interpreted as one long binary bit stream (divident) which is divided by another fixed binary number (divisor). The remainder of this division is the checksum value.\
_cf. http://www.sunshine2k.de/articles/coding/crc/understanding_crc.html_
```
#Définition de la fonction XOR utilisée pour la division
def xor(a,b):
    resultat = []                                     #Initialisation du résultat sous forme d'une liste vide
    for i in range(1,len(b)):                         #Création d'une boucle pour appliquer la fonction XOR (si les bits sont les même, alors XOR vaut 0, sinon XOR vaut 1)
        if a[i]== b[i]:                               #Création d'un conditionnel if qui va comparer 2 bits et appliquer la fonction XOR
            resultat.append('0')                      #Ajout de le caractère '0' à la liste resultat
        else:
            resultat.append('1')                      #Ajout de le caractère '1' à la liste resultat
    return ''.join(resultat)                          #On retourne les données de la fonction XOR sous forme d'une chaîne de caractères sans espaces

#Définition de la fonction division euclidienne
def Division_eucl(divident,diviseur):
    val = len(diviseur)                               #Stockage de la taille du diviseur dans la variable "val"
    tmp = divident[0 : val]                           #Stockage des val-premières données du divident
    while val<len(divident):                          #Création boucle while qui va s'executer tant que val est inférieur à la taille du divident
        if tmp[0]=='1':                               #Création conditionnel if qui va comparer le caractère '1' à la première valeur du divident
            tmp = xor(diviseur, tmp)+divident[val]    #Stockage du résultat de l'addition de la val-ième valeur du divident et de xor(diviseur,tmp)
        else:
            tmp = xor('0'*val, tmp)+divident[val]     #Stockage du résultat de l'addition de la val-ième valeur du divident et de xor('0'*val, tmp)
        val+=1                                        #Incrémentation de la variable "val"
    if tmp[0]=='1':                                   #Création d'un conditionnel if qui va comparer le caractère '1' à la première valeur du divident en dehor de la boucle while 
        tmp = xor(divisor, tmp)                       #Stockage du résultat de xor(divisor,tmp)
    else:
        tmp = xor('0'*val, tmp)                       #Stockage du résultat de xor('0'*val, tmp)
    check = tmp                                       #Stockage du résultat final de tmp dans la variable "check"
    return check                                      #On retourne la valeur de check

#Définition de la fonction d'encodage CRC (total_binary + 'N-bits 0', G (la clé/polynom générateur))
def encodage(data, key):
    length_key = len(key)                             #Stockage de la taille de la clé dans une variable
    data_ajout = data + '0'*(length_key-1)            #Ajout des (length_key-1)'0' correspondant au nombre de '0' par rapport à la taille de la clé-1
    reste = Division_eucl(data_ajout, key)            #Stockage du reste de la division euclidienne des données par la clé
    data_encode = data + reste                        #Stockage du message binaire à envoyer
    return data_encode                                #On retourne le message binaire

#Fonction Main
data = total_binary                                   #Ajout des 0 correspondant à la taille de la clé
key = '11010'                                         #Initialisation de la clé
print("Les données à envoyer sont :", data)           #Affichage des données à envoyer
print("La clé CRC est:", key)                         #Affichage de la clé

data_crc = encodage(data,key)                         #Encodage CRC du message avec la clé
print("Les données encodées avec la clé key sont :", data_crc) #Affichage du message encodé avec la clé
```
In order to allow the Agent in charge of listening to check if the received message does not contain any error, we have implemented a CRC encoding at transmission. Indeed, to carry out this code, we defined several functions such as the function "xor" (allowing to allot a binary code corresponding to the action of a xor gate), "Division_eucl" (allowing to carry out a Euclidean division) and "encoding" (allowing to encode the message in CRC via the function "Division_eucl"). Finally, we find ourselves with the display of the data to be sent, the CRC key and the data encoded with the key which represent the message to be processed throughout the code until the CRC verification.

### Manchester encoding
In telecommunication and data storage, Manchester code is a line code in which the encoding of each data bit is either low then high, or high then low, for equal time.

<p align="center">
  <img width="600" height="400" src="https://user-images.githubusercontent.com/93186642/138866973-82bcbd98-ec14-4b85-9db2-4d9c0d928ea8.png">
</p>

```
message_bool = []                          #Initialisation d'une liste vide
for i in range(0,len(data_crc),1):         #Boucle for allant de 0 à la valeur de la taille du message codé en CRC par pas de 1
    if data_crc[i]=='1':                   #Conditionnel if qui compare le caractère '1' à la i-ème valeur du message codé en CRC
        message_bool.append(True)          #Ajout d'un booléen True à la liste "message_bool" si condition vérifiée
    if data_crc[i]=='0':                   #Conditionnel if qui compare le caractère '0' à la i-ème valeur du message codé 
        message_bool.append(False)         #Ajout d'un booléen False à la liste "message_bool" si condition vérifiée
#Codage Manchester du message
message_code=[]                            #Initialisation d'une liste vide
for ii in range(0,len(message_bool)):      #Boucle for allant de 0 à la valeur de la taille de "message_bool" par pas de 1
    if message_bool[ii]==True:             #Conditionnel if qui compare le booléen "True" à la ii-ème donnée de la liste "message_bool"
        message_code.extend([int(1)])      #Ajout d'un entier 1 dans la liste "message_code" si la condition est vérifiée
        message_code.extend([int(0)])      #Ajout d'un entier 0 à la suite du 1 dans la liste "message_code" si la condition est vérifiée
    if message_bool[ii]==False:            #Conditionnel if qui compare le booléen "False" à la ii-ème donnée de la liste "message_bool"
        message_code.extend([int(0)])      #Ajout d'un entier 0 dans la liste "message_code" si la condition est vérifiée
        message_code.extend([int(1)])      #Ajout d'un entier 1 à la suite du 0 dans la liste "message_code" si la condition est vérifiée
print(message_code)                        #Affichage du message codé en Manchester
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
Fe = 88200                                 #Initialisation de la fréquence d'échantillonnage à 88 200Hz (soit le double de 44 100Hz)
Fp = 35000                                 #Initialisation de la fréquence de la porteuse à 35 000Hz (Pour que le son sot inaudible)
baud = 600                                 #Initialisation du débit souhaité sur le canal de transmission à 600bit/s
Nbits = len(M)                             #Initialisation du nombre de bits initial
Ns = Fe/baud                               #Initialisation du nombre de symboles par bit (Fréquence d'échantillonnage/débit binaire)
N = Nbits*Ns                               #Initialisation du nombre de bits total à moduler (Nombre de symbole par bits*Nombre de bits)

M_duplique=np.repeat(M,Ns)                 #On génère le message binaire dupliqué

t = np.arange (0.0,N)/Fe                   #On génère le vecteur temps

Ap=1
Porteuse = Ap*np.sin(2*np.pi*Fp*t)         #On génère la porteuse P(t)

ASK = Porteuse*M_duplique                  #On réalise la modulation en amplitude (ASK)

#Affichage Message binaire
plt.figure (figsize = (10,6))
plt.plot(t,M_duplique,'Black')
plt.title('Message M(t)')
plt.xlabel('temps (s)')
plt.ylabel('Amplitude')
plt.grid()

#Affichage du signal modulé
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
Fe = 88200                                 #Initialisation de la fréquence d'échantillonnage à 88 200Hz (soit le double de 44 100Hz)
Fp = 35000                                 #Initialisation de la fréquence de la porteuse à 35 000Hz (Pour que le son sot inaudible)
baud = 600                                 #Initialisation du débit souhaité sur le canal de transmission à 600bit/s
Nbits = len(M)                             #Initialisation du nombre de bits initial
Ns = Fe/baud                               #Initialisation du nombre de symboles par bit (Fréquence d'échantillonnage/débit binaire)
N = Nbits*Ns                               #Initialisation du nombre de bits total à moduler (Nombre de symbole par bits*Nombre de bits)

M_duplique = np.repeat(M,Ns)                                                  # On génère le message binaire dupliqué

t = np.arange (0.0,N)/Fe                                                      #On génère le vecteur temps

# Génération des 2 porteuse P1 pour le bit 1 et P2 pour le bit 0
A1 =  1                                        
A2 =  1                  
fp1 =  35000                
fp2 =  36000 
P1 =  A1*np.sin(2*np.pi*fp1*t)                                               
P2 =  A2*np.sin(2*np.pi*fp2*t)

FSK=[P1[i] if M_duplique[i]==1 else P2[i] for i in range(len(M_duplique))]    #Dans une boucle for allant de 0 à la taille du message binaire dupliqué, on applique la valeur de p1[i] à FSK[i] si la i-ème valeur du message binaire dupliqué est égale à 1 sinon on lui applique la valeur de p2[i]

#Affichage du message
plt.figure (figsize = (10,6))
plt.plot(t,M_duplique,'Black')
plt.title('Message M(t)')
plt.xlabel('temps (s)')
plt.ylabel('Amplitude')
plt.grid()

#Affichage du signal modulé
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
#Émission du message sous forme de signal (Jouer le son)
sd.play(ASK, Fe)
```

The transmission of the message in the form of sound is a fundamental step of the code because it allows the infiltrated Agent to send his message in the form of sound through the microphone of the conference room so that it is then received by the computer of the Agent in charge of listening.

## Receiving phase
### ASK demodulation of the received signal
```
#Initialisation des données nécessaires à la démodulation ASK
Fe = 88200
Fp = 35000
baud = 600
N = len(ASK)
Ns = Fe/baud

t = np.arange (0.0,N)/Fe                                       #Génération du vecteur temps

Porteuse=np.sin(2*np.pi*Fp*t)                                  #Génération de la porteuse
Produit=ASK*Porteuse

#Intégration
y=[]                                                           #Initialisation d'une liste vide
for i in range(0,int(N),int(Ns)):                              #Boucle for allant de 0 à N par pas de NS
    y.append(np.trapz(Produit[i:i+int(Ns)],t[i:i+int(Ns)]))    #Ajout du résultat de l'intégrale des données du Produit de l'ASK avec la Porteuse en fonction du temps allant de i à i+Ns

#Affichage de l'ASK
plt.figure (figsize = (10,6))
plt.plot(t,ASK,'b')
plt.title('ASK')
plt.xlabel('temps (s)')
plt.ylabel('Amplitude')
plt.grid()

#Affichage des données de y
print("Les valeurs de y sont :",y)
```

The ASK demodulation is the first stage of the reception phase. Indeed, this one allows via the code above to carry out the reverse of the ASK modulation carried out at the time of the emission of the message and thus to find values in a variable, "y" in our case, being the results of the integral of the product of the ASK and the carrier. These values will then allow us to carry out the Manchester decoding that we will see next.


### FSK Demodulation

```
#Initialisation des données nécessaires à la démodulation FSK
Fp1 = 35000
Fp2 = 36000
S1 = np.sin(2*np.pi*Fp1*t)                           #Génération de la première porteuse S1
S2 = np.sin(2*np.pi*Fp2*t)                           #Génération de la deuxième porteuse S2

Produit1 = S1*FSK
Produit2 = S2*FSK

Res1=[]                                              #Initialisation d'une liste vide
Res2=[]                                              #Initialisation d'une liste vide

i=0                                                  #Initialisation de i à 0
for i in range(0,int(N),int(Ns)):                    #Boucle for allant de 0 à N par pas de Ns
    Res1.append(np.trapz(Produit1[i:i+int(Ns)]))     #Ajout des résultat de l'intégrale du Produit(Porteuse1(S1)*FSK) à la liste "Res1" allant de i à i+Ns
    Res2.append(np.trapz(Produit2[i:i+int(Ns)]))     #Ajout des résultat de l'intégrale du Produit(Porteuse2(S2)*FSK) à la liste "Res2" allant de i à i+Ns

y=[]                                                 #Initialisation d'une liste vide
for i in range(0,len(Res1)):                         #Boucle for allant de 0 à la taille de "Res1"
    if Res1[i]>Res2[i]:                              #Conditionnel if qui vérifie si la i-ème valeur de Res1 est supérieure à la i-ème valeur de Res2
        y.append(1)                                  #Ajout de la valeur 1 à la liste "y" si la condition est vérifiée
    if Res1[i]<Res2[i]:                              #Conditionnel if qui vérifie si la i-ème valeur de Res1 est inférieure à la i-ème valeur de Res2
        y.append(0)                                  #Ajout de la valeur 0 à la liste "y" si la condition est vérifiée

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
#Décodage Manchester
message_demodule = np.array(y)>0                                         #Si >0 renvoie True, sinon renvoie False

message_recu_decode=[]                                                   #Initialisation d'une liste vide
for iii in range(0,len(message_demodule)):                               #Boucle for allant de 0 à la valeur de la taille de "message_demodule"
    if message_demodule[iii]==True:                                      #Conditionnel if qui vérifie si la iii-ème valeur de "message_demodule" est égale à "True"
        message_recu_decode.extend([int(1)])                             #Ajout de la valeur 1 à la liste "message_recu_decode" si la condition est vérifiée
    if message_demodule[iii]==False:                                     #Conditionnel if qui vérifie si la iii-ème valeur de "message_demodule" est égale à "False"
        message_recu_decode.extend([int(0)])                             #Ajout de la valeur 0 à la liste "message_recu_decode" si la condition est vérifiée

message_recu_bin=[]                                                      #Initialisation d'une liste vide
for iiii in range(0, len(message_demodule),2):                           #Boucle for allant de 0 à la valeur de la taille de "message_demodule" par pas de 2 afin de revenir à un bit et non 2 bits par état
    if message_recu_decode[iiii]==1 and message_recu_decode[iiii+1]==0:  #Conditionnel if qui vérifie si la iiii-ème valeur de "message_recu_decode" est égale à 1 et si la (iiii+1)-ème valeur de "message_recu_decode" est égale à 0
        message_recu_bin.extend([str(1)])                                #Ajout d'un caractère 1 à la liste mesage_recu_bin si la condition est vérifiée
    if message_recu_decode[iiii]==0 and message_recu_decode[iiii+1]==1:  #Conditionnel if qui vérifie si la iiii-ème valeur de "message_recu_decode" est égale à 0 et si la (iiii+1)-ème valeur de "message_recu_decode" est égale à 1
        message_recu_bin.extend([str(0)])                                #Ajout d'un caractère 0 à la liste mesage_recu_bin si la condition est vérifiée
print(message_recu_bin)                                                  #Affichage de la liste de caractères "message_recu_bin" contenant le message initial envoyé par l'agent après avoir été codé en CRC
```

Like most of the steps in the reception phase, Manchester decoding is used to perform the reverse of Manchester encoding. Therefore, in our code, we have made sure to convert the message into Boolean and then into binary (initial message of the infiltrated agent with Manchester encoding). In fact, by taking a step of two in the second "for" loop we can divide the list by two and thus return to the initial message, because during the Manchester coding, the bits of the list must be doubled according to the binary code. This code works just as well for the results of ASK demodulation as for FSK demodulation.

### Conversion of the binary list into a character string
 
```
 #conversion liste en chaine de caractère
message_réceptionné=''.join(message_recu_bin)
print(message_réceptionné)
```

As you can see, we decided to convert the binary list obtained during the Manchester decoding into a character string so that it can be exploited by the CRC decoding and the binary-to-text conversion that follow.

### CRC decoding and error verification

```
#Définition de la fonction de décodage
def decodage(data_crc, key):
    length_key = len(key)                                                #Stockage de la taille de la clé
    data_ajout = data_crc + '0'*(length_key-1)                           #Ajout des '0' en fonction de la taille de la clé-1
    reste = Division_eucl(data_ajout, key)                               #Stockage du reste de la division euclidienne du message auquel on ajouté les 0 par la clé
    return reste                                                         #On retourne la valeur du reste

key = '11010'                                                            #Initialisation de la clé (c'est la même que dans la phase d'émission)
check = decodage(message_réceptionné, key)                               #Récupération du reste de la division euclidienne du message réceptionné par la clé
print("Le reste de la division après décodage est ->" +check)            #Affichage du reste de la division après décodage
temp = "0" * (len(key) - 1)                                              #Récupération du message de base
if check == temp:                                                        #Conditionnel if qui vérifie si check est égal à temp
    print("Les données -> "+message_réceptionné +"<- sont bien reçues!") #Affichage d'un message de confirmation de la validité du message si la condition est vérifiée
else:       
    print("Erreur de réception")                                         #Affichage d'un message d'erreur si la condition n'est pas vérifiée
```

In order to carry out the CRC decoding of the message received by the computer of the agent in charge of listening, we have created a decoding function which allows us to carry out the Euclidean division of the message via the key used during the CRC encoding and to recover the remainder. Indeed, if this remainder is equal to 0, the message has no error. However, if this remainder is different from 0, then there is a reception error. Therefore, we have displayed the outputs of this code above to be able to visualize it better.

### Conversion of the binary message into text via the ASCII table

```
#Définition de la fonction de conversion du binaire en décimale
def BinaryToDecimal(binary):  
    #initialisation des variables
    decimal = 0
    i = 0
    n = 0
    while(binary != 0):                            #Boucle tant que qui s'execute tant que le code binaire est différent de 0
        dec = binary % 10                          #On récupère le reste de la division euclidienne du code binaire par 10
        decimal = decimal + dec * pow(2, i)        #On récupère la décimale correspondant au code binaire
        binary = binary//10                        #On effectue la division euclidienne du code binaire par 10 sans récupérer le reste
        i += 1                                     #On incrémente i de 1
    return (decimal)                               #On retourne la valeur décimale du code binaire entré

#Conversion du message binaire en texte via la table ASCII
Total_texte=''                                     #Initialisation d'une chaîne de caractère vide
for i in range(0,len(message_réceptionné),7):      #Boucle for allant de 0 à la taille de "message_réceptionné"
    bin_data=int(message_réceptionné[i:i+7])       #Stockage des 7 bits correspondant à une lettre
    decimal_data = BinaryToDecimal(bin_data)       #Conversion du code binaire (7 bits) en décimal
    Total_texte = Total_texte + chr(decimal_data)  #Conversion de la décimale en texte puis ajout du caractère à la liste "Total_teste"
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
verification = int(check,2)             #Conversion de "check" binaire en décimal provenant du décodage CRC
FeA=44100                               #Initialisation de la fréquence d'échantillonnage pour un signal de fréquence comprise dans le spectre de l'audible par l'oreille humaine
t=np.arange(0,0.2,1/FeA)                #Génération d'un vecteur temps de durée 0.2s (ce temps nous parrait suffisant pour un accusé de réception(bip sonore) mais il reste mdifiable)
Accusé_réception=np.sin(2*np.pi*5000*t) #Génération d'un signal quelconque de fréquence 5 000Hz
if verification == 0:                   #Conditionnel if qui vérifie si le message est bien reçu
    sd.play(Accusé_réception, FeA)      #Émission du message sous forme de signal (Jouer le son) si la condition est vérifiée, sinon pas d'accusé de réception
```

In this final stage of the code, we carried out an acknowledgement as in the half-duplex links (communications). To realize this code, we initialized a new sampling frequency corresponding to that of a signal whose frequency is in the spectrum of the audible by the human ear. This is because the agent in charge of listening will send an audible beep to the infiltrated agent so that he knows if his message has been correctly received. For this reason, we have created a 5000Hz signal and a condition that allows the acknowledgement to be sent (beep) only if the message does not contain any error on reception (see CRC decoding). Finally, the infiltrated agent will be able to know if it should send its message or not, and thus know if it should communicate or not.

# Conclusion
Finally, if the next agents of the infiltration cell use this code, we can guarantee that a situation like the one experienced by Agent K57 will not happen again.

