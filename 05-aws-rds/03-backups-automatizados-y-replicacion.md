# 03. Automatización de Copias de Seguridad y Replicación

## 📖 Teoría

La continuidad de negocio en bases de datos relacionales depende de dos estrategias: **Backups / Recuperación** y **Alta Disponibilidad (HA)**.

---

### 1. Copias de Seguridad en RDS
* **Backups Automatizados:**
  - Toman una foto diaria completa de la base de datos y guardan los logs de transacciones (*WAL / Binary Logs*).
  - Permite la **Recuperación en un Punto en el Tiempo (PITR - Point-In-Time Restore)** con precisión de segundos dentro del período de retención (de 1 a 35 días).
* **DB Snapshots Manuales:**
  - Iniciados manualmente por el usuario.
  - Persisten en Amazon S3 indefinidamente incluso si la instancia de RDS original es eliminada.

---

### 2. Alta Disponibilidad vs. Escalabilidad

| Característica | Multi-AZ Deployment (HA) | Read Replicas (Escalabilidad) |
| :--- | :--- | :--- |
| **Propósito Principal** | Alta Disponibilidad y Conmutación por error (*Failover*). | Escalado de lecturas (`SELECT`) y análisis. |
| **Tipo de Replicación** | **Síncrona** a nivel de bloque de almacenamiento. | **Asíncrona** a nivel de motor SQL. |
| **Ubicación** | Instancia Standby en **otra Zona de Disponibilidad**. | Misma región u otras regiones (Cross-Region). |
| **Acceso a Datos** | La instancia Standby **NO es accesible** para consultas. | Son instancias **accesibles para lectura**. |
| **Failover** | **Automático** (RDS cambia los registros DNS en 60-120 seg). | **Manual / Promoción** a instancia independiente. |

---

## 💻 Ejemplos Prácticos

```bash
# 1. Crear un Snapshot Manual desde AWS CLI
aws rds create-db-snapshot \
    --db-instance-identifier mi-postgres-rds \
    --db-snapshot-identifier snapshot-pre-despliegue-2026

# 2. Restaurar una base de datos a un punto específico en el tiempo (PITR)
aws rds restore-db-instance-to-point-in-time \
    --source-db-instance-identifier mi-postgres-rds \
    --target-db-instance-identifier rds-restaurada-test \
    --restore-time 2026-08-03T12:00:00.000Z

# 3. Crear una Read Replica para liberar carga de lectura de la base principal
aws rds create-db-instance-read-replica \
    --db-instance-identifier rds-replica-lectura \
    --source-db-instance-identifier mi-postgres-rds \
    --db-instance-class db.t4g.micro