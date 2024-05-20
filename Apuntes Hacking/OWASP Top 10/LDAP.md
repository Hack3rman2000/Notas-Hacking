___

- Tags:

___
# Definición 

**LDAP**, siglas de Protocolo Ligero de Acceso a Directorios, es un protocolo estándar de la industria utilizado para acceder y mantener información de directorios distribuidos. Se utiliza comúnmente para almacenar información de usuarios, grupos, recursos de red y otros datos de configuración en un entorno de red.

___
# Características Principales:

- **Ligero:** **LDAP** es "ligero" en comparación con otros protocolos de acceso a directorios más antiguos, lo que significa que es eficiente en términos de recursos y ancho de banda. Esto lo hace adecuado para su uso en redes grandes y distribuidas.

- **Basado en TCP/IP:** LDAP opera sobre [[TCP|TCP/IP]], lo que lo hace compatible con la infraestructura de red existente y fácilmente accesible a través de Internet.

- **Modelo Cliente-Servidor:** **LDAP** sigue un modelo cliente-servidor, donde los clientes **LDAP** envían solicitudes al servidor **LDAP** para buscar, agregar, modificar o eliminar información del directorio.

- **Jerárquico:** **LDAP** organiza los datos en una estructura jerárquica similar a un árbol, utilizando una estructura de datos llamada árbol de información. Esto permite una fácil organización y navegación de los datos.

- **Basado en Esquema:** **LDAP** utiliza un esquema de datos para definir y organizar los tipos de información que pueden ser almacenados en el directorio. Esto proporciona una estructura y consistencia en los datos almacenados.

___
# Componentes de LDAP:

- **Servidor LDAP:** El servidor **LDAP** almacena y gestiona la información del directorio. Responde a las solicitudes de los clientes **LDAP** y proporciona acceso a los datos del directorio.

- **Cliente LDAP:** Los clientes **LDAP** son aplicaciones que interactúan con el servidor **LDAP** para realizar operaciones en el directorio, como buscar, agregar, modificar o eliminar información.

- **Directorio LDAP:** El directorio **LDAP** es donde se almacena la información organizada jerárquicamente. Puede contener datos como información de usuarios, grupos, recursos de red, entre otros.

- **Esquema LDAP:** El esquema **LDAP** define la estructura y el formato de los datos que pueden ser almacenados en el directorio. Define los tipos de atributos y clases de objetos que pueden ser utilizados en el directorio.

___
# Utilidades y Herramientas:

- **ldapsearch:** Una herramienta de línea de comandos utilizada para buscar información en un servidor **LDAP**, permitiendo a los usuarios buscar y recuperar datos del directorio.

- **ldapadd, ldapmodify, ldapdelete:** Estas herramientas se utilizan para agregar, modificar o eliminar entradas en el directorio **LDAP.**

- **ldapadmin:** Un cliente gráfico utilizado para administrar y manipular datos en un directorio **LDAP**.

___
# Casos de Uso Comunes:

- **Autenticación de Usuarios:** **LDAP** se utiliza ampliamente para autenticar y autorizar usuarios en sistemas y aplicaciones. Los servidores LDAP almacenan credenciales de usuario y los servicios de autenticación pueden verificar estas credenciales consultando el servidor **LDAP**.

- **Directorio de Servicios de Red:** **LDAP** se utiliza para almacenar información sobre recursos de red, como impresoras, servidores, y otros dispositivos, facilitando su gestión y acceso.

- **Gestión de Identidades:** **LDAP** es utilizado en entornos empresariales para centralizar y gestionar la información de identidad de los usuarios, incluyendo información de contacto, roles y privilegios.

**LDAP** es una tecnología fundamental en la gestión de identidades y el acceso a la información en entornos de red. Su flexibilidad, eficiencia y capacidad de integración lo convierten en una opción popular para una amplia gama de aplicaciones y servicios en redes empresariales y en Internet.