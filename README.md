  **UNIVERSIDAD PRIVADA DE TACNA**

**FACULTAD DE INGENIERÍA**

**ESCUELA DE INGENIERÍA DE SISTEMAS**

![][image1]

**Guía Práctica de Laboratorio**

**“Auditoría en Docker y custodia de la evidencia digital”**

**CURSO:**

**“AUDITORÍA DE SISTEMAS”**

**Laboratorio N.º 01**

**AUTOR(ES):**

Apellidos y nombres · Código · Grupo

**Docente:**

**Dr. Oscar Juan Jimenez Flores**

**TACNA — PERÚ**

# Índice General {#índice-general}

[Índice General	2](#índice-general)

[Introducción	3](#introducción)

[Guía de Laboratorio N.º 01 — «Montaje del laboratorio de auditoría en Docker y custodia de la evidencia digital»	4](#guía-de-laboratorio-n.º-01-auditoría-en-docker-y-custodia-de-la-evidencia-digital)

[1\. Información sobre el evento práctico	4](#1.-información-sobre-el-evento-práctico)

[1.1. Título del evento práctico	4](#1.1.-título-del-evento-práctico)

[1.2. Objetivos	4](#1.2.-objetivos)

[1.3. Tiempo de duración	4](#1.3.-tiempo-de-duración)

[1.4. Resultados de Aprendizaje (RA)	4](#1.4.-resultados-de-aprendizaje-\(ra\))

[1.5. Recursos (equipos, materiales, programas y otros)	4](#1.5.-recursos-\(equipos,-materiales,-programas-y-otros\))

[1.6. Seguridad	6](#1.6.-seguridad)

[2\. Procedimiento o Metodología	6](#2.-procedimiento-o-metodología)

[Paso A — Crear el repositorio de papeles de trabajo (15 min)	6](#paso-a-—-crear-el-repositorio-de-papeles-de-trabajo-\(15-min\))

[Paso B — Definir el entorno auditable (30 min)	7](#heading)

[Paso C — Capturar la línea base del sistema auditado (25 min)	8](#heading-1)

[Paso D — Sellar la evidencia (cadena de custodia) (25 min)	9](#heading-2)

[Paso E — Primer hallazgo documentado (25 min)	10](#heading-3)

[3\. Resultados	11](#3.-resultados)

[4\. Conclusiones	11](#toda-la-evidencia-subida-a-un-repositorio-propio-en-github,-por-tano-presentan-la-carátula-upt,-la-url-github-y-las-conclusiones)

[5\. Cuestionario	11](#heading=h.6f3sj5f3tdc5)

[6\. Referencias Bibliográficas	12](#heading-4)

[7\. Anexos	12](#7.-anexos)

# Introducción {#introducción}

La presente guía práctica corresponde al **Guía de Laboratorio N.º 01 — «Auditoría en Docker y custodia de la evidencia digital»** y se desarrolla en la sesión de taller en el laboratorio de cómputo de la semana.

El trabajo de laboratorio de este curso es **acumulativo**, cada guía produce un artefacto que se incorpora al entregable final del semestre. Por esa razón la evidencia se versiona, se rotula y se conserva desde la primera sesión.

Al concluir la práctica, el estudiante habrá logrado:

* Desplegar con Docker Compose un entorno multi-servicio que servirá como organización auditada durante toda la Unidad I.

* Aplicar el principio de reproducibilidad de la evidencia: cualquier tercero debe poder reconstruir el entorno y obtener el mismo resultado.

* Construir un repositorio de papeles de trabajo versionado en Git, con estructura normalizada.

* Implementar la cadena de custodia digital mediante funciones hash y sellado de tiempo, de modo que la evidencia sea defendible.

* Registrar la línea base (baseline) del entorno para poder demostrar posteriormente qué cambió y cuándo.

La evaluación considera la evidencia depositada en el repositorio y el informe elaborado con esta misma estructura, información sobre el evento práctico, procedimiento o metodología, resultados, conclusiones, cuestionario, referencias bibliográficas y anexos.

# Guía de Laboratorio N.º 01 Auditoría en Docker y custodia de la evidencia digital {#guía-de-laboratorio-n.º-01-auditoría-en-docker-y-custodia-de-la-evidencia-digital}

## 1\. Información sobre el evento práctico {#1.-información-sobre-el-evento-práctico}

### 1.1. Título del evento práctico {#1.1.-título-del-evento-práctico}

Montaje de un entorno auditable reproducible con Docker Compose y establecimiento de la cadena de custodia de la evidencia digital.

### 1.2. Objetivos {#1.2.-objetivos}

* Desplegar con **Docker Compose** un entorno multi-servicio que servirá como organización auditada durante toda la Unidad I.

* Aplicar el principio de **reproducibilidad de la evidencia**: cualquier tercero debe poder reconstruir el entorno y obtener el mismo resultado.

* Construir un **repositorio de papeles de trabajo** versionado en Git, con estructura normalizada.

* Implementar la **cadena de custodia digital** mediante funciones hash y sellado de tiempo, de modo que la evidencia sea defendible.

* Registrar la **línea base (baseline)** del entorno para poder demostrar posteriormente qué cambió y cuándo.

### 1.3. Tiempo de duración {#1.3.-tiempo-de-duración}

**02 horas.**

### 1.4. Resultados de Aprendizaje (RA) {#1.4.-resultados-de-aprendizaje-(ra)}

* **RA1** Analiza e interpreta los conceptos y terminología de Auditoría de Sistemas.

* **RA2** Evalúa la seguridad de la información en Auditoría de Sistemas.

### 1.5. Recursos (equipos, materiales, programas y otros) {#1.5.-recursos-(equipos,-materiales,-programas-y-otros)}

**Equipos y sistema operativo**

| Recurso | Requisito mínimo |
| :---- | :---- |
| Computadora | 8 GB de RAM, 20 GB libres en disco, virtualización habilitada en BIOS/UEFI |
| Sistema operativo | Windows 11, Linux o macOS, **con permisos de administrador** |
| Terminal | PowerShell 7, bash o zsh |
| Conexión | Necesaria solo para descargar las imágenes de contenedor |

**Herramientas y enlaces de descarga.** Todas son libres o gratuitas. Se instalan **antes** de la sesión.

| Herramienta | Para qué se usa en este laboratorio | Descarga |
| :---- | :---- | :---- |
| **Docker Desktop** o **Docker Engine** | Desplegar el entorno multiservicio que hará de organización auditada. *Gratuito, Apache 2.0* | https://docs.docker.com/get-started/get-docker/ |
| **Docker Compose** | Orquestar los cinco servicios del entorno con un solo archivo. *Incluido en Docker Desktop* | https://docs.docker.com/compose/install/ |
| **Git** | Versionar el repositorio de papeles de trabajo. *GPL-2.0* | https://git-scm.com/downloads |
| **Visual Studio Code** | Editar el docker-compose.yml y los papeles de trabajo. *MIT* | https://code.visualstudio.com/download |
| **OpenSSL** | Calcular los hash SHA-256 de la cadena de custodia. *Apache 2.0; ya incluido en Linux y macOS* | https://openssl-library.org/source/ |
| **7-Zip** *(solo Windows)* | Empaquetar la evidencia sin alterar sus fechas. *LGPL* | https://www.7-zip.org/download.html |

**Imágenes de contenedor que se descargarán** (se obtienen automáticamente con docker compose pull):

| Imagen | Papel en el entorno auditado | Ficha oficial |
| :---- | :---- | :---- |
| bkimminich/juice-shop | Aplicación web deliberadamente vulnerable | https://hub.docker.com/r/bkimminich/juice-shop |
| vulnerables/web-dvwa | Segunda aplicación vulnerable, para pruebas de control de acceso | https://hub.docker.com/r/vulnerables/web-dvwa |
| postgres:16 | Base de datos con los registros a auditar | https://hub.docker.com/\_/postgres |
| wordpress:latest | Portal institucional simulado | https://hub.docker.com/\_/wordpress |
| mariadb:11 | Motor de datos del portal | https://hub.docker.com/\_/mariadb |

**Verificación previa.** Ejecute docker \--version, docker compose version y git \--version. Si alguno falla, resuélvalo **antes** del laboratorio: la instalación no forma parte de las dos horas de práctica.

### 1.6. Seguridad {#1.6.-seguridad}

**Advertencia obligatoria.** Este laboratorio despliega aplicaciones **deliberadamente vulnerables**. Su exposición a una red no controlada convierte el equipo en un punto de compromiso.

Reglas no negociables:

1. Los contenedores se publican **exclusivamente en 127.0.0.1**, nunca en 0.0.0.0.

2. El laboratorio corre en una **red Docker aislada** (audit\_net), sin puentes a la red del campus.

3. **Nunca** se ejecuta ninguna prueba de este curso contra un sistema que no sea este laboratorio o un sistema con autorización escrita del titular.

4. Al terminar la sesión se ejecuta docker compose down para bajar el entorno.

5. Ninguna credencial real, ningún dato personal real y ningún dato de la empresa auditada se cargan en este entorno.

## 2\. Procedimiento o Metodología {#2.-procedimiento-o-metodología}

### Paso A — Crear el repositorio de papeles de trabajo (15 min) {#paso-a-—-crear-el-repositorio-de-papeles-de-trabajo-(15-min)}

Todo auditor trabaja sobre un expediente estructurado. Se crea con esta jerarquía normalizada:

mkdir \-p auditoria-si084/{00\_administracion,10\_planificacion,20\_evidencia,30\_papeles\_trabajo,40\_hallazgos,50\_informe}  
cd auditoria-si084  
git init

| Carpeta | Contenido |
| :---- | :---- |
| 00\_administracion | Acta de acuerdo, acuerdo de confidencialidad, declaración de independencia |
| 10\_planificacion | Plan y programa de auditoría (Unidad II) |
| 20\_evidencia | Evidencia cruda: capturas, exportaciones, salidas de herramientas |
| 30\_papeles\_trabajo | Análisis del auditor sobre la evidencia |
| 40\_hallazgos | Un archivo por hallazgo, con estructura CCCER |
| 50\_informe | Informe final (Unidad III) |

Se crea el archivo de control de custodia:

cat \> 20\_evidencia/CADENA\_DE\_CUSTODIA.md \<\<'FIN'  
\# Cadena de custodia de la evidencia

| ID | Archivo | SHA-256 | Fecha y hora (UTC) | Obtenido por | Método de obtención | Sistema origen |  
|----|---------|---------|--------------------|--------------|---------------------|----------------|  
FIN  
git add . **&&** git commit \-m "Estructura inicial del expediente de auditoria"

###  {#heading}

### Paso B — Definir el entorno auditable (30 min)

Se crea entorno/docker-compose.yml. Este archivo **es en sí mismo evidencia**: describe con exactitud el sistema auditado.

name**:** si084-lab

networks**:**  
  audit\_net**:**  
    driver**:** bridge

volumes**:**  
  db\_data**:**  
  wp\_data**:**

services**:**

  *\# \--- Aplicación web vulnerable moderna (SPA \+ API REST) \---*  
  juiceshop**:**  
    image**:** bkimminich/juice-shop:latest  
    container\_name**:** si084\_juiceshop  
    ports**:**  
      **\-** "127.0.0.1:3000:3000"  
    networks**:** **\[**audit\_net**\]**  
    restart**:** unless-stopped

  *\# \--- Aplicación web vulnerable clásica (PHP \+ MySQL) \---*  
  dvwa**:**  
    image**:** vulnerables/web-dvwa:latest  
    container\_name**:** si084\_dvwa  
    ports**:**  
      **\-** "127.0.0.1:8081:80"  
    networks**:** **\[**audit\_net**\]**  
    restart**:** unless-stopped

  *\# \--- Base de datos corporativa simulada \---*  
  db**:**  
    image**:** postgres:16  
    container\_name**:** si084\_db  
    environment**:**  
      POSTGRES\_USER**:** erp\_app  
      POSTGRES\_PASSWORD**:** erp\_app          *\# \<-- hallazgo intencional: credencial débil*  
      POSTGRES\_DB**:** erp  
    ports**:**  
      **\-** "127.0.0.1:5432:5432"  
    volumes**:**  
      **\-** db\_data:/var/lib/postgresql/data  
    networks**:** **\[**audit\_net**\]**

  *\# \--- Portal corporativo \---*  
  wordpress**:**  
    image**:** wordpress:latest  
    container\_name**:** si084\_portal  
    depends\_on**:** **\[**wpdb**\]**  
    environment**:**  
      WORDPRESS\_DB\_HOST**:** wpdb  
      WORDPRESS\_DB\_USER**:** wp  
      WORDPRESS\_DB\_PASSWORD**:** wp  
      WORDPRESS\_DB\_NAME**:** wp  
    ports**:**  
      **\-** "127.0.0.1:8082:80"  
    volumes**:**  
      **\-** wp\_data:/var/www/html  
    networks**:** **\[**audit\_net**\]**

  wpdb**:**  
    image**:** mariadb:11  
    container\_name**:** si084\_wpdb  
    environment**:**  
      MARIADB\_DATABASE**:** wp  
      MARIADB\_USER**:** wp  
      MARIADB\_PASSWORD**:** wp  
      MARIADB\_ROOT\_PASSWORD**:** root  
    networks**:** **\[**audit\_net**\]**

Levantar y verificar:

cd entorno  
docker compose up \-d  
docker compose ps

Comprobación de servicios:

| Servicio | URL local |
| :---- | :---- |
| Juice Shop | http://127.0.0.1:3000 |
| DVWA | http://127.0.0.1:8081 (usuario admin / password) |
| Portal WordPress | http://127.0.0.1:8082 |
| PostgreSQL | 127.0.0.1:5432 |

###  {#heading-1}

### Paso C — Capturar la línea base del sistema auditado (25 min)

La línea base responde a la pregunta que toda auditoría termina haciendo: *¿cómo estaba esto el día que empezamos?*

mkdir \-p ../20\_evidencia/E01\_baseline  
cd ../20\_evidencia/E01\_baseline

*\# 1\. Inventario de contenedores en ejecución*  
docker compose \-f ../../entorno/docker-compose.yml ps \--format json \> contenedores.json

*\# 2\. Inventario de imágenes con su digest inmutable*  
docker images \--digests \--format '{{.Repository}}\\t{{.Tag}}\\t{{.Digest}}' \> imagenes.tsv

*\# 3\. Puertos publicados (superficie de exposición)*  
docker ps \--format '{{.Names}}\\t{{.Ports}}' \> puertos.tsv

*\# 4\. Configuración efectiva desplegada (no la declarada)*  
docker compose \-f ../../entorno/docker-compose.yml config \> compose\_efectivo.yml

*\# 5\. Usuarios de la base de datos corporativa*  
docker exec si084\_db psql \-U erp\_app \-d erp \-c "\\du" \> usuarios\_postgres.txt

*\# 6\. Variables de entorno de un servicio (fuga de secretos en claro)*  
docker inspect si084\_db \--format '{{json .Config.Env}}' \> env\_db.json

**Observación de auditor.** El paso 6 casi siempre produce el primer hallazgo del curso: las credenciales viajan en variables de entorno en texto claro y son visibles para cualquiera con acceso al *socket* de Docker.

###  {#heading-2}

### Paso D — Sellar la evidencia (cadena de custodia) (25 min)

Sin integridad demostrable, la evidencia es refutable. Se calcula el hash SHA-256 de cada artefacto y se registra.

**Linux / macOS:**

sha256sum \* \> ../SHA256SUMS\_E01.txt  
cat ../SHA256SUMS\_E01.txt

**Windows PowerShell:**

Get-ChildItem \-File | Get-FileHash \-Algorithm SHA256 |  
  Select-Object Hash, @{n\='File';e\={Split-Path $\_.Path \-Leaf}} |  
  Export-Csv ..\\SHA256SUMS\_E01.csv \-NoTypeInformation

Se registra cada archivo en 20\_evidencia/CADENA\_DE\_CUSTODIA.md con: identificador, nombre, hash, fecha y hora **en UTC**, auditor responsable, método de obtención y sistema de origen.

**Sellado de tiempo con Git.** El *commit* de Git es en sí un sello criptográfico, su identificador es un hash del contenido más la marca temporal más el *commit* anterior, de modo que alterar un archivo antiguo invalida toda la cadena posterior.

cd ../..  
git add 20\_evidencia  
git commit \-m "E01: linea base del entorno auditado, sellada con SHA-256"  
git log \--format\='%H  %aI  %s' \-1

**Verificación de que el sellado funciona.** Se altera deliberadamente un archivo y se comprueba que el hash cambia:

echo "linea agregada" \>\> 20\_evidencia/E01\_baseline/puertos.tsv  
sha256sum 20\_evidencia/E01\_baseline/puertos.tsv     *\# el hash difiere del registrado*  
git checkout \-- 20\_evidencia/E01\_baseline/puertos.tsv   *\# se restaura*

###  {#heading-3}

### Paso E — Primer hallazgo documentado (25 min)

Con la línea base en mano, se redacta el primer hallazgo en 40\_hallazgos/H-001.md:

\# H-001 · Credenciales de base de datos almacenadas en texto claro

\- \*\*Riesgo:\*\* Alto  
\- \*\*Fecha:\*\* aaaa-mm-dd  
\- \*\*Evidencia:\*\* E01/env\_db.json (SHA-256: ...)

\#\# Condición  
La instancia PostgreSQL ***\`si084\_db\`*** recibe su usuario y contraseña mediante variables de  
entorno en texto claro (***\`POSTGRES\_USER=erp\_app\`***, ***\`POSTGRES\_PASSWORD=erp\_app\`***), legibles con  
***\`docker inspect\`*** por cualquier cuenta con acceso al socket de Docker. La contraseña es  
idéntica al nombre de usuario.

\#\# Criterio  
ISO/IEC 27001:2022, Anexo A, control \*\*A.5.17 Authentication information\*\* y control  
\*\*A.8.24 Use of cryptography\*\*. Adicionalmente, NTP-ISO/IEC 27001:2022 para entidades del  
Sistema Nacional de Informática.

\#\# Causa  
El despliegue se realiza con un archivo de composición sin gestión de secretos; no existe  
un almacén de secretos ni una política de complejidad de contraseñas de servicio.

\#\# Efecto  
Un usuario con acceso local al host o un contenedor comprometido en la misma red obtiene  
acceso completo a la base de datos corporativa, con capacidad de lectura, modificación y  
borrado de la totalidad de los datos del ERP.

\#\# Recomendación  
Migrar las credenciales a ***\`docker secret\`*** o a un gestor de secretos externo; establecer  
contraseñas de servicio de al menos 16 caracteres generadas aleatoriamente; restringir el  
acceso al socket de Docker al grupo de administradores. Responsable: Jefe de Infraestructura.  
Plazo: 30 días.

## 3\. Resultados {#3.-resultados}

Al término de la práctica el estudiante debe evidenciar:

| \# | Resultado esperado | Verificación |
| :---- | :---- | :---- |
| 1 | Repositorio auditoria-si084 con las seis carpetas y al menos dos *commits* | git log \--oneline |
| 2 | Cinco servicios en estado running, publicados solo en 127.0.0.1 | docker compose ps y docker ps \--format '{{.Ports}}' |
| 3 | Carpeta 20\_evidencia/E01\_baseline con los seis artefactos | Listado de directorio |
| 4 | Archivo SHA256SUMS\_E01.txt con un hash por artefacto | Contenido del archivo |
| 5 | CADENA\_DE\_CUSTODIA.md con las filas completas y horas en UTC | Contenido del archivo |
| 6 | Hallazgo H-001.md con los cinco bloques de la estructura CCCER | Contenido del archivo |
| 7 | Demostración de que modificar un artefacto rompe el hash registrado | Captura del antes y el después |

## Toda la evidencia subida a un repositorio propio en Github, por tano presentan la carátula UPT, la URL github y las conclusiones  {#toda-la-evidencia-subida-a-un-repositorio-propio-en-github,-por-tano-presentan-la-carátula-upt,-la-url-github-y-las-conclusiones}

## 

## 4\. Conclusiones

El estudiante redacta un mínimo de tres conclusiones propias. Se esperan líneas argumentales como:

1. La reproducibilidad del entorno auditado —garantizada aquí por un archivo de composición versionado— es lo que permite que un tercero replique la prueba y llegue al mismo resultado; sin ella, el hallazgo depende de la palabra del auditor.

2. La cadena de custodia no es burocracia: es el mecanismo que convierte un archivo en evidencia defendible ante una gerencia que va a discutir el hallazgo.

3. La línea base capturada al inicio del encargo es el único punto de referencia que permitirá luego demostrar qué cambió durante la auditoría y quién lo cambió.

##  {#heading-4}

## 6\. Referencias Bibliográficas

ISO 19011:2018. *Guidelines for auditing management systems*. International Organization for Standardization. https://www.iso.org/standard/70017.html

ISO/IEC 27001:2022. *Information security, cybersecurity and privacy protection — Information security management systems — Requirements*. https://www.iso.org/standard/27001

Resolución de Secretaría de Gobierno y Transformación Digital n.° 003-2023-PCM/SGTD (uso obligatorio de la NTP-ISO/IEC 27001 vigente en entidades públicas). https://www.gob.pe/institucion/pcm/tema/transformacion-digital/normas-legales

Piattini Velthuis, M., Del Peso Navarro, E. y Del Peso Ruiz, M. (2009). *Auditoría de tecnologías y sistemas de información* (6.ª ed.). Alfaomega / Ra-Ma. Capítulos 1 y 2\.

ISACA. *Code of Professional Ethics*. https://www.isaca.org/credentialing/code-of-professional-ethics

ISACA. *ITAF: A Professional Practices Framework for IS Audit/Assurance* (5.ª ed.). https://www.isaca.org/resources/frameworks-standards-and-models

Ley 30096, Ley de Delitos Informáticos (Perú). https://www.gob.pe/institucion/congreso-de-la-republica/normas-legales

Docker Inc. *Docker Compose specification*. https://docs.docker.com/reference/compose-file/

OWASP Foundation. *OWASP Juice Shop Project*. https://owasp.org/www-project-juice-shop/

## 7\. Anexos {#7.-anexos}

* anexo\_A\_capturas.pdf — capturas de docker compose ps, del navegador con cada servicio activo y del git log.

* anexo\_B\_SHA256SUMS\_E01.txt — archivo de hashes generado.

* anexo\_C\_propuesta\_empresa.pdf — ficha de la empresa real propuesta por el equipo: razón social, RUC, sector, sistema de información a auditar, contacto y evidencia del acercamiento inicial.

*SI-084 AUDITORÍA DE SISTEMAS · Laboratorio N.º 01 · Dr. Oscar Juan Jimenez Flores · Universidad Privada de Tacna — Escuela Profesional de Ingeniería de Sistemas.*

[image1]: <data:image/png;base64,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>
