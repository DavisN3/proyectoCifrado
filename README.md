# proyectoCifrado
En el presente repositorio encontraran los avances preliminares al desarrollo del proyecto final del área de informática, con la finalidad de dar revisión y avance al planteamiento que se está desarrollando.
# ¿En qué consiste el proyectoCifrado?
Este proyecto consiste en la implementación de un sistema de cifrado personalizado basado en el cifrado César. Su objetivo es permitir que los mensajes sean cifrados y enviados a través de un chat, de modo que el receptor pueda descifrar el contenido solo si tiene la contraseña, la cual será enviada al ejecutarse el código por correo electrónico, con el agregado de que es una contraseña temporal y cambia cada "num" segundos.
## Extensiones preliminares
Antes de ejecutar el código es necesario instalar las siguiente extensión.
### Pasos:
1. Abrir "Símbolo de sistema".
2. Copiar y pegar las siguiente extensión.

**Extensión 1:**
```python
pip install secure-smtplib
```
# Código cifrado
## Cifrado César
### ¿En qué consiste?
El cifrado César consiste en un sistema del estilo sustitución, en el que cada letra del texto original es desplazado por otra letra que se encuentra a un número fijo de posición de la letra en el alfabeto. Ya sea un desplzamiento de 3 en la palabra "Hola", empezando por la "H" siendo reemplazada por la "K", la "o" por la letra "r", la "l" por la "o" y finalmente la "a" por la "d", dando como resultado "Krod".
**Importante: No importa si la letra es mayúscula o minúscula.**
## Sitema de cifrado implementado en el proyecto.
#### AD_DA: Orden del Alfabeto (se puede usar cualquier letra)
En terminos simples si el alfabeto se tomara como una lista, cada letra tendria un indice, si el indice de la primera es menor al de la segunda, es AD en caso contrario es DA

alpha_base = list("abcdefghijklmnopqrstuvwxyz")

- AD: orden normal, cualquier letra que en orden alfabetico este antes y otra despues (eo, fg, xy, etc) "a" tomaria indice 0 aqui ejem
- DA: orden inverso cualquier letra que en orden alfabetico este despues y otra anterior a esa misma (oe, gf, xy)  "a" tomaria indice 25 aqui ejem

#### X#:
Desplazamiento basado en cifrado cesar, al guardarse como regla este numero se reemplaza por por una letra con el "indice" de desplazamiento: alpha_base[#desplazamiento]

#### DX#
Sera AD_DA + X# y la posicion dentro del texto, escencialmente son las reglas y en donde se guardan, funcionara como un "insert", esta regla es escencial para facilitar el desencriptado
