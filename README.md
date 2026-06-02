<div align="center">
<picture>
    <source srcset="https://imgur.com/5bYAzsb.png" media="(prefers-color-scheme: dark)">
    <source srcset="https://imgur.com/Os03JoE.png" media="(prefers-color-scheme: light)">
    <img src="https://imgur.com/Os03JoE.png" alt="Escudo UNAL" width="350px">
</picture>

<h3>Curso de Robótica 2026-I</h3>

<h1>Informe Laboratorio #3</h1>

<h2>Profesores: <br>Pedro Fabián Cárdenas Herrera <br> Manuel Felipe Carranza Montenegro<br></h2>

</div>

# Integrantes
1. Juan Andrés Moreno Benavides [jumorenobe@unal.co](Jumorenobe)
2. Mateo Ramos Cujer [mramoscu@unal.edu.co](MateoKGR)


# Índice

1. [Cuadro comparativo de manipuladores](#1-cuadro-comparativo-de-manipuladores)
2. [Diferencias entre los tipos de trayectorias en EPSON RC+ 7.0](#2-diferencias-entre-los-tipos-de-trayectorias-en-epson-rc-70)
3. [Configuración de la posición Home](#3-configuracion-de-la-posicion-home)
4. [Procedimiento de movimientos manuales del manipulador](#4-procedimiento-de-movimientos-manuales-del-manipulador)
5. [Control y niveles de velocidad en la interfaz](#5-control-y-niveles-de-velocidad-en-la-interfaz)
6. [Funcionalidades y comunicación de EPSON RC+ 7.0](#6-funcionalidades-y-comunicacion-de-epson-rc-70)
7. [Análisis comparativo de software (EPSON RC+, RoboDK y RobotStudio)](#7-analisis-comparativo-de-software-epson-rc-robodk-y-robotstudio)
8. [Diseño técnico del gripper neumático por vacío](#8-diseno-tecnico-del-gripper-neumatico-por-vacio)
9. [Diagrama de flujo de la trayectoria (Patrón de Caballo)](#9-diagrama-de-flujo-de-la-trayectoria-patron-de-caballo)
10. [Plano de planta del espacio de trabajo](#10-plano-de-planta-del-espacio-de-trabajo)
11. [Código desarrollado en SPEL+](#11-codigo-desarrollado-en-spel)
12. [Videos demostrativos (Simulación e Implementación física)](#12-videos-demostrativos-simulacion-e-implementacion-fisica)

## Cuadro comparativo de manipuladores

| Característica | **EPSON T3-401S** | **Motoman MH6** | **ABB IRB140** |
|----------------|--------------------|------------------|----------------|
| **Tipo de robot** | SCARA (4 ejes) | Articulado (6 ejes) | Articulado (6 ejes) |
| **Grados de libertad (DOF)** | 4 | 6 | 6 |
| **Carga máxima (Payload)** | 3 kg | 6 kg | 6 kg |
| **Alcance máximo** | 400 mm | 1373 mm | 810 mm |
| **Repetibilidad** | ±0.02 mm | ±0.08 mm | ±0.03 mm |
| **Velocidad máxima** | Hasta 4500 mm/s (ejes XY) | 230°/s (articulaciones) | 225°/s (articulaciones) |
| **Montaje** | De mesa (compacto) | En piso, pared o techo | En piso, pared o invertido |
| **Controlador** | EPSON RC+ 7.0 | NX100 / DX100 | IRC5 Compact |
| **Fuente de potencia** | 200–240 V CA monofásico | 200–230 V CA trifásico | 200–600 V CA trifásico |
| **Aplicaciones típicas** | Ensamble electrónico, empaque, pick and place | Soldadura, manipulación de piezas, paletizado | Ensamble, manipulación de materiales, mantenimiento de máquinas |
| **Peso del robot** | ~27 kg | ~130 kg | ~98 kg |
| **Ventajas principales** | Compacto, rápido, bajo costo y fácil de integrar | Alta carga, gran alcance, estructura robusta | Preciso, compacto, ideal para espacios reducidos |
| **Limitaciones** | Alcance corto, solo 4 ejes | Menor precisión que ABB | Mayor costo que Epson |
| **Software asociado** | EPSON RC+ 7.0 | MotoSim EG / NX100 | RobotStudio |
| **Comunicación con PC** | USB / Ethernet | Ethernet / RS-232 | Ethernet / USB |

## Diferencias entre los tipos de trayectorias en EPSON RC+ 7.0

* **Movimiento Punto a Punto (Comando `Go`):** El robot simplemente busca la forma más rápida de llegar de un punto inicial a un punto final. No le importa si el recorrido es una línea recta o no, ya que su prioridad es que todas las articulaciones lleguen al mismo tiempo al destino. Es ideal cuando el espacio está libre y solo nos importa llegar rápido.
* **Movimiento Lineal (Comando `Move`):** A diferencia del movimiento punto a punto, aquí el robot sí se obliga a viajar en una **línea recta perfecta** en el espacio. Es muy útil cuando el camino exacto importa, como al insertar una pieza en un agujero o mover algo sin desviarse.
* **Movimiento de Salto (Comando `Jump`):** Hace un movimiento en 3D muy característico: sube, se desplaza horizontalmente y luego baja. Está pensado específicamente para tareas de "Pick and Place" (como mover los huevos en nuestra cubeta), ya que es muy eficiente y evita chocar con los objetos que están en la superficie de trabajo.
* **Movimiento Circular (Comando `Arc`):** Permite que el robot describa una trayectoria curva. Le damos unos puntos intermedios como referencia y el robot traza un arco suave. Es práctico para rodear obstáculos o seguir contornos curvos.