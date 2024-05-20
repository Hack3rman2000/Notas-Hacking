___

- Tags: #enumeración #herramientas #explotación #reconocimiento 

___
# Enumeración del Sistema

La enumeración del sistema es una fase crucial en las pruebas de penetración y evaluaciones de seguridad. Proporciona información detallada sobre la configuración del sistema, servicios en ejecución y permisos de archivo, permitiendo a los profesionales de seguridad identificar posibles vulnerabilidades y obtener información valiosa para futuros ataques. A continuación, se destacan dos herramientas populares utilizadas para la enumeración en sistemas Linux:

___
# LSE (Linux Smart Enumeration)

[LSE](https://github.com/diego-treitos/linux-smart-enumeration) es una herramienta de enumeración para sistemas Linux que utiliza diversos comandos de Linux para recopilar información y presentarla en un formato fácil de entender. Al ejecutar LSE, los profesionales de seguridad pueden obtener insights sobre la configuración del sistema, identificar servicios en ejecución y revisar permisos de archivos clave. Esto facilita la detección de posibles puntos débiles y configuraciones subóptimas en el sistema.

### Uso de LSE:

```bash
./lse.sh
```

___
# Pspy

[Pspy](https://github.com/DominicBreuker/pspy) es una herramienta de enumeración de procesos que permite a los profesionales de seguridad observar los procesos y comandos que se ejecutan en un sistema a intervalos regulares de tiempo. Esta herramienta es especialmente útil para la detección de malware, backdoors y procesos maliciosos que podrían estar ejecutándose en segundo plano sin interacción del usuario.

### Uso de Pspy:

```bash
./pspy
```

___
# Enumeración del Sistema - Comandos Importantes

La enumeración del sistema implica el uso de una variedad de comandos para obtener información clave sobre la configuración del sistema, permisos y servicios en ejecución. Aquí se presentan algunos comandos esenciales, así como enlaces a recursos valiosos para una enumeración más completa:

### Comandos Importantes:

1. **whoami:**

   ```bash
   whoami
   ```
   
   Muestra el nombre de usuario actual.

2. **id:**

   ```bash
   id
   ```
   
   Proporciona información detallada sobre el usuario actual y sus grupos.

3. **sudo -l:**

   ```bash
   sudo -l
   ```
   
   Lista los privilegios de sudo asignados al usuario actual.

4. **Comando para Buscar SUID:**

   ```bash
   find / -type f -perm -4000 2>/dev/null
   ```
   
   Busca archivos con el bit **SUID** activado.

5. **getcap -r /:**

   ```bash
   getcap -r /
   ```

   Muestra las capacidades asignadas a los archivos del sistema.

6. **crontab -l:**

   ```bash
   crontab -l
   ```
  
   Lista las tareas programadas para el usuario actual.

7. **cat /etc/crontab:**

   ```bash
   cat /etc/crontab
   ```

   Muestra el contenido del archivo crontab del sistema.

8. **systemctl list-timers:**

   ```bash
   systemctl list-timers
   ```

   Lista los temporizadores activos del sistema.

___
# Recursos Adicionales:

- [HackTricks - Guía de Enumeración](https://book.hacktricks.xyz/welcome/readme)
- [GTFOBins - Listado de Binarios SUID](https://gtfobins.github.io/)

Estos comandos y recursos son fundamentales para una enumeración exhaustiva del sistema. Los enlaces proporcionados te llevarán a guías detalladas y listas de binarios que pueden ser útiles en diversas situaciones de seguridad. Recuerda adaptar los comandos según el contexto del sistema que estás evaluando.