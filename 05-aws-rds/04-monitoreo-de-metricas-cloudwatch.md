# 04. Monitoreo de Métricas con CloudWatch y RDS Insights

## 📖 Teoría

El monitoreo continuo permite prevenir cuellos de botella, optimizar costos y garantizar la salud operativa del motor de base de datos.

---

### Herramientas de Monitoreo en RDS:

1. **Amazon CloudWatch Metrics (Métricas Básicas):**
   - Colecta métricas a nivel de hipervisor e infraestructura cada 60 segundos por defecto.
   - **Métricas Críticas:**
     - `CPUUtilization`: Porcentaje de CPU usado.
     - `FreeableMemory`: Memoria RAM disponible.
     - `FreeStorageSpace`: Espacio en disco restante.
     - `ReadIOPS` / `WriteIOPS`: Cantidad de operaciones I/O por segundo.
     - `WriteLatency` / `ReadLatency`: Tiempo medio de respuesta en E/S.
     - `ReplicaLag`: Retraso de sincronización en Read Replicas (en segundos).

2. **Enhanced Monitoring:**
   - Muestra métricas en tiempo real sobre el **sistema operativo de la instancia** con granularidad de hasta 1 segundo.
   - Permite identificar qué procesos específicos consumen CPU o RAM en el host.

3. **Performance Insights:**
   - Herramienta visual especializada en analizar la carga de trabajo del motor SQL (*Database Load*).
   - Mide la métrica **AAS (Average Active Sessions)** y clasifica la carga por:
     - Consultas específicas (`SQL Text`).
     - Eventos de espera (`Wait Events` ej. espera de I/O, bloqueo de locks, CPU).
     - Usuarios o hosts de origen.

---

## 💻 Ejemplos Prácticos

### A. Creación de una Alarma en CloudWatch para Espacio en Disco Crítico

```bash
aws cloudwatch put-metric-alarm \
    --alarm-name "RDS-Disco-Bajo" \
    --alarm-description "Alarma cuando el espacio en disco sea menor a 5 GB" \
    --metric-name FreeStorageSpace \
    --namespace AWS/RDS \
    --statistic Average \
    --period 300 \
    --threshold 5368709120 \
    --comparison-operator LessThanOrEqualToThreshold \
    --dimensions Name=DBInstanceIdentifier,Value=mi-postgres-rds \
    --evaluation-periods 1
```

### B. Diagnóstico de Problemas de Rendimiento con Performance Insights

[ Performance Insights Dashboard ]

DB Load (AAS)
 ^
 |    ███  [Wait: IO:DataFileRead] -> (I/O lento por falta de índice)
 |    ███  [Wait: CPU]            -> (Consulta ineficiente consumiendo CPU)
 |  █████  [Wait: Lock:RowExclusive] -> (Bloqueos/Transacciones largas)
 +------------------------------------------------------------> Tiempo