---
layout: post
title: "Tutorial de MicroBlaze con Vivado y Vitis"
date: 2025-05-23
categories: [fpga, xilinx, vivado, vitis, tutorial]
tags: [microblaze, xilinx, digilent, vivado, vitis]
---

Este tutorial te guiará por el proceso de configuración de un proyecto en Vivado y Vitis para trabajar con el procesador **MicroBlaze**. Incluye la creación de un diseño de hardware con periféricos AXI GPIO conectados a botones y LEDs, y la creación de un proyecto baremetal en Vitis.

---

## 🧰 Requisitos

- Una placa FPGA Digilent compatible.
- Cables USB para programación y UART, fuente de alimentación.
- Instalación de Vivado y Vitis.
- Archivos de la placa desde el repositorio [`vivado-library`](https://github.com/Digilent/vivado-library).

---

## 🔧 Configuración del Proyecto en Vivado

1. **Abrir Vivado**:
   - En Linux:
     ```bash
     source <install_path>/Vivado/<version>/settings64.sh
     vivado
     ```

2. **Crear un nuevo proyecto**:
   - Selecciona `Exmaple Project`.
   
   ![MicroBlaze Example template]({{ "/assets/images/2025-05-23-Microblaze-tutorial/microblaze_vanilla_project_template.png   " | relative_url }}) 
   
   - Elige tu placa desde la pestaña `Boards`.

3. **Diseño con IP Integrator**:
   - Crea un *Block Design*.

    ![MicroBlaze Block design]({{ "/assets/images/2025-05-23-Microblaze-tutorial/microblaze_vanilla_block_diagram.png" | relative_url }}) 

4. **Conexiones clave**:
   - IP `MicroBlaze`.
   - Conectar reloj y reset.
   - Añadir interfaz UART (AXI Uartlite).
   - Conectar puertos AXI con `Run Connection Automation`.

5. **Genera el gitstream**:
   - Sintetiza y genera el bitstream.
---

## 🖥️ Proyecto Vitis

1. Exporta el diseño hardware desde Vivado.


![MicroBlaze Export Hardware]({{ "/assets/images/2025-05-23-Microblaze-tutorial/microblaze_vanilla_export_hw_xsa.png" | relative_url }})

2. Abre Vitis y crea un nuevo proyecto de aplicación.

3. Selecciona la plataforma exportada.

![MicroBlaze Vitis Platform]({{ "/assets/images/2025-05-23-Microblaze-tutorial/microblaze_vanilla_vitis_ready.png" | relative_url }})

4. Escribe un código en C que:
   - Lee el estado de los botones.
   - Enciende o apaga los LEDs.
   - Haz lo que quieras.


