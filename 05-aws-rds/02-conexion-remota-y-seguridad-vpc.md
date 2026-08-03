# 02. Conexión Remota y Gestión de Seguridad (VPC y Security Groups)

## 📖 Teoría

La seguridad en RDS sigue el principio de **defensa en profundidad**, aislando la base de datos dentro de una red privada virtual (**VPC**).

### Componentes Clave de Red y Seguridad:

1. **Subnet Groups:** Colección de subredes privadas repartidas en múltiples Zonas de Disponibilidad (AZ) dentro de una VPC. RDS usa este grupo para asignar IPs privadas a la instancia.
2. **Security Groups (SG):** Actúan como un firewall virtual a nivel de instancia que controla el tráfico entrante (*Inbound*) y saliente (*Outbound*).
3. **Public Accessibility:**
   - `FALSE` (Recomendado): La instancia solo obtiene una IP privada interna. Solo es accesible desde recursos dentro de la VPC (ej. EC2, Lambdas, ECS) o mediante VPN/Bastion Host.
   - `TRUE`: Asigna una IP pública (no recomendado para producción).
4. **Bastion Host / SSH Tunneling:** Servidor intermedio en subred pública que permite conectarse a una RDS privada en subred privada a través de un túnel SSH cifrado.

---

## 💻 Ejemplos Prácticos

### A. Regla de Security Group para RDS (Inbound)

| Tipo | Protocolo | Puerto | Origen (Source) | Descripción |
| :--- | :--- | :--- | :--- | :--- |
| PostgreSQL | TCP | 5432 | `sg-0a1b2c3d4e5f6g7h8` | Permitir acceso **solo** a servidores con el SG de la aplicación |
| MySQL / Aurora | TCP | 3306 | `10.0.1.0/24` | Permitir acceso solo a la subred privada de la aplicación |

### B. Conexión a RDS Privada desde DBeaver mediante Túnel SSH (Bastion Host)

```text
[ Tu Computadora ] ---> ( SSH Puerto 22 ) ---> [ Bastion Host (Subred Pública) ]
                                                        |
                                            ( TCP Puerto 5432 / IP Privada )
                                                        v
                                            [ AWS RDS (Subred Privada) ]
```

### Comando de Túnel SSH en Terminal:

```bash
ssh -i "mi_clave.pem" -L 5433:mi-postgres-rds.c12345678.us-east-1.rds.amazonaws.com:5432 ec2-user@IP_PUBLIC_BASTION
```
