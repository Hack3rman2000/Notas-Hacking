___
- Tags: #cms #joomla #enumeración #joomscan #herramientas 

___
# Introducción 

[JoomScan](https://github.com/rezasp/joomscan) es una herramienta de escaneo diseñada específicamente para identificar y enumerar información sobre sitios web construidos con el sistema de gestión de contenidos [[Joomla]]. La enumeración es un proceso crucial en la seguridad informática que implica recopilar información detallada sobre un objetivo para comprender su estructura y posibles vulnerabilidades.  

___
# Uso de JoomScan 

```bash
joomscan -u [URL]
```

La utilidad básica del comando `joomscan` con la opción `-u` es escanear una página web y proporcionar información relevante sobre si está construida con [[Joomla]]. Reemplaza `[URL]` con la dirección de la página web que deseas escanear.

___
# Resultados del Escaneo

**JoomScan** realiza un análisis exhaustivo y devuelve información valiosa, que puede incluir:

- **Versión de Joomla:** Identifica la versión específica del [[CMS]] utilizado en el sitio.
- **Componentes y Módulos:** Lista los componentes y módulos instalados en el sitio, lo que puede ayudar a identificar posibles puntos de entrada.
- **Vulnerabilidades Conocidas:** Informa sobre vulnerabilidades conocidas asociadas con la versión específica de [[Joomla]] encontrada.

___
# Medidas de Seguridad

- **Actualizaciones Regulares:** Mantén tu instalación de [[Joomla]] actualizada para mitigar vulnerabilidades conocidas.
- **Configuración Segura:** Ajusta la configuración del servidor y del [[CMS]] siguiendo las mejores prácticas de seguridad.
- **Monitoreo Continuo:** Utiliza herramientas de monitoreo de seguridad para detectar actividades sospechosas.