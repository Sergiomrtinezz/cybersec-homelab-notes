# Laboratorio: Auditoría de Linux y Vectores de Escalada de Privilegios
**Fecha:** 6-8 de Noviembre de 2026
**Entorno:**  Kali Linux
**Objetivos cumplidos:** Resolución de OverTheWire: Bandit (Niveles 0 al 33), análisis forense de Git, auditoría de permisos SUID/SGID, evaluación de `/etc/sudoers` y tareas programadas (`cron`).

---

## 1. Habilidades Prácticas Desarrolladas

Durante estas jornadas he completado la transición desde el manejo básico de la terminal hasta la identificación de vectores de abuso de privilegios en sistemas Linux:

- **Inspección y flujo de datos en Bash:** Manejo avanzado de pipes (`|`), redirección de descriptores estándar (`stdout`, `stderr` con `2>/dev/null`), operadores de texto (`grep`, `sort`, `uniq -u`, `tr`) y extracción de cadenas en binarios mediante `strings`.
- **Filtros por metadatos con `find`:** Búsqueda granular en el sistema de archivos por tamaño exacto (`-size Nc`), permisos de lectura y ejecución (`-readable`, `! -executable`), y propiedad de usuario/grupo (`-user`, `-group`).
- **Análisis de seguridad en Git (SCM Auditing):**
  - Recuperación de credenciales históricas censuradas en commits pasados (`git log`, `git show <hash>`).
  - Detección e inspección de ramas paralelas y de desarrollo huérfanas (`git branch -a`, `git checkout`).
  - Auditoría de etiquetas de versión (`git tag -l`, `git show <tag>`) para identificar artefactos o secretos filtrados en releases.
- **Escape de entornos restringidos (*Restricted Shell Escape*):** Identificación de restricciones por conversión de mayúsculas y escape mediante ejecución del parámetro posicional de proceso `$0` para heredar un entorno interactivo estándar.

---

## 2. Vectores de Escalada de Privilegios (PrivEsc) Analizados

### A. Permisos Especiales: SUID / SGID (SetUID / SetGID)
- **Mecanismo:** El bit SUID (`chmod u+s`, valor octal 4000) permite que un ejecutable se ejecute en memoria con los privilegios del propietario del archivo (habitualmente `root`) en lugar de los del usuario que lo invoca.
- **Detección del vector:**
  ```bash
  find / -perm -u=s -type f 2>/dev/null
