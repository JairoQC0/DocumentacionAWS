# DocumentacionAWS

# 📘 Documentación del Proyecto  
## Despliegue Modular de WordPress en AWS para el Portafolio de Elena

---

## 🧠 Descripción General

Se desplegó una solución modular basada en CloudFormation para alojar un sitio WordPress en AWS, cumpliendo los requisitos de:

- **Seguridad**: RDS en subred privada, control de acceso con Security Groups.
- **Costo**: Instancias `t2.micro` y `db.t3.micro`, elegibles para capa gratuita.
- **Modularidad**: Arquitectura dividida en tres stacks reutilizables.
- **Disponibilidad**: Separación de componentes para facilitar escalado futuro.

---

## 🔧 Infraestructura Desplegada

### 1. Stack: `WordPress-Network`

- VPC con CIDR `10.0.0.0/16`
- 1 Subred pública (`10.0.1.0/24`)
- 2 Subredes privadas (`10.0.2.0/24`, `10.0.3.0/24`)
- Internet Gateway y tabla de rutas pública

**Salidas exportadas:**

- `WordPress-Network-VPCID`
- `WordPress-Network-PublicSubnetID`
- `WordPress-Network-PrivateSubnetIDs`

📸 **Captura 1:** Salidas de la pila `WordPress-Network`  
*(Incluye VPCID, PublicSubnetID, PrivateSubnetIDs)*

---

### 2. Stack: `WordPress-Security`

- Security Group `SG-EC2`: permite tráfico HTTP, HTTPS y SSH
- Security Group `SG-RDS`: sólo permite tráfico desde `SG-EC2` al puerto 3306

**Parámetros usados:**

- `NetworkStackName`: `WordPress-Network`

📸 **Captura 2:** Parámetros de la pila `WordPress-Security`  
*(Demuestra la dependencia de `WordPress-Network`)*

---

### 3. Stack: `WordPress-Application`

- **Instancia EC2**:
  - AMI Amazon Linux 2 (x86_64)
  - Instalación automática de WordPress vía `UserData`
- **RDS MySQL**:
  - Subnet Group con las dos subredes privadas
  - No accesible desde Internet
  - Contraseña segura `DBPassword` proporcionada

**Parámetros usados:**

- `NetworkStackName`: `WordPress-Network`
- `SecurityStackName`: `WordPress-Security`
- `DBPassword`: `********`
- `KeyPairName`: `MiClaveAWS`

📸 **Captura 3:** Salidas de la pila `WordPress-Application`  
*(Incluye `WebsiteURL` con el DNS público de EC2)*

---

## 🌐 Acceso Final

📸 **Captura 4:** Página de instalación de WordPress  
Accedida mediante el navegador usando el URL proporcionado.

---

## 🗺️ Diagrama de Arquitectura

Incluye:

- VPC
- Subred pública con EC2
- Subredes privadas con RDS
- Internet Gateway
- Security Groups (EC2 y RDS)

📎 Ver imagen: `diagrama-wordpress-aws.png`

---

## ✅ Conclusión

Este despliegue demuestra cómo aplicar Infraestructura como Código (IaC) en AWS para crear soluciones modulares, seguras y listas para producción.  
La separación en stacks permite mantenimiento, escalado y reutilización más sencilla.

---

## 📌 Próximos pasos sugeridos

- Agregar un balanceador de carga (ALB) para alta disponibilidad
- Habilitar backups automáticos en RDS
- Configurar HTTPS con ACM + ALB
- Migrar a ECS o Lightsail si se desea simplificar costos

---
