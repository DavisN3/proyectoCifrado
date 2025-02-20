# Código de Schrodinger, proyecto S.I.C.R.A
En el presente repositorio encontraran los avances preliminares al desarrollo del proyecto final del área de informática, con la finalidad de dar revisión y avance al planteamiento que se está desarrollando.



# ¿En qué consiste el S.I.C.R.A?
(Siglas para Sistema Integrado de Cifrado Remoto ATEDv1) El proyecto consiste en la implementación de un sistema de cifrado personalizado basado en el cifrado César. Su objetivo es permitir que los mensajes sean cifrados y enviados a través de un chat, de modo que el receptor pueda descifrar el contenido solo si tiene la contraseña, la cual será enviada al ejecutarse el código por correo electrónico, con el agregado de que es una contraseña temporal y cambia cada "num" segundos.

## Extensiones preliminares
Antes de ejecutar el código es necesario instalar las siguiente extensión.
### Pasos:
1. Abrir "Símbolo de sistema".
2. Copiar y pegar las siguiente extensión.

**Extensión 1:**
```python
pip install secure-smtplib
```

# Cifrado ATEDv1

## Inspiracion en Cifrado César

### ¿En qué consiste el cifrado cesar?
El cifrado César consiste en un sistema del estilo sustitución, en el que cada letra del texto original es desplazado por otra letra que se encuentra a un número fijo de posición de la letra en el alfabeto. Ya sea un desplazamiento de 3 en la palabra "Hola", empezando por la "H" siendo reemplazada por la "K", la "o" por la letra "r", la "l" por la "o" y finalmente la "a" por la "d", dando como resultado "Krod".

## Sitema de cifrado implementado en el proyecto:
El sistema creado para el proyecto fue, como se menciona anteriormente, inspirado en el cifrado cesar, pero con la implementacion de unas reglas que amplian sus posibilidades de combinacion, las cuales serian las siguientes:

#### AD_DA: Orden del Alfabeto (se puede usar cualquier letra)
En terminos simples si el alfabeto se tomara como una lista, cada letra tendria un indice, si el indice de la primera es menor al de la segunda, es AD en caso contrario es DA

alpha_base = list("abcdefghijklmnopqrstuvwxyz") #26 indices de 0-25

- AD: orden normal, cualquier letra que en orden alfabetico este antes y otra despues (eo, fg, xy, etc) "a" tomaria indice 0 aqui ejem
- DA: orden inverso cualquier letra que en orden alfabetico este despues y otra anterior a esa misma (oe, gf, xy)  "a" tomaria indice 25 aqui ejem

#### X#:
Desplazamiento basado en cifrado cesar, al guardarse como regla este numero se reemplaza por por una letra con el "indice" de desplazamiento: alpha_base[#desplazamiento]

#### DX#
Sera AD_DA + X# y la posicion dentro del texto, escencialmente son las reglas y en donde se guardan, funcionara como un "insert", esta regla es escencial para facilitar el desencriptado

```python
import random
alpha_base = list("abcdefghijklmnopqrstuvwxyz") #Alfabeto estandar
def cifrado_atedv2(texto):
  desplazamiento = random.randint(1,25) #el desplazamiento sera aleatorio
  orden=random.choice(["AD","DA"])#Aqui se definira el orden del alfabeto a usar
  #Valor aleatorio para AD_DA
  A=(random.randint(0, 23)) #Una letra random del alfabeto
  D=(random.randint(A+1, 25)) #Otra letra random de indice mayor que A
  if orden=="AD":
    alfa=alpha_base
    AD_DA=str(alpha_base[A]) + str(alpha_base[D]) 
  if orden=="DA":
    alfa=list("zyxwvutsrqponmlkjihgfedcba") #Alfabeto inverso en caso de "DA"
    AD_DA=str(alpha_base[D]) + str(alpha_base[A])
  alpha = alfa
  texto_cifrado = [] # Los caracteres se guardaran en una lista de 1 en 1
  for char in texto:  #Se repetira por la longitud de caracteres en el texto
    if char.isalpha():  #Identificar si es una letra del alfabeto
      char_minus = char.lower() # Convertimos la letra a minúscula para simplificar
      indice = alpha.index(char_minus)  #Encontramos el indice de la letra en el alfabeto
      # Desplazamos la letra, usando módulo para que se mantenga dentro del rango (A-Z)
      nuevo_indice = (indice + desplazamiento) % 26
      nuevo_char = alpha[nuevo_indice]
      if char.isupper(): # Si la letra original era mayúscula, la convertimos nuevamente a mayúscula
        nuevo_char = nuevo_char.upper()
      texto_cifrado.append(nuevo_char) #La letra nueva se agregara a la lista como caracter unico
    else:
      texto_cifrado.append(char) #Si no es una letra, lo agregamos tal cual (espacios, signos de puntuación, etc.)
  Xn=alpha_base[desplazamiento]
  pos=random.randint(0, len(list(texto_original))) #Posicion de DX
  DX =(AD_DA+Xn) #DX es el orden y desplazamiento cesar
  texto_cifrado.insert(pos, DX) #DX sera insertado en la lista en una "pos"icion random

  salida=''.join(texto_cifrado) #''join agregara los elementos de la lista "texto_cifrado" a una cadena vacia 
  #transformandola efectivamente en una cadena

  salidas=[salida, DX, pos] # [0] es la cadena cifrada
  return salidas #salidas=[salida, DX, pos] (Guia de elementos)

def des_atedv2(salidad, DX, pos):
  reglas=list(DX) #Se desarman las reglas guardadas
  salida=list(salidad) #Tranformar la cadena cifrada en lista para facilitar 1 modificacion
  #Esta parte definira el orden de alfabeto usado:
  letra1 = alpha_base.index(reglas[0]) 
  letra2 = alpha_base.index(reglas[1]) 
  desplazamiento=alpha_base.index(reglas[2]) #La 3ra letra se transformara en un numero con esto
  #cada letra tiene un indice definido en el alpha_base veremos cual va primero para definir el orden
  if letra1<letra2:
    orden="AD"
  else: orden="DA"

  for p in range(3):#Esto eliminara DX de la cadena (aqui aplica el porque de la conversion)
    salida.pop(pos) #Quitara los caracteres de DX

  salida=''.join(salida) #lo convertimos a cadena de nuevo para reciclar la funcion

  #Consulta de orden y definicion de alfabeto:
  if orden=="AD":
    alfa=alpha_base
  if orden=="DA":
    alfa=list("zyxwvutsrqponmlkjihgfedcba") #Alfabeto inverso en caso de "DA"
  alpha = alfa #Alfabeto definido con el orden
  texto_descifrado = [] # Los caracteres se guardaran en una lista de 1 en 1
  for char in salida:  #Se repetira por la longitud de caracteres en el texto
    if char.isalpha():  #Identificar si es una letra del alfabeto
      char_minus = char.lower() # Convertimos la letra a minúscula para simplificar
      indice = alpha.index(char_minus)  #Encontramos el indice de la letra en el alfabeto
      # Desplazamos la letra, usando módulo para que se mantenga dentro del rango (A-Z)
      nuevo_indice = (indice - desplazamiento) % 26 #Esta vez restandole el desplazamiento
      nuevo_char = alpha[nuevo_indice] #La letra modificada
      if char.isupper(): # Si la letra original era mayúscula, la convertimos nuevamente a mayúscula
        nuevo_char = nuevo_char.upper()
      texto_descifrado.append(nuevo_char) #La letra nueva se agregara a la lista como caracter unico
    else:
      texto_descifrado.append(char) #Si no es una letra, lo agregamos tal cual (espacios, signos de puntuación, etc.)

  salida_f=''.join(texto_descifrado)
  
  return salida_f
  
if __name__=="__main__":
  print("CIFRADO!")
  texto_original = input("Texto a cifrar: ") # Hola Mundo!
  
  texto_cifrado = cifrado_atedv2(texto_original) #La salida sera totalmente aleatorea
  
  print("Texto original:", texto_original)
  print("Texto cifrado:", texto_cifrado[0]) #Solo el primer elemento de la lista de salida sera la palabra cifrada

  print("DESCIFRADO!")
  
  #salidas=[salida, DX, pos] esta es la informacion valioza >:)

  salida=texto_cifrado[0]
  DX=texto_cifrado[1]
  pos=texto_cifrado[2]
  
  texto_desencriptado = des_atedv2(salida, DX, pos) #La salida sera totalmente aleatorea
  
  print("Texto cifrado:", texto_cifrado[0]) 
  print("Texto descifrado:", texto_desencriptado)
```
**Importante:** *Para mejor entendimiento de Salidas consulte el nootebook adjunto arriba*

Si quiere probar independientemente alguna de las 2 funciones y/o ver versiones tempranas del codigo entre a: https://github.com/Felip-UN/ATEDx1

#  Automatización de mensajes (contraseña).
El código tiene como funcionalidad envíar un correo electrónico de manera periódica (automatizada) de manera alaeatoria generada cada vez que se ejecuta el código. Dicho proceso se ejecuta en un bucle infinito, el cual el mensaje cada que pase "intervalo" segundos se genera un nuevo número aleatorio y se vuelve a enviar al correo dicha contraseña. Esto gracias principalmente a la librería "email.mime" que permite lograr este tipo de programas.

```python
import smtplib
import random
import time
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart

#Función para enviar un correo a través de Gmail con esos datos.
def enviar_correo_gmail(destinatario, asunto, mensaje, remitente, contraseña):
    #Conexión al servidor SMTP de Gmail (587 es el puerto para ejecutar el código)
    servidor = smtplib.SMTP('smtp.gmail.com', 587)
    #Establece una conexión con el servidor.
    servidor.starttls()
    # Inicia sesión al servidor usando los datos proporcionados en la casilla de remitente y contraseña.
    servidor.login(remitente, contraseña)
    
		#Crea el mensaje de correo
    correo = MIMEMultipart()
    correo['From'] = remitente
    correo['To'] = destinatario
    correo['Subject'] = asunto
    correo.attach(MIMEText(mensaje, 'plain'))
    #Envía el mensaje
    servidor.send_message(correo)
    #Finaliza su conexión con el servidor.
    servidor.quit()

if __name__ == "__main__":
    #Definen diferentes variables
    remitente = ""
    contraseña = ""
    destinatario = ""
    asunto = "Contraseña"
    
		#Intervalo en segundos para enviar el código.
    intervalo = 500
    #Bucle para enviar correos periodicamente de manera infinita.
    while True:
        #Genera un número aleatorio de 4 digitos, el cual va a ser nuestra contraseña.
        num = random.randint(0, 9999)
        #Crea el mensaje con la contraseña (Rellena para que si, por ejemplo es un número de 3 cifras ponga un 0 antes.)
        mensaje = f"Tu contraseña es: {num:04d}"
        #Llama a nuestra variable para enviar correos
        enviar_correo_gmail(destinatario, asunto, mensaje, remitente, contraseña)
        #Esperamos el intervalo (intervalo) para que se vuelva a enviar otro correo.
        time.sleep(intervalo)
```

# Combinación de la contraseña y el proceso de automatización de la contraseña.

EL prososito del siguiente programa es poder probar salidas de correo con una version temprana del cifrado (hay errores de indentado en una funcion definida por problemas de copiado desde otro repositorio)
```python
import smtplib
import random
import time
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart

alpha_base = list("abcdefghijklmnopqrstuvwxyz")

def enviar_correo_gmail(destinatario, asunto, mensaje, remitente, contraseña):
    servidor = smtplib.SMTP('smtp.gmail.com', 587)
    servidor.starttls()
    servidor.login(remitente, contraseña)

    correo = MIMEMultipart()
    correo['From'] = remitente
    correo['To'] = destinatario
    correo['Subject'] = asunto
    correo.attach(MIMEText(mensaje, 'plain'))

    servidor.send_message(correo)
    servidor.quit()

def cifrado_cesar(texto, clave, alfa):
    texto_cifrado = []
    for char in texto:
        if char.isalpha(): 
            char_minus = char.lower()
            indice = alfa.index(char_minus)
            nuevo_indice = (indice + clave) % 26 
            nuevo_char = alfa[nuevo_indice]
            if char.isupper(): 
                nuevo_char = nuevo_char.upper()
            texto_cifrado.append(nuevo_char)
        else:
            texto_cifrado.append(char)
    return texto_cifrado

def cifrado1(num):
    while True:
        texto_original = input("Texto a cifrar: ")
        if texto_original.lower() == "salir":
            print ("chau bro")
            break
        desplazamiento = random.randint(1, 25)
        Xn = alpha_base[desplazamiento]
        orden = random.choice(["AD", "DA"])
        pos = random.randint(0, len(texto_original))
        A = random.randint(0, 23)
        D = random.randint(A + 1, 25)
        if orden == "AD":
            alfa = alpha_base
            AD_DA = str(alpha_base[A]) + str(alpha_base[D])
        else:
            alfa = list("zyxwvutsrqponmlkjihgfedcba")
            AD_DA = str(alpha_base[D]) + str(alpha_base[A])
            texto_cifrado = cifrado_cesar(texto_original, desplazamiento, alfa)
            DX = AD_DA + Xn
            texto_cifrado.insert(pos, DX)
            salida = ''.join(texto_cifrado)
            print(f"Texto cifrado: {salida}")
            x = input("Ingrese la contraseña: ")
            if str(x) == str(num):
                print(f"Contraseña correcta. Texto original: {texto_original}")
            else:
                print("Contraseña incorrecta.")

if __name__ == "__main__":
    remitente = ""
    contraseña = ""
    destinatario = ""
    asunto = "Contraseña"

    intervalo = 500 # en seg
    while True:
        num = random.randint(0, 9999)
        mensaje = f"Tu contraseña es: {num:04d}"
        enviar_correo_gmail(destinatario, asunto, mensaje, remitente, contraseña)
        print ("Escriba 'salir' si desea salir del programa.")
        cifrado1(num)
        time.sleep(intervalo)
```
## Servidores
Un servidor host es un sistema informático que proporciona datos a otros dispositivos llamados clientes dentro de una red. Su función principal es almacenar, procesar y gestionar la comunicación entre dispositivos permitiendo la interacción, por ejemplo, con un microcontrolador.
¿Cómo funcionan?
Cada servidor host tiene una dirección IP única que lo identifica dentro de la red y utiliza protocolos de comunicación (como HTTP, MQTT, TCP/IP) para recibir y procesar solicitudes (datos) de los clientes.

### Fuentes de consulta
Como extra se agrega que una de las principales fuentes de consulta fue [stock overflow](https://stackoverflow.com) en especial para lo que fue la biblioteca socket, [w3](https://www.w3schools.com) para la biblioteca **random** y ademas del repositorio de la clase 13 para guiarnos con algunas funciones [Github](https://github.com/fegonzalez7/pdc_unal_clase13)
