#  Infraestructura Hardware - ClimAir S.L.

Este apartado del proyecto **ClimAir S.L.** detalla la base física sobre la que se asienta la infraestructura tecnológica de la empresa.

## Resumen de Equipamiento
- **Estaciones de Desarrollo (2):** Máximo rendimiento para ingeniería y compilación.
- **Puestos de Administración (3):** Equipos equilibrados para gestión y ERP.
- **Aula de Formación (10):** Terminales eficientes para aprendizaje técnico.
- **Servidores CPM (2):** Nodos críticos con alta disponibilidad y redundancia.

## Gestión de Almacenamiento
Para garantizar la integridad de los datos, se han implementado dos niveles de redundancia:
1. **RAID 1 (Espejo):** En estaciones de desarrollo para proteger el código local.
2. **RAID 6 (Doble Paridad):** En el servidor central (CPM) mediante controladora dedicada, permitiendo el fallo de hasta 2 discos sin pérdida de datos.

## Despliegue y Mantenimiento
- **Instalación:** Uso de imágenes maestras (Sysprep) y clonación masiva con **Clonezilla** para estandarizar el software.
- **Mantenimiento:** Protocolo de limpieza semestral y monitorización de temperaturas para prevenir el desgaste de componentes.

---
**Responsable:** Alejandro Sánchez Abellán
**Proyecto:** Intermodular ASIR 2025-2026
