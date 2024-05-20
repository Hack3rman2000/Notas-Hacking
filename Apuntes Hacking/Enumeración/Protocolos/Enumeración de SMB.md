___

- Tags: #enumeración #redes #smb #herramientas #protocolos 

___

```bash
smbclient -L 192.168.1.1 -N
```

Este comando sirve para listar los recursos compartidos con [[SMB]] sin necesidad de proporcionar credenciales.

```bash
smbmap -H 192.168.1.1
```

Sirve para enumerar de manera más detallada los recursos compartidos con [[SMB]], proporcionando información adicional.

```bash
smbclient //192.168.1.1/share -N
```

Accedes al recurso compartido por [[SMB]] sin necesidad de ingresar credenciales.

```bash
mount -t cifs //192.168.1.1/share /mnt/mounted
```

Este comando sirve para traer un recurso compartido de [[SMB]] y luego montarlo en tu máquina principal.

```bash
umount /mnt/mounted
```


Desmonta un recurso compartido [[SMB]] previamente montado.