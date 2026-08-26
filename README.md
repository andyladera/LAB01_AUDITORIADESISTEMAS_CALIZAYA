## 2\. Procedimiento o Metodología

### Paso A — Crear el repositorio de papeles de trabajo (15 min)

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

![alt text](image-1.png)

Se crea el archivo de control de custodia:

cat \> 20\_evidencia/CADENA\_DE\_CUSTODIA.md \<\<'FIN'  
\# Cadena de custodia de la evidencia

| ID | Archivo | SHA-256 | Fecha y hora (UTC) | Obtenido por | Método de obtención | Sistema origen |  
|----|---------|---------|--------------------|--------------|---------------------|----------------|  
FIN  
git add . **&&** git commit \-m "Estructura inicial del expediente de auditoria"


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

## 3\. Resultados

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

## Toda la evidencia subida a un repositorio propio en Github, por tano presentan la carátula UPT, la URL github y las conclusiones
