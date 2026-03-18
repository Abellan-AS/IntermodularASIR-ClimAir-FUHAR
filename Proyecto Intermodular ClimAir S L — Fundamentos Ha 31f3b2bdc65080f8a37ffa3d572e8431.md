# Proyecto Intermodular: ClimAir S.L. — Fundamentos Hardware

<aside>
📌

***PROYECTO INTERMODULAR: ClimAir S.L.***

Fundamentos Hardware

**Responsable:** Alejandro Sánchez Abellán

*2025-2026*

</aside>

<aside>
🧭

**Índice interactivo**

</aside>

---

## Despliegue y Almacenamiento

> Para que nuestra empresa esté avanzada tecnológicamente, vamos a plantear una **estrategia de despliegue profesional**. Ligándolo con la Implantación de Sistemas Operativos, en este apartado vamos a hablar de las imágenes maestras, automatización y, de cómo configuramos un RAID (Practicado en clase y en nuestro manual FUHAR) para que los datos de la empresa estén seguros.
> 

---

## 1. Despliegue de Sistemas Operativos y Software

En esta fase, definimos cómo pasamos del hardware desnudo a estaciones de trabajo funcionales. No realizaremos instalaciones manuales individuales; utilizaremos técnicas de **clonación y despliegue masivo.**

### Estrategia de Instalación: Pendrive Maestro y Clonación

Para los 10 equipos del Aula y los 5 de oficina, utilizaremos un sistema de **Imagen Maestra**:

1. **Preparación de la ISO:** Usaremos la herramienta **Rufus** para crear unidades USB de arranque (bootables) con las versiones más recientes de los SO.
2. **Sysprep y Captura:** Configuramos un equipo de cada sección (uno de Desarrollo, uno de Aula) con todo el software necesario. Usamos la herramienta `Sysprep` de Windows para "limpiar" el SID (identificador único) y creamos una imagen del disco usando **Clonezilla**.
3. **Despliegue Masivo:** Mediante el USB con Clonezilla, volcamos esa imagen en el resto de equipos idénticos. Esto reduce el tiempo de instalación de 2 horas a apenas 15 minutos por equipo.

Ligado como digo a ISO vamos a implementar lo siguiente:

| **Departamento** | **Sistema Operativo** | **Justificación** |
| --- | --- | --- |
| **Administración / Dirección** | **Windows 11 Pro** | Necesario por compatibilidad con software de gestión (ERP/Contabilidad) y cifrado. |
| **Desarrollo** | **Ubuntu Desktop (Dual Boot con Win11)** | Entorno nativo para programadores; mayor eficiencia en compilación. |
| **Aula de Formación** | **Windows 11 Home** | Facilidad de uso para los alumnos y compatibilidad con software didáctico. |
| **Servidores (CPM)** | **Ubuntu Server** | Estabilidad total, gestión de dominios y servicios de red 24/7. |

---

## 2. Almacenamiento y Redundancia

Aquí vamos a tratar el desarrollo y la implementación de sistemas de almacenamiento eficaces y que generen viabilidad si surge algún problema.
Para ello vamos a empezar por nuestras máquinas de la sección de desarrollo, seguido del almacenamiento de datos en el CPM, explicando qué sistema vamos a gestionar, por qué lo vamos a hacer, y para qué.

En primer lugar un Raid 1 para gestionar la sección de Desarrollo**.**

![dd.jpg](dd.jpg)

---

### 1. ¿Qué es el RAID 1 (Mirroring)?

El RAID 1, también conocido como **espejo (mirroring)**, consiste en duplicar la información en dos unidades de almacenamiento idénticas. Todo lo que el procesador escribe en el "Disco A", se escribe exactamente igual y al mismo tiempo en el "Disco B".

### ¿Por qué lo vamos a instalar?

- **Alta Disponibilidad:** Si el Disco 1 muere, el sistema sigue funcionando con el Disco 2 sin que se note el corte.
- **Seguridad:** Tienes una copia exacta del arranque del sistema en todo momento.

---

### 2. ¿Dónde lo vamos a instalar?

El RAID 1 lo instalaremos exclusivamente en la sección de desarollo.

- Los desarrolladores manejan código local, entornos de programación y bases de datos de prueba que no siempre están subidas al servidor al minuto.
- **Hardware:** Usaremos **2 discos SSD NVMe de 1TB** cada uno.
- **Instalación:** Se configura desde la **BIOS/UEFI** de la placa base (RAID por firmware), lo vemos en el siguiente punto.

---

### 3. ¿Cómo lo vamos a instalar?

Hay dos formas de hacerlo:

**Paso A: Configuración en la BIOS/UEFI**

1. **Entrar en la BIOS:** Encendemos el servidor y pulsamos la tecla (F2, Supr o F12) para entrar en la configuración.
2. **Modo SATA:** Cambiamos el modo de los controladores de disco de "AHCI" a **"RAID"**.
3. **Entrar en la utilidad RAID:** Al reiniciar, aparecerá un menú rápido (ej. Intel Rapid Storage o Dell PERC). Entramos ahí.
4. **Crear Volumen:** Seleccionamos los dos discos físicos y elegimos **RAID 1**. El sistema nos avisará de que se borrarán los datos. Aceptamos y ahora el servidor verá esos dos discos como **un solo disco lógico**.

**Paso B: Instalación del Sistema Operativo**

1. **Arrancar desde USB:** Metemos el USB (creado con  Rufus) con la ISO de  Ubuntu Server.
2. **Detección de disco:** Durante la instalación, cuando te pregunte "¿Dónde quieres instalar?", solo verás **un disco** (el volumen RAID que creamos antes).
3. **Drivers:** A veces, Windows no reconoce la controladora RAID a la primera. En ese caso, le damos a "Cargar controlador" desde otro pendrive con los drivers de la placa base.

### Paso C: Verificación en el Sistema

Una vez instalado el SO, entramos y descargamos el software de gestión (como *Intel RST*). Desde ahí podemos ver que ambos discos están funcionando correctamente.

---

*Si el RAID 1 era el seguro de vida para que el servidor arranque, el RAID 6 es la caja fuerte donde guardaremos los proyectos, bases de datos de clientes y backups de la empresa.*

![ee.jpg](bd6e9926-55af-4b83-b7de-0a26b4a78e96.png)

### 1. ¿Qué es el RAID 6?

El RAID 6 es una evolución del RAID 5. Distribuye los datos por todos los discos (esto se llama *striping*), pero la clave es que calcula **dos bloques de paridad distintos** y los reparte por todas las unidades.

La regla del RAID 6:

- **Mínimo de discos:** Necesitas al menos **4 discos**.
- **Tolerancia a fallos:** Es su mayor ventaja. Puedes perder **2 discos al mismo tiempo** y la red seguirá funcionando sin perder datos.

### 2. ¿Por qué elegimos RAID 6 y no RAID 5?

El punto crítico de un RAID no es cuando funciona bien, sino cuando **un disco falla y hay que sustituirlo (Rebuild)**.

1. **El riesgo del Rebuild:** Cuando quitas un disco roto y pones uno nuevo, la controladora tiene que leer *todos* los demás discos para reconstruir los datos. Este proceso estresa mucho a los discos sanos.
2. **La pesadilla de IT:** En un RAID 5, si durante esa reconstrucción falla otro disco (algo común porque suelen ser de la misma edad), **pierdes toda la información de la empresa**.
3. **La solución:** En **RAID 6**, como tenemos doble paridad, si falla un segundo disco durante la reconstrucción, no pasa nada. El sistema sigue aguantando.

### 3.  ¿Dónde lo vamos a instalar?

En el Servidor Principal / CPM

Reservamos el RAID 6 para el corazón de la empresa. Aquí es donde se guardan las facturas, los datos de los clientes y los backups.

- **¿Por qué ahí?** El servidor es el único sitio donde tenemos al menos 4 bahías de disco. El RAID 6 es ideal aquí porque priorizamos la **seguridad extrema** sobre la velocidad.
- **Hardware:** Usaremos **4 discos HDD Enterprise de 4TB** cada uno.
- **Instalación:** Aquí lo haremos de forma más profesional: usaremos una **Tarjeta Controladora RAID dedicada** (una tarjeta PCIe que se pincha en la placa del servidor),

### 4 ¿Cómo lo vamos a instalar?

**Paso A: Instalación Física (Hardware)**

1. **Inserción:** Pinchamos la tarjeta en una ranura **PCIe x8 o x16** de la placa base del servidor.
2. **Conexión de Datos:** Conectamos los cables **SAS/SATA** que salen de la tarjeta directamente a los **4 discos duros** de 4TB.
    - *Nota:* Los discos ya no se conectan a la placa base, sino a la tarjeta.
3. **Batería de Respaldo (BBU):** Si la tarjeta tiene una pequeña batería (BBU), la instalamos. Esto evita que, si se va la luz, se pierdan los datos que estaban en la memoria caché de la tarjeta.

---

**Paso 2: Configuración del Volumen Lógico (Firmware)**

Una vez encendido el servidor, ignoramos el arranque normal y entramos en el menú de la tarjeta:

1. **Acceso al Menú:** Durante el arranque, aparecerá un mensaje tipo: *"Press <Ctrl+R> to enter Configuration Utility"*.
2. **Inicialización:** Seleccionamos los 4 discos físicos detectados por la tarjeta.
3. **Creación del "Virtual Drive":**
    - **Nivel de RAID:** Seleccionamos **RAID 6**.
    - **Stripe Size:** Lo dejamos en **64KB o 128KB** (estándar para servidores de archivos).
    - **Write Policy:** Seleccionamos **Write Back** (gracias a la batería instalada, es muy seguro y rápido).
4. **Inicialización Completa:** La tarjeta preparará los discos. Al terminar, para el sistema operativo, esos 4 discos han desaparecido y solo existe **una unidad lógica de 8TB**.

---

**Paso 3: Instalación del SO con Drivers**

1. **Carga de Drivers:** Al empezar la instalación de Ubuntu, es probable que **no aparezca ningún disco**.
2. **Inyección:** Debes tener un pendrive con los drivers específicos de la tarjeta controladora. Le das a Cargar controlador, seleccionas el archivo, y  aparecerá el volumen de 8TB listo para particionar.

**Ventajas de usar la tarjeta** 

- **Independencia:** Si la placa base del servidor se quema, se puede sacar la tarjeta y los discos, pincharlos en otro servidor igual, y los datos siguen ahí.
- **Rendimiento:** El cálculo de la doble paridad del RAID 6 es complejo; al hacerlo la tarjeta, la CPU del servidor queda libre al 100% para gestionar las peticiones de los usuarios.
- **Seguridad:** La caché protegida por batería  asegura que incluso en un apagón repentino, los datos en tránsito se escriban correctamente.

---

## **3. Resumen del Almacenamiento en Climair S.L.**

- **En Desarrollo: RAID 1 (2x SSD 1TB) - Prioridad: Velocidad y disponibilidad local.**
- **En Servidor CPM: RAID 6 (4x HDD 4TB) - Prioridad: Seguridad máxima y gran capacidad.**

> Se han implementado dos filosofías de almacenamiento redundante. En el **entorno de desarrollo**, se ha configurado un **RAID 1 por firmware** para proteger el trabajo diario de los ingenieros. Por otro lado, en el **nodo central (CPM)**, se ha optado por un **RAID 6 gestionado por hardware dedicado**, garantizando que el volumen de datos crítico de la empresa permanezca íntegro incluso en el peor escenario de fallo simultáneo de dos unidades."
>