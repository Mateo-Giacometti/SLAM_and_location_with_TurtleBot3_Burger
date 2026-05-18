<table>
  <tr>
    <td width="36%" align="center" valign="middle">
      <img src="https://www.robotis.us/wp-content/uploads/2020/01/TurtleBot3_logo-500x500.png" alt="TurtleBot3 logo" width="280" />
    </td>
    <td width="64%" valign="top">
      <h2>TP Final: Robótica Autónoma — SLAM y Localización</h2>
      <p><strong>Curso:</strong> Principios de la Robótica Autónoma</p>
      <p><strong>Institución:</strong> Universidad de San Andrés, Argentina</p>
      <p><strong>Programa:</strong> Ingeniería en Inteligencia Artificial</p>
      <p><strong>Año Académico:</strong> 4° año</p>
    </td>
  </tr>
</table>

---

## Descripción general

Proyecto final de robótica autónoma que implementa dos componentes clave en navegación robótica:

- **Parte A — SLAM (Simultaneous Localization and Mapping):** mapeo y localización simultánea de un TurtleBot3 en un entorno laberinto usando `slam_toolbox`, teleoperación y visualización en RViz2.
- **Parte B — AMCL (Adaptive Monte Carlo Localization):** localización probabilística con múltiples hipótesis de ubicación, filtrado por partículas y corrección mediante sensores (láser, odom).

Ambas partes utilizan **ROS2** como middleware de comunicación, **Gazebo** para simulación y **RViz2** para visualización de datos en tiempo real.

## Requisitos

### Entorno ROS2
- **ROS2 Humble** o superior
- **Colcon** (herramienta de build para ROS2)
- **Gazebo** (simulador)
- **RViz2** (herramienta de visualización)

### Dependencias de Python
```
rclpy
launch_ros
slam_toolbox
rviz2
teleop_twist_keyboard
turtlebot3_gazebo
nav2_common
gazebo_ros
tf_transformations
numpy
scipy
```

### Hardware / Simulación
- **TurtleBot3 Burger** (en simulación con Gazebo)

## Estructura del proyecto

```
tp_final_robotica/
├── README.md                                      # Este archivo
├── LICENSE                                        # Licencia Apache 2.0
├── Instrucciones.txt                              # Instrucciones de ejecución
├── python_slam_node_aux.py                        # Nodo auxiliar Python
├── src/
│   ├── turtlebot3_slam_mapper/                   # Paquete ROS2 — Parte A (SLAM)
│   │   ├── turtlebot3_slam_mapper/               # Módulo Python
│   │   ├── launch/
│   │   │   └── python_slam_maze.launch.py        # Launch file (SLAM + teleop + RViz)
│   │   ├── rviz/                                 # Configuraciones de RViz2
│   │   ├── config/                               # Parámetros y configuración
│   │   ├── package.xml                           # Definición del paquete
│   │   └── setup.py, setup.cfg                   # Configuración de instalación
│   │
│   └── my_py_amcl/                               # Paquete ROS2 — Parte B (AMCL)
│       ├── my_py_amcl/                           # Módulo Python
│       ├── launch/                               # Launch files
│       ├── rviz/                                 # Configuraciones de RViz2
│       ├── config/                               # Parámetros de localización
│       ├── param/                                # Parámetros de nodos
│       ├── maps/                                 # Mapas pre-generados
│       ├── package.xml                           # Definición del paquete
│       └── setup.py, setup.cfg                   # Configuración de instalación
│
├── report/                                        # Informe académico
│   └── Informe - TP Final - ...pdf               # Reporte completo del proyecto
├── instructions/                                  # Documentación adicional
├── build/, install/, log/                        # Directorios generados por colcon
└── .git/                                          # Repositorio Git
```

## Instalación y configuración

### 1. Clonar el repositorio
```bash
cd ~/colcon_ws/src
git clone <repository-url> tp_final_robotica
cd ~/colcon_ws
```

### 2. Instalar dependencias
```bash
rosdep install --from-paths src --ignore-src -r -y
```

### 3. Compilar el workspace
```bash
colcon build
source install/setup.bash
```

## Ejecución

### Parte A — SLAM (Mapeo y localización)

#### Terminal 1: Lanzar simulación SLAM
```bash
cd ~/colcon_ws
source install/setup.bash
colcon build --packages-select turtlebot3_slam_mapper
source install/setup.bash

export TURTLEBOT3_MODEL=burger
ros2 launch turtlebot3_slam_mapper python_slam_maze.launch.py
```

Esto abre:
- **Gazebo** con un TurtleBot3 Burger en un laberinto
- **RViz2** con visualización del mapa en construcción y nubes de puntos lidar
- Nodo **SLAM Toolbox** compilando el mapa en tiempo real

#### Terminal 2: Teleoperación
```bash
export TURTLEBOT3_MODEL=burger
ros2 run turtlebot3_teleop teleop_keyboard
```

**Controles:**
- `W / X`: acelerar/desacelerar hacia adelante
- `A / D`: girar izquierda/derecha
- `S`: detener
- `Space`: reset de velocidades

#### Guardar el mapa
Una vez que el robot ha explorado el laberinto, guardar el mapa:
```bash
ros2 run nav2_map_server map_saver_cli -f ~/map
```

Esto genera:
- `~/map.pgm` (imagen del mapa)
- `~/map.yaml` (metadatos del mapa)

---

### Parte B — AMCL (Localización con partículas)

#### Terminal 1: Compilar y lanzar AMCL
```bash
cd ~/colcon_ws
source install/setup.bash
colcon build --packages-select my_py_amcl
source install/setup.bash

export TURTLEBOT3_MODEL=burger
ros2 launch my_py_amcl <launch_file>
```

Típicamente:
```bash
ros2 launch my_py_amcl amcl_gazebo.launch.py
```

#### Terminal 2: Teleoperación
```bash
export TURTLEBOT3_MODEL=burger
ros2 run turtlebot3_teleop teleop_keyboard
```

En RViz2 se verá:
- La posición real del robot en Gazebo
- La nube de partículas (hipótesis de localización)
- Convergencia de partículas hacia la posición correcta

---

## Conceptos principales

### SLAM (Simultaneous Localization and Mapping)
- Construcción incremental de un mapa mientras se estima la localización del robot.
- En este proyecto: **SLAM Toolbox** usa datos del lidar (rango y ángulo) para crear un mapa 2D.
- La posición del robot se estima continuamente mientras se agrega nueva información topológica.

### AMCL (Adaptive Monte Carlo Localization)
- Localización probabilística basada en filtrado de partículas.
- Requiere un mapa **a priori** (generado en Parte A).
- Estima la pose del robot comparando lecturas de lidar actuales con el mapa conocido.
- Adapta el número de partículas dinámicamente según la confianza.

### Componentes ROS2 usados
- **rclpy:** cliente Python para ROS2
- **tf2_ros:** transformaciones espaciales (frame graph)
- **sensor_msgs:** mensajes de sensores (LaserScan, PointCloud)
- **nav_msgs:** mensajes de navegación (OccupancyGrid, Path)
- **geometry_msgs:** mensajes de geometría (Twist, Pose, PoseArray)
- **visualization_msgs:** marcadores para visualización en RViz2

---

## Tecnologías y herramientas

| Categoría | Herramienta / Librería |
|-----------|------------------------|
| **Middleware Robótico** | ROS2 Humble, rclpy |
| **Algoritmos** | SLAM Toolbox, AMCL (Monte Carlo Localization) |
| **Simulación** | Gazebo, turtlebot3_gazebo |
| **Visualización** | RViz2 |
| **Lenguaje** | Python 3 |
| **Cálculo Científico** | NumPy, SciPy |
| **Build System** | Colcon, Ament (Python) |
| **Control** | teleop_twist_keyboard |
| **Navegación** | nav2_common, nav2_map_server |
| **Transformaciones** | tf2_ros, tf_transformations |

---

## Salida esperada

### Parte A — SLAM
- **Gazebo:** simulación del TurtleBot3 en un entorno laberinto
- **RViz2:** mapa 2D construido en tiempo real, lecturas del lidar, trayectoria del robot
- **Consola:** logs de SLAM Toolbox mostrando número de scans, optimizaciones de grafo

### Parte B — AMCL
- **Gazebo:** robot en el laberinto conocido
- **RViz2:** nube de partículas (inicialmente dispersas) converge a la posición real del robot
- **Console:** logs de AMCL mostrando número efectivo de partículas, likelihood

---

## Troubleshooting

### Error: `Could not load display plugin ...`
```bash
sudo apt-get install ros-humble-rviz2
```

### Error: `slam_toolbox not found`
```bash
sudo apt-get install ros-humble-slam-toolbox
```

### Error: `turtlebot3_gazebo not found`
```bash
sudo apt-get install ros-humble-turtlebot3-gazebo
```

### El mapa no se genera o actualiza lentamente
- Verificar que el lidar está publicando datos en `/scan`
- Aumentar la velocidad del robot o reducir la velocidad de simulación en Gazebo
- Ajustar los parámetros de SLAM en `config/`

### Las partículas no convergen en AMCL
- Aumentar `population_per_meter` en los parámetros de AMCL
- Verificar que el mapa cargado (`maps/`) coincide con la simulación en Gazebo
- Asegurar que el lidar está correctamente calibrado (noise, range)

---

## Referencias académicas

- **SLAM Toolbox:** https://github.com/StanleyInnovation/slam_toolbox
- **Nav2 AMCL:** https://navigation.ros.org/configuration/packages/nav2_amcl/amcl/index.html
- **ROS2 Official Documentation:** https://docs.ros.org
- **TurtleBot3 Manual:** https://emanual.robotis.com/docs/en/platform/turtlebot3/overview/

---

## Autores

- **Mateo Giacometti**
- **Tiziano Leví Martín Bernal**

## Licencia

Este proyecto se distribuye bajo la **Apache License 2.0**. Véase el archivo `LICENSE` para más detalles.

## Contacto

Para preguntas o sugerencias, abrir un issue en el repositorio.
