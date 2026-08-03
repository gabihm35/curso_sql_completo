# 01. Aprovisionamiento y Configuración de AWS RDS

## 📖 Teoría

**Amazon RDS (Relational Database Service)** es un servicio administrado de bases de datos relacionales en la nube que simplifica las tareas de aprovisionamiento, escalado, aplicación de parches y copias de seguridad.

### Beneficios del Enfoque Administrado:
* **Reducción de Carga Operativa:** AWS gestiona la instalación del SO, parches de seguridad del motor y reemplazo de hardware defectuoso.
* **Elección de Motores:** Soporta PostgreSQL, MySQL, MariaDB, Oracle, SQL Server y Amazon Aurora.
* **Escalabilidad Horizontal y Vertical:** Permite cambiar el tamaño de la instancia (`db.t4g.micro`, `db.r6g.xlarge`) o expandir el almacenamiento (Storage Auto Scaling) sin migraciones complejas.

### Clases de Almacenamiento:
* **gp3 (General Purpose SSD):** Balance costo/rendimiento por defecto. Permite configurar IOPS y Throughput de forma independiente.
* **io2 (Provisioned IOPS SSD):** Diseñado para cargas de trabajo críticas de alta latencia y transacciones intensivas.

---

## 💻 Ejemplos Prácticos (Aprovisionamiento vía AWS CLI / Terraform)

### A. Aprovisionamiento usando AWS CLI

```bash
aws rds create-db-instance \
    --db-instance-identifier mi-postgres-rds \
    --db-instance-class db.t4g.micro \
    --engine postgres \
    --engine-version 16.1 \
    --allocated-storage 20 \
    --max-allocated-storage 100 \
    --storage-type gp3 \
    --master-username admin_user \
    --master-user-password "MiPasswordSeguro2026!" \
    --backup-retention-period 7 \
    --no-publicly-accessible
```

### B. Ejemplo de Configuración con Infrastructure as Code (Terraform)

```bash
resource "aws_db_instance" "postgres_rds" {
  identifier           = "mi-postgres-rds"
  allocated_storage    = 20
  max_allocated_storage = 100
  storage_type         = "gp3"
  engine               = "postgres"
  engine_version       = "16.1"
  instance_class       = "db.t4g.micro"
  username             = "admin_user"
  password             = "MiPasswordSeguro2026!"
  skip_final_snapshot  = true
  publicly_accessible = false
}
```