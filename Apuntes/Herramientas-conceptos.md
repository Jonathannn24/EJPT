# 1) SMB (Server Message Block) — enumeración y verificación

**Objetivo:** detectar shares expuestos, usuarios enumerables y comprobar versiones vulnerables del servicio SMB (Server Message Block).

- **Comandos ** `sudo nmap -sS -p 139,445 -sV --script "smb-*" -Pn 192.168.100.4 `  
  **Descripción:** escanea el host para identificar información del servicio SMB y enumerar recursos compartidos (shares) y usuarios.  

- **Comando para listar shares con cliente SMB:** `smbclient -L //ip/ -N`  
  **Descripción:** consulta los recursos compartidos del host.  

- **Comandos para probar acceso anónimo:** `smbclient //ip/share -N`  
  **Descripción:** intenta conectarse como usuario anónimo a un share.  

- **Detección de vulnerabilidades conocidas (ej.: MS17-010 / EternalBlue):** `sudo nmap -sS -p 445 --script smb-vuln-ms17-010 --script-args=unsafe=1 192.168.100.4`  
  **Descripción:** ejecuta scripts de detección de firmas de vulnerabilidades históricas.  

- **Herramientas complementarias:** `enum4linux ip` (descripción: enumerador SMB específico).

---

# 2) Web — pruebas y mapeado

**Objetivo:** mapear recursos web, probar autenticación y detectar vectores como inyección SQL (Structured Query Language), LFI (Local File Inclusion) o XSS (Cross-Site Scripting).

- **Ejemplo de patrones de inyección (texto conceptual):**  
  `admin' OR '1'='1` — **Concepto:** ejemplo de payload de inyección SQL para pruebas en entornos controlados.  
  **Riesgo:** explotación real; documentar y reportar como hallazgo.

- **Local File Inclusion (LFI) (concepto):**  
  URL con parámetro que permite `../` para leer archivos locales.  
  **Qué mirar:** si `/etc/passwd` o ficheros de configuración son accesibles desde la URL.

- **Fuerza bruta contra formularios HTTP hydra :** `hydra -L /usr/share/seclists/Usernames/top-usernames-shortlist.txt -P /root/Desktop/wordlists/100-common-passwords.txt target.ine.local http-post-form "/login:username=^USER^&password=^PASS^:Invalid" -t 1`  
  **Descripción:** automatiza intentos de autenticación con listas de usuarios y contraseñas.  
  **Cuándo:** solo con permiso y con control de tasa para no generar denegación de servicio.

- **Enumeración de directorios (gobuster/ffuf conceptuales):** `gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/dirb/big.txt -t 30 -x php,txt,htm`  
  **Descripción:** busca rutas/dispositivos y plugins (ej.: plugins de WordPress).  
  **Consejo:** verificar falsos positivos y comparar tamaños/respuestas.

- **Mapeado con proxy (Burp Suite) (concepto):**  
  Interceptar peticiones → revisar parámetros, cookies, tokens.  
  **Recomendación:** usar para entender flujo de autenticación y tokens CSRF (Cross-Site Request Forgery).

---

# 3) Captura de tráfico / Wireshark

**Objetivo:** analizar paquetes para ver respuestas, códigos HTTP y tráfico de interés.

- **Filtrado de respuestas HTTP 200 (concepto):** `http.response.code == 200`  
  **Descripción:** muestra paquetes HTTP con código 200 OK.  
  **Qué buscar:** credenciales en texto claro, tokens o datos sensibles.

- **Herramientas y atajos:** búsqueda de strings en paquetes, seguir flujos TCP (Follow TCP Stream) para reconstruir sesiones.

- **Privacidad:** manejar archivos `pcap` con cuidado; eliminar datos sensibles si se comparten.

---

# 4) Bases de datos (MySQL, MSSQL)

**Nota:** las bases de datos pueden escuchar en puertos no estándar.

- **Conexión a bases de datos:** `mysql ip -u name -p`  
  **Descripción:** conectar a RDBMS (Relational DataBase Management System) para comprobar permisos y usuarios.  

- **En msfconsole (concepto):** búsqueda de exploits por versión (ej.: MSSQL 2012) — anotar versión y CVE (Common Vulnerabilities and Exposures) relevantes.

---

# 5) Payloads 
.
- **Qué es:** payload = código que se ejecuta en el sistema objetivo para obtener acceso.  

---

# 6) Windows — búsquedas, ficheros y permisos

- **Búsqueda de archivos y visualización (concepto):** búsqueda recursiva de ficheros y uso de `type` para leer contenidos en entornos controlados.  
- **Permisos (icacls) (concepto):** ver y modificar permisos de archivos; documentar riesgos asociados a cambios de ACL (Access Control List).  
- **Escalado de privilegios (concepto):** enumerar privilegios y servicios que corren con permisos elevados.

---

# 7) Rsync / FTP — enumeración y transferencia

- **Rsync (concepto):** enumerar módulos `rsync` públicos (si existen) y revisar contenido.  
- **FTP (concepto):** verificar accesos anónimos y posibilidad de subida;.

---

# 8) Linux — navegación, shells y búsquedas

- **Navegación de directorios (ls / conceptual)** y búsqueda de shells definidos en `/etc/shells`.  
- **Comandos para encontrar binarios SUID (concepto):** identificar archivos con bit SUID que puedan ser vectores de escalado `cat /etc/shells | while read shell; do ls -l $shell 2>/dev/null; done`.
` find / -exec /bin/rbash -p \; -quit cambiando el rbash por el que tenga SUID`
---

# 9) DNS / subdominios / fuzzing

- **Uso de `curl` con cabecera Host simulada (concepto):** comprobar wildcard o subdominios no autorizados.  
- **Fuzzing de subdominios (ffuf/otras) (concepto):** probar nombres para descubrir entradas virtual hosts.

---

# 10) Hashes y John (John the Ripper)

- **Generación y comprobación de hashes (concepto):** comprobar formato y robustez.  
- **john (concepto):** herramienta para auditoría de contraseñas en entornos controlados y con hashes legítimamente obtenidos.

---

# 11) RDP (Remote Desktop Protocol) / xfreerdp

- **Uso de clientes RDP (concepto):** probar acceso remoto autorizado.  
- **Recomendación:** MFA (Multi-Factor Authentication) obligatorio, limitación de exposición y listas blancas.

---

# 12) Nmap (Network Mapper) — opciones y scripts

- **Escaneo completo de puertos, UDP y TCP (concepto)**, uso de scripts NSE (Nmap Scripting Engine) para detección de servicios y vulnerabilidades históricas.  
- **Exportación de resultados:** `-oN` (formato normal) y `-oX` (XML) para análisis y reporte.

---

# 13) Subir archivos y servidores HTTP simples

- **Servir archivos estáticos en laboratorio (concepto):** utilidades para compartir archivos en LAN, útiles para pruebas en entornos controlados.  
- **Recomendación:** usar rutas seguras y eliminar el servidor al terminar.

---

# 14) Dumping de hashes y Mimikatz — advertencia

- **Mimikatz (concepto):** herramienta para extraer credenciales en Windows; potente y peligrosa.  
- **Uso responsable:** únicamente en entornos controlados y con permiso; no se ofrece tutorial ni comandos.

---

# 15) Pass-the-hash — concepto

- **Descripción:** técnica histórica que usa hashes en lugar de contraseñas en algunos protocolos.  

---

# 16) Steganografía (steghide)

- **Concepto:** extraer datos ocultos de imágenes y multimedia; `steghide` y herramientas similares pueden esconder contenido útil en archivos.  
- **Cuando revisar:** en análisis de exfiltración o evidencias sospechosas.

---

# 17) SSH (Secure Shell) — claves y formatos

- **Manejo de claves privadas:** usar permisos restrictivos (`chmod 600`) para proteger archivos `id_rsa` (concepto).  
- **Conversión de formatos para auditoría (ssh2john etc.):** uso controlado solo en pruebas con autorización.

---

# 18) PATH y SUID — notas de escalado

- **Revisar `$PATH`:** detectar directorios writeables por usuarios que puedan afectar ejecución de binarios privilegiados (concepto).  
- **Buscar binarios SUID:** analizar riesgos de escalado (sin proveer exploits).

---
