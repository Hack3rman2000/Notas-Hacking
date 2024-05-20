___

- Tags: #redes #protocolos #smb

___
# Descripción

**SMB** (Server Message Block) es un protocolo de red utilizado para compartir archivos, impresoras y otros recursos en una red local. Originalmente desarrollado por Microsoft, **SMB** se ha convertido en un estándar de facto y se utiliza en entornos Windows y también en sistemas no Windows a través de implementaciones como [Samba](https://es.wikipedia.org/wiki/Samba_(software)) en sistemas Linux.

___
# Características Principales

- **Compartición de Recursos:** Permite compartir archivos, impresoras y otros recursos entre sistemas en una red.
- **Protocolo de Nivel de Sesión:** Funciona en el nivel de sesión del [[modelo OSI]] y utiliza el protocolo de transporte [NetBIOS](https://es.wikipedia.org/wiki/NetBIOS) (Network Basic Input/Output System).
- **Autenticación y Acceso Seguro:** Proporciona mecanismos de autenticación y cifrado para asegurar el acceso seguro a recursos compartidos.

___
# Ejemplos de Uso

### Montar un Recurso Compartido en Linux (Usando Samba):

```bash
sudo mount -t cifs //servidor/compartido /punto_de_montaje -o user=usuario
```

### Acceder a un Recurso Compartido en Windows:

1. Abrir el Explorador de Archivos.
2. Ingresar `\\servidor\compartido` en la barra de direcciones.

___
# Seguridad y Consideraciones
- Configurar permisos adecuados en los recursos compartidos para controlar el acceso.
- Implementar medidas de seguridad adicionales, como la restricción de versiones de **SMB** y el uso de conexiones seguras.

