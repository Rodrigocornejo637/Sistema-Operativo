# Informe de Arquitectura de Núcleo (Kernel Architecture)

### 2.1. Cuadro Comparativo de Arquitecturas
A continuación se detallan las ventajas, desventajas y características de las distintas estructuras de núcleo frente a los requerimientos de software:

| Criterio | Núcleo Monolítico | Núcleo Modular | Micro Núcleo (Microkernel) |
| :--- | :--- | :--- | :--- |
| **Ubicación de Servicios** | Todos los servicios (FS, controladores, red) se ejecutan dentro del espacio del núcleo. | El núcleo base gestiona servicios esenciales; los controladores se cargan como módulos dinámicos. | Solo funciones esenciales (IPC, memoria) quedan en el núcleo; los servicios se ejecutan en el espacio de usuario. |
| **Rendimiento** | **Muy Alto:** Llamadas directas en memoria sin cambio de contexto. | **Alto:** Cercano al monolítico, evita cuellos de botella en comunicación. | **Moderado/Bajo:** Mayor sobrecarga por la comunicación constante entre procesos (IPC). |
| **Flexibilidad** | **Baja:** Requiere recompilar el núcleo para agregar o modificar funciones. | **Alta:** Carga y descarga de módulos en tiempo de ejecución sin reiniciar el sistema. | **Alta:** Los servicios en espacio de usuario se pueden modificar o reiniciar independientemente. |
| **Complejidad** | **Alta:** Gran cantidad de código interconectado internamente. | **Media:** Código base estructurado mediante módulos independientes. | **Variable:** Código del núcleo simple, pero la arquitectura general del sistema es más compleja. |
| **Costo de Comunicación (IPC)** | **Mínimo:** Uso directo de punteros y memoria compartida dentro del núcleo. | **Mínimo:** Los módulos cargados se comunican dentro del mismo espacio del núcleo. | **Alto:** Requiere múltiples cambios de contexto entre usuario y núcleo para reenviar mensajes. |

---

### 2.2. Resolución de Casos Prácticos de Diseño

#### Caso A: Software para Dispositivo IoT con Recursos Limitados (Industrial o Biomédico)
* **Selección de Arquitectura:** Micro Núcleo (Microkernel).
* **Justificación Técnica:** En dispositivos IoT, la estabilidad y la seguridad son prioritarias frente a la velocidad pura. Al ejecutar controladores de sensores y protocolos de red en el espacio de usuario, si un controlador falla o sufre un error de memoria, el servicio colapsa de forma aislada sin afectar al resto del sistema. Esto evita un colapso general (Kernel Panic) y permite reiniciar únicamente el módulo afectado, garantizando alta disponibilidad con un consumo reducido de memoria en el núcleo.

#### Caso B: Servidor de Alta Velocidad (Alto volumen de peticiones)
* **Selección de Arquitectura:** Núcleo Modular.
* **Justificación Técnica:** Un servidor con alto volumen de peticiones por segundo requiere minimizar la sobrecarga en la comunicación entre el sistema de archivos, la pila de red y el hardware. El núcleo modular ofrece el máximo rendimiento al ejecutar estas tareas dentro del espacio del núcleo (evitando el *overhead* de IPC de un micro núcleo), manteniendo la flexibilidad de cargar dinámicamente controladores de red especializados o filtros de seguridad según la demanda sin necesidad de reiniciar el servidor.
