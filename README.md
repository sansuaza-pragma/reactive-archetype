# Arquetipo Spring Boot WebFlux - Arquitectura Hexagonal

## Descripción General

Este arquetipo implementa un microservicio bancario utilizando **Spring Boot 3.3.7** con **WebFlux** (programación reactiva) y **Java 21**, siguiendo los principios de **Arquitectura Hexagonal**. El proyecto está diseñado para operaciones de préstamos y depósitos, proporcionando una base sólida y escalable para aplicaciones financieras.

### Características Principales

- **Arquitectura Hexagonal**: Separación clara entre dominio, aplicación e infraestructura
- **Programación Reactiva**: Uso de Spring WebFlux para manejo asíncrono y no bloqueante
- **Multi-persistencia**: Soporte para SQL Server (R2DBC) y MongoDB reactivo
- **Integración Azure**: Key Vault para secretos y Application Insights para telemetría
- **Mensajería**: Azure Service Bus para comunicación asíncrona
- **Observabilidad**: Logging estructurado y métricas de aplicación
- **Testing**: Cobertura completa con JUnit 5 y Reactor Test

### Stack Tecnológico

- **Java 21** - Lenguaje de programación
- **Spring Boot 3.3.7** - Framework principal
- **Spring WebFlux** - Programación reactiva
- **R2DBC SQL Server** - Acceso reactivo a base de datos relacional
- **MongoDB Reactive** - Base de datos NoSQL reactiva
- **Azure Key Vault** - Gestión de secretos
- **Azure Application Insights** - Telemetría y monitoreo
- **Azure Service Bus** - Mensajería asíncrona
- **Lombok** - Reducción de código boilerplate
- **Gradle** - Gestión de dependencias y construcción

## 1. Arquitectura Hexagonal

La Arquitectura Hexagonal (también conocida como Arquitectura de Puertos y Adaptadores) es un patrón arquitectónico que promueve la separación de preocupaciones al dividir la aplicación en capas o componentes independientes. El núcleo de la aplicación (dominio) está aislado de los detalles de infraestructura y tecnología, permitiendo que los componentes externos interactúen con el núcleo a través de puertos (interfaces) y adaptadores (implementaciones concretas).

* **Aplicación en el Arquetipo:**
    * **Dominio (domain):** contiene la lógica de negocio pura, incluyendo entidades, servicios de dominio y repositorios (interfaces).
    * **Aplicación (application):** orquesta los casos de uso y actúa como intermediario entre el dominio y el mundo exterior.
    * **Infraestructura (infrastructure):** implementa los detalles técnicos y de infraestructura, como controladores, repositorios concretos, adaptadores y configuraciones.

![ArquitecturaHexagonal](imagenes/ArquitecturaHexagonal.png)

## 2. Estructura del Proyecto

```
src/
├── main/
│   ├── java/com/mercantil/operationsandexecution/
│   │   ├── BankingApplication.java                    # Clase principal de la aplicación
│   │   ├── crosscutting/                             # Funcionalidades transversales
│   │   │   ├── constants/                            # Constantes de la aplicación
│   │   │   ├── domain/                               # Configuración y modelos de dominio transversal
│   │   │   ├── events/                               # Configuración de eventos (Service Bus)
│   │   │   ├── exception/                            # Manejo global de excepciones
│   │   │   ├── infraestructure/                      # Infraestructura común
│   │   │   │   ├── common/                           # Configuraciones comunes
│   │   │   │   ├── dataproviders/                    # Proveedores de datos transversales
│   │   │   │   │   ├── logging/                      # Implementación de logging
│   │   │   │   │   └── noqsl/                        # Configuración MongoDB
│   │   │   │   └── entrypoints/                      # Puntos de entrada comunes
│   │   │   ├── logging/                              # Servicios de logging
│   │   │   ├── restclient/                           # Cliente REST genérico
│   │   │   └── secrets/                              # Gestión de secretos (Key Vault)
│   │   └── loansanddeposits/                         # Módulo de negocio
│   │       ├── application/                          # Capa de aplicación
│   │       │   └── usecase/                          # Casos de uso
│   │       ├── domain/                               # Capa de dominio
│   │       │   ├── models/                           # Modelos de dominio
│   │       │   ├── ports/in/                         # Puertos de entrada
│   │       │   └── services/                         # Servicios de dominio
│   │       └── infraestructure/                      # Capa de infraestructura
│   │           ├── dataproviders/                    # Proveedores de datos
│   │           │   ├── database/                     # Acceso a bases de datos
│   │           │   │   ├── mongo/                    # Implementación MongoDB
│   │           │   │   └── sqlserver/                # Implementación SQL Server
│   │           │   └── restclient/                   # Clientes REST específicos
│   │           └── entrypoints/                      # Puntos de entrada
│   │               └── rest/                         # Endpoints REST
│   └── resources/
│       ├── application-local.yaml                    # Configuración local
│       └── logback-spring.xml                        # Configuración de logging
└── test/                                             # Tests unitarios e integración
    └── java/com/mercantil/operationsandexecution/
        ├── crosscutting/
        ├── loansanddeposits/
        └── BankingApplicationTests.java
```

## 3. Inicio Rápido

### Prerrequisitos

- Java 21
- Gradle 8.11+
- SQL Server (local o remoto)
- MongoDB (opcional)
- Azure Key Vault (para producción)

### Configuración Local

1. **Clonar el repositorio**
   ```bash
   git clone <repository-url>
   cd ms-archetype-springboot-webflux-java
   ```

2. **Configurar base de datos**
   - Actualizar `application-local.yaml` con las credenciales de SQL Server
   - Configurar MongoDB si es necesario

3. **Ejecutar la aplicación**
   ```bash
   ./gradlew bootRun
   ```

4. **Verificar salud de la aplicación**
   ```bash
   curl http://localhost:8080/actuator/health
   ```

### Endpoints Principales

- **Health Check**: `GET /actuator/health`
- **Crear Préstamo**: `POST /loansanddeposits/create`
- **Métricas**: `GET /actuator/metrics`

## 4. Arquitectura y Patrones

### 4.1. Arquitectura Hexagonal

La aplicación sigue los principios de Arquitectura Hexagonal con tres capas principales:

- **Dominio**: Lógica de negocio pura, modelos y puertos
- **Aplicación**: Casos de uso y orquestación
- **Infraestructura**: Implementaciones técnicas, controladores y adaptadores

### 4.2. Programación Reactiva

Utiliza Spring WebFlux para:
- Manejo no bloqueante de requests
- Acceso reactivo a bases de datos (R2DBC, MongoDB Reactive)
- Procesamiento asíncrono de eventos

### 4.3. Patrones Implementados

- **Repository Pattern**: Abstracción del acceso a datos
- **Use Case Pattern**: Encapsulación de lógica de aplicación
- **Adapter Pattern**: Integración con servicios externos
- **Factory Pattern**: Creación de proveedores de datos
- **Observer Pattern**: Manejo de eventos asíncronos servicios de dominio para ejecutar operaciones de negocio específicas.
    * Los servicios de dominio encapsulan la lógica de negocio, manteniendo el dominio coherente y evitando que los controladores o casos de uso tengan lógica de negocio.

### 4.3. Patrón de Fábrica (Factory Pattern)

El Patrón de Fábrica proporciona una forma de crear objetos sin exponer la lógica de creación al cliente, y se utiliza para encapsular la creación de objetos.

* **Aplicación en el Arquetipo:**
    * **Clase **`DataProviderFactory.java`** en **`infrastructure/dataproviders`**:**
        * Esta clase es responsable de crear instancias de los proveedores de datos (por ejemplo, SQL, NoSQL, Blob Storage) según sea necesario.

* **Cómo se Aplica:**
    * La fábrica decide qué implementación de **`IDataProvider`** o **`Repository`** utilizar en tiempo de ejecución, ocultando los detalles de creación al resto de la aplicación.
    * Facilita la inyección de dependencias y el cambio de implementaciones sin modificar el código cliente.

### 4.4. Patrón Adaptador (Adapter Pattern)

El Patrón Adaptador permite que clases con interfaces incompatibles trabajen juntas, convirtiendo la interfaz de una clase en otra que el cliente espera.

* **Aplicación en el Arquetipo:**
    * **Adaptadores en **`infrastructure/dataproviders`**:**
        * Blob Storage Adapter:
          **`StorageService.java`** implementa **`IStorageService`**
        * Proveedores de Datos:
          **`NoSqlDataProvider.java`** implementa **`INoSqlDataProvider`**
          **`SqlDataProvider.java`** implementa **`ISqlDataProvider`**

    * **Email Adapters:**
        * **`emailadapters`** actualmente no se han creado las clases, actua como adaptador para servicios de correo electrónico.

* **Cómo se Aplica:**
    * Los adaptadores implementan las interfaces esperadas por el dominio o la aplicación y traducen las llamadas a las implementaciones concretas.
    * Permiten que el dominio y la aplicación no dependan de detalles específicos de infraestructura o servicios externos.

### 4.5. Patrón Fachada (Facade Pattern)

El Patrón Fachada proporciona una interfaz simplificada a un conjunto de interfaces en un subsistema, haciendo que el subsistema sea más fácil de usar.

* **Aplicación en el Arquetipo:**
    * **Servicios de Dominio como Fachadas:**
        * **`LoanService.java`** actúa como una fachada que encapsula las operaciones relacionadas con préstamos, proporcionando métodos sencillos al resto de la aplicación.

* **Cómo se Aplica:**
    * Los servicios de dominio ofrecen una interfaz clara y simplificada para operaciones complejas que pueden involucrar múltiples repositorios, entidades y lógica de negocio.
    * Facilitan la interacción con el dominio desde los casos de uso y controladores.

    
### 4.6. Patrón Inyección de Dependencias (Dependency Injection)

La Inyección de Dependencias es un patrón donde las dependencias (servicios, repositorios, etc.) son proporcionadas a una clase en lugar de ser creadas por ella misma. Promueve la modularidad y facilita las pruebas.

* **Aplicación en el Arquetipo:**
    * **Uso de Anotaciones como **`@Autowired:`****
        * Inyección de servicios y repositorios en controladores y casos de uso.
    * **Configuraciones en **`infrastructure/configuration`**:**
        * Clases como **`RepositoryConfig.java`** y **`DataSourceConfig.java`** definen cómo se configuran e inyectan las dependencias.

* **Cómo se Aplica:**
    * Las clases no crean sus propias dependencias, sino que las reciben a través del constructor o propiedades, facilitando la inversión de control.
    * Mejora la testeabilidad al permitir inyectar implementaciones simuladas o mock durante las pruebas.


### 4.7. Patrón Builder (Constructor)

El Patrón Builder separa la construcción de un objeto complejo de su representación, permitiendo que el mismo proceso de construcción pueda crear diferentes representaciones.

* **Aplicación en el Arquetipo:**
    * **Construcción de Objetos DTO o Entidades:**
        * Se esta utilizando builders para crear instancias de **`LoanDTO`** o **`LoanSQLEntity`** con un enfoque más legible y mantenible.

* **Cómo se Aplica:**
    * Facilita la creación de objetos con múltiples propiedades, especialmente cuando algunas son opcionales o requieren validación.
    * Mejora la legibilidad y evita constructores con muchos parámetros.

### 4.8. Patrón Decorador (Decorator Pattern)

El Patrón Decorador permite agregar dinámicamente responsabilidades adicionales a un objeto.

* **Aplicación en el Arquetipo:**
    * **Servicios de Mensajes y Logging:**
        * Se esta decorando servicios para añadir funcionalidades adicionales como **`logging`** o **`manejo de errores`**.

* **Cómo se Aplica:**
    * Extiende la funcionalidad de los servicios sin modificar su código original.
    * Permite agregar características de manera flexible y modular.

### 4.14. Aplicación de los Principios SOLID

Además de los patrones de diseño, se esta aplicando los principios **`SOLID`**:

* **Single Responsibility Principle (SRP):**
    * Cada clase tiene una única responsabilidad, como **`LoanService`** que maneja la lógica de préstamos.

* **Open/Closed Principle (OCP):**
    * Las clases están abiertas para extensión pero cerradas para modificación, permitiendo agregar nuevas funcionalidades sin cambiar el código existente.

* **Liskov Substitution Principle (LSP):**
    * Las clases derivadas pueden sustituir a sus clases base sin alterar el correcto funcionamiento de la aplicación.

* **Interface Segregation Principle (ISP):**
    * Se crean interfaces específicas y pequeñas, evitando depender de métodos que no se utilizan.

* **Dependency Inversion Principle (DIP):**
    * Se depende de abstracciones (**`interfaces`**) en lugar de implementaciones concretas, facilitando el cambio de implementaciones sin afectar al código que las utiliza.
