# Código de Schrodinger - Proyecto S.I.C.R.A
En este repositorio se encuentra la información sintetizada y el desarrollo funcional del proyecto final para la materia de Programación Básica.

## Índice
- [Introducción](#introducción)
- [Servidor / Cliente](#servidor--cliente)
  - [Comunicación cliente / servidor.](#comunicación-cliente--servidor)
  - [Servidor](#servidor)
    - [Versión inicial Chat Cliente - Conexión servidor](#versión-inicial-chat-cliente---conexión-servidor)
  - [Cliente](#cliente)
    - [Versión inicial Chat Cliente - Conexión cliente](#versión-inicial-chat-cliente---conexión-cliente)
- [Cifrado ATEDv3](#cifrado-atedv3)
  - [¿En qué consiste el cifrado cesar?](#en-qué-consiste-el-cifrado-cesar)
  - [Sitema de cifrado implementado en el proyecto](#sistema-de-cifrado-implementado-en-el-proyecto)
   
## Introducción
### ¿En qué consiste el S.I.C.R.A?
S.I.C.R.A es un sistema de cifrado personalizado basado en el cifrado César, diseñado de tal manera para proteger mensajes en un chat. Utiliza socket para conexiones "TCP" y "Threading" para la concurrencia, permitiendo el envío y recepción de mensajes en tiempo real. La seguridad se refuerza con un desplazamiento aleatorio y reglas de cifrado que permiten su descifrado en el servidor.

## Servidor / Cliente
### Comunicación cliente / servidor.
El sistema S.I.C.R.A se basa en una arquitectura de comunicación tipo "Socket" para la comunicación entre el cliente y el servidor usando el protocolo "TCP". El servidor actua como intermediario para que se realice la conexión de múltiples clientes y asegurando ue la transmisión de mensajes (cifrados y desifrados) se realice de manera efectiva.

### Servidor:
- Se ejecuta en un localhost (o en una IP de un servidor), el cual escucha las peticiones que se realizan en un puerto determinado.
- Emplea el uso de "Socket" para aceptar conexiones y "Threading" para gestionar de manera efectiva las peticiones que se realizan.
- Recibe los mensajes cifrados, los envía y a la vez los descodifica.

#### Versión inicial Chat Cliente - Conexión servidor.
```python
import socket   
import threading

# Alfabeto estándar en minúsculas
base_alfabeto = list("abcdefghijklmnopqrstuvwxyz")  

# Dirección y puerto del servidor
host = '127.0.0.1'  # Dirección local (localhost)
puerto = 55555  

# Creación del socket del servidor
servidor = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Vinculación del servidor a la dirección y puerto
servidor.bind((host, puerto))
servidor.listen()

print(f"Servidor en ejecución en {host}:{puerto}")

# Listas para almacenar clientes conectados y sus nombres de usuario
clientes = []
usuarios = []

# Función para enviar mensajes a todos los clientes excepto al remitente
def transmitir(mensaje, _cliente):
    for cliente in clientes:
        if cliente != _cliente:
            cliente.send(mensaje)

# Función para manejar los mensajes entrantes de un cliente
def manejar_mensajes(cliente):
    while True:
        try:
            # Recibir mensaje del cliente
            mensaje = cliente.recv(1024)
            # Enviar el mensaje a los demás clientes
            transmitir(mensaje, cliente)
        except:
            # Si hay un error (cliente desconectado), eliminarlo de las listas
            indice = clientes.index(cliente)
            usuario = usuarios[indice]
            transmitir(f"Servidor: {usuario} se ha desconectado".encode('utf-8'), cliente)
            clientes.remove(cliente)
            usuarios.remove(usuario)
            cliente.close()
            break

# Función para aceptar y gestionar nuevas conexiones de clientes
def recibir_conexiones():
    while True:
        cliente, direccion = servidor.accept()

        # Solicitar nombre de usuario al cliente
        cliente.send("@username".encode("utf-8"))
        usuario = cliente.recv(1024).decode('utf-8')

        # Almacenar cliente y nombre de usuario
        clientes.append(cliente)
        usuarios.append(usuario)

        print(f"{usuario} se ha conectado desde {str(direccion)}")

        # Notificar a los demás clientes que un nuevo usuario se ha unido
        mensaje = f"Servidor: {usuario} se ha unido al chat!".encode("utf-8")
        transmitir(mensaje, cliente)
        cliente.send("Conectado al servidor".encode("utf-8"))

        # Iniciar un hilo para manejar los mensajes de este cliente
        hilo = threading.Thread(target=manejar_mensajes, args=(cliente,))
        hilo.start()

# Iniciar la función para aceptar conexiones
recibir_conexiones()

```

### Cliente
- Se conecta al servidor usando "TCP".
- Cifra los mensajes antes de enviar algún mensaje.
- Maneja la codificación "UTF-8" para la transmisión de información con el servidor.

#### Versión inicial Chat Cliente - Conexión cliente.
```python
import socket
import threading
import builtins

# Solicita el nombre de usuario mediante la función original input
usuario = builtins.input("Ingrese su nombre de usuario: ")

host = '127.0.0.1'
puerto = 55555

# Configuración del socket del cliente
cliente = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
cliente.connect((host, puerto))

# Función para recibir mensajes enviados por el servidor
def recibir_mensajes():
    while True:
        try:
            mensaje = cliente.recv(1024).decode('utf-8')
            if mensaje == "@username":
                cliente.send(usuario.encode('utf-8'))
            else:
                # Imprime el mensaje recibido con saltos de línea para mayor legibilidad
                print("\n" + mensaje + "\n")
        except Exception as error:
            print("\nError:", error)
            cliente.close()
            break


# Función para enviar mensajes al servidor
def escribir_mensajes():
    while True:
        mensaje = f"{usuario}: {input('')}"
        cliente.send(mensaje.encode('utf-8'))

# Inicia los hilos para recibir y enviar mensajes
threading.Thread(target=recibir_mensajes).start()
threading.Thread(target=escribir_mensajes).start()
```

## Cifrado ATEDv3
### ¿En qué consiste el cifrado cesar?
El cifrado César consiste en un sistema del estilo sustitución, en el que cada letra del texto original es desplazado por otra letra que se encuentra a un número fijo de posición de la letra en el alfabeto. Ya sea un desplazamiento de 3 en la palabra "Hola", empezando por la "H" siendo reemplazada por la "K", la "o" por la letra "r", la "l" por la "o" y finalmente la "a" por la "d", dando como resultado "Krod".

### Sitema de cifrado implementado en el proyecto
El sistema creado para el proyecto fue, como se menciona anteriormente, inspirado en el cifrado César, pero con la implementacion de unas reglas que amplian sus posibilidades de combinacion, las cuales serian las siguientes:

#### Sistema de reglas:
- AD_DA: Orden del Alfabeto (se puede usar cualquier letra)
En terminos simples si el alfabeto se tomara como una lista, cada letra tendria un indice, si el indice de la primera es menor al de la segunda, es AD en caso contrario es DA

alpha_base = list("abcdefghijklmnopqrstuvwxyz") #26 indices de 0-25

- AD: orden normal, cualquier letra que en orden alfabetico este antes y otra despues (eo, fg, xy, etc) "a" tomaria indice 0 aqui ejem
- DA: orden inverso cualquier letra que en orden alfabetico este despues y otra anterior a esa misma (oe, gf, xy)  "a" tomaria indice 25 aqui ejem

- X#:
Desplazamiento basado en cifrado cesar, al guardarse como regla este numero se reemplaza por por una letra con el "indice" de desplazamiento: alpha_base[#desplazamiento]

- DX#
Sera AD_DA + X# y la posicion dentro del texto, escencialmente son las reglas y en donde se guardan, funcionara como un "insert", esta regla es escencial para facilitar el desencriptado.

#### Sistema de encriptado
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
  print("\nReglas que se guardaron: ",DX,) #Verificador de salida
  #^^#Opcional para verificar que las salidas son correctas
  salidas=[salida, pos] # [0] es la cadena cifrada
  return salidas #salidas=[Texto cifrado, posicion de las reglas] (Guia de elementos)

if __name__=="__main__":
  texto_original = input("Texto a cifrar: ")
  #Mensajes de prueba:
  #Hola Buenos dias como estan gente aqui cifrando un mensaje con un viejo amigo, FELIZ LUNES
  #Hola buenas!, esto funciona?

  texto_cifrado = cifrado_atedv2(texto_original) #La salida sera totalmente aleatorea
  pos=texto_cifrado[1]

  print("\nTexto cifrado:", texto_cifrado[0])
  salida=texto_cifrado[0]
  #Opcional:
  print("\nEscencialmente no se imprimiran:")
  print("\nPosicion de reglas en la cadena:",pos)
  print("Reglas guardadas: ",str(salida[pos]+salida[pos+1]+salida[pos+2]),) #Esta salida debe ser igual a la de la funcion
  print("Orden: ", (salida[pos]+salida[pos+1]),"interpretarlo frente a lo dicho en el documento")
```
#### Sistema de desencriptado
```python
alpha_base = list("abcdefghijklmnopqrstuvwxyz") #Alfabeto estandar
def des_atedv2(salidad, pos):
  reglas=(salidad[pos],salidad[pos+1],salidad[pos+2]) #Se desarman las reglas guardadas, y se guardan en una tupla
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
  #Entradas del programa=[salida, pos] (Guia de elementos)

  texto_cifrado = input("Texto a desencriptar: ") #Hola Mundo!
  pos=int(input("poscicion de DX: "))

  texto_desencriptado = des_atedv2(texto_cifrado, pos)

  print("\nTexto cifrado:", texto_cifrado)
  print("\nTexto desencriptado:", texto_desencriptado)
```
**Importante:** *Para mejor entendimiento de Salidas consulte el nootebook adjunto arriba*

Si quiere probar independientemente alguna de las 2 funciones y/o ver versiones tempranas del codigo entre a: https://github.com/Felip-UN/ATEDx1



### Fuentes de consulta
Como extra se agrega que una de las principales fuentes de consulta fue [stock overflow](https://stackoverflow.com) en especial para lo que fue la biblioteca socket, [w3](https://www.w3schools.com) para la biblioteca **random** y ademas del repositorio de la clase 13 para guiarnos con algunas funciones [Github](https://github.com/fegonzalez7/pdc_unal_clase13)
