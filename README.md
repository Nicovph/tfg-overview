# TEAslator

Aplicación web de apoyo a la comprensión de mensajes escritos, desarrollada
como Trabajo de Fin de Grado.

TEAslator combina interpretación mediante un modelo de lenguaje, autenticación
federada y apoyo visual opcional. El proyecto incorpora medidas de seguridad,
minimización de datos, pruebas para seleccionar el modelo de lenguaje y pruebas 
automatizadas.

> Este repositorio presenta el proyecto y el trabajo realizado. El código
> fuente se mantiene privado.

## Objetivo

TEAslator busca facilitar la comprensión de mensajes cuyo significado puede
depender del contexto, como expresiones ambiguas, ironía o lenguaje indirecto.

La aplicación ofrece una explicación del posible significado y una reformulación
más clara y directa. Cuando el usuario lo desea, puede acompañar la respuesta con
pictogramas.

El proyecto está concebido como una herramienta de apoyo cognitivo, especialmente
para personas con trastorno del espectro autista, aunque puede utilizarse en otros
contextos de comunicación escrita.

## Funcionalidades implementadas

### Autenticación federada

- Inicio de sesión con Google mediante OpenID Connect.
- Flujo de autorización con PKCE y validación de los tokens de identidad.
- Gestión de sesiones y cierre de sesión.
- Gestión administrativa de usuarios, permisos y revocación de acceso.

La aplicación utiliza la identidad proporcionada por Google y no ofrece
autenticación mediante contraseñas locales.

### Interpretación mediante un modelo de lenguaje

- Interpretación de un mensaje principal.
- Incorporación opcional de un mensaje anterior y otro posterior como contexto.
- Indicación de la relación entre los autores de los mensajes.
- Explicación del posible significado y reformulación más directa.
- Indicación de cuándo puede ser necesario aportar más contexto.
- Señales orientativas de posible ironía, ambigüedad, lenguaje indirecto,
  lenguaje ofensivo, agresión o ciberacoso.

La integración utiliza un modelo servido por Groq. Las respuestas siguen un
esquema estructurado y se validan antes de mostrarlas en la interfaz.

### Apoyo visual mediante ARASAAC

- Búsqueda de pictogramas a partir de conceptos derivados de la interpretación.
- Presentación de pictogramas y etiquetas.
- Ampliación de las imágenes en un diálogo.
- Atribución de los recursos utilizados.
- Conservación de la respuesta textual cuando no hay pictogramas adecuados
  o falla el servicio visual.

El apoyo visual puede activarse o desactivarse desde las preferencias.

### Preferencias personales

Cada usuario puede configurar:

- Nivel de detalle: breve, estándar o detallado.
- Activación del apoyo visual.
- Visualización de lenguaje ofensivo.
- Avisos de contenido.
- Tema claro, oscuro o según el sistema.

Las preferencias se conservan entre sesiones y solo pueden modificarse desde
la sesión del usuario al que pertenecen.

### Interfaz y experiencia de uso

- Interfaz adaptable a distintos tamaños de pantalla.
- Estados de carga, avisos y mensajes de error.
- Controles etiquetados y navegación mediante teclado.
- Gestión del foco al abrir y cerrar paneles y diálogos.
- Cancelación de solicitudes y control de respuestas que llegan después de
  cambiar el estado de la interfaz.
- Eliminación del contenido transitorio de la interfaz al cerrar sesión.

## Mi aportación

Me encargué del diseño y desarrollo de la aplicación, desde la interfaz hasta
los servicios del backend y el entorno de ejecución.

En concreto, mi trabajo incluyó:

- Diseño de la interfaz y elaboración de wireframes para ordenador y móvil.
- Desarrollo del frontend con React, TypeScript y Vite.
- Implementación de la API con Django y Django REST Framework.
- Diseño del modelo de datos y sus migraciones.
- Integración de Google OpenID Connect y gestión de sesiones.
- Implementación de las preferencias persistentes.
- Realización de un análisis de amenazas mediante STRIDE.
- Ejecución de pruebas para seleccionar el modelo de lenguaje.
- Integración de Groq, preparación de las solicitudes y validación de las
  respuestas del modelo.
- Desarrollo del procesamiento de contexto y de la minimización de
  identificadores antes del envío al proveedor.
- Integración de ARASAAC, selección de pictogramas y gestión de errores.
- Implementación de eventos de auditoría y límites de uso.
- Preparación del entorno con Docker Compose, PostgreSQL y Caddy.
- Desarrollo de pruebas del backend, de la interfaz y de flujos completos
  en navegador.
- Documentación de instalación, configuración y verificación del proyecto.

Mi aportación se centra en el diseño y la integración de estos componentes.
El modelo de lenguaje y los pictogramas proceden de proveedores externos.

## Arquitectura y tecnologías

| Área | Tecnologías y responsabilidad |
| --- | --- |
| Frontend | React, TypeScript y Vite |
| Backend | Django y Django REST Framework |
| Persistencia | PostgreSQL |
| Autenticación | Google OpenID Connect |
| Interpretación | Modelo de lenguaje servido por Groq |
| Apoyo visual | ARASAAC |
| Entorno de ejecución | Docker y Docker Compose |
| Proxy y HTTPS local | Caddy |
| Pruebas del backend | Herramientas de pruebas de Django |
| Pruebas del frontend | Vitest y React Testing Library |
| Pruebas en navegador | Playwright |

El frontend se comunica con el backend, que gestiona la autorización, las
preferencias, la validación y las integraciones externas.

Caddy termina el HTTPS local y dirige las solicitudes hacia los servicios
internos. PostgreSQL permanece en la red interna del backend.

## Seguridad y privacidad

La implementación incluye medidas en distintas capas:

### Identidad y control de acceso

- Validación de firma y atributos del token de identidad.
- Comprobación de los parámetros de seguridad del flujo de autenticación.
- Autorización basada en la sesión del usuario.
- Protección CSRF y configuración de cookies de sesión.
- Control de permisos para las funciones administrativas.

### Validación y control de uso

- Validación de las solicitudes y las respuestas de la API.
- Comprobación del esquema y de reglas adicionales para las respuestas del LLM.
- Límites de solicitudes y presupuestos de uso del proveedor.
- Tiempos de espera, reintentos y control de concurrencia.
- Tratamiento de respuestas inválidas y fallos de servicios externos.

### Minimización de datos

- Reducción de determinados identificadores antes de enviar el texto al
  proveedor del modelo.
- Procesamiento transitorio de mensajes, contexto e interpretaciones,
  sin persistir su contenido en la aplicación.
- Almacenamiento limitado a la identidad necesaria, sesiones, preferencias,
  eventos de auditoría y estado de las cuotas.
- Envío a la búsqueda de ARASAAC de conceptos derivados, en lugar del mensaje
  original.
- Información en la interfaz sobre el procesamiento externo del texto.

La minimización utiliza reglas de detección y no garantiza eliminar todos los
datos personales. El procesamiento transitorio en la aplicación tampoco implica
que los proveedores externos carezcan de sus propias políticas de tratamiento.

### Infraestructura y auditoría

- Separación de los roles de base de datos para aplicación, migraciones y pruebas.
- Ejecución de los servicios de aplicación con usuarios sin privilegios de root.
- Configuración de secretos fuera del código fuente.
- Eventos estructurados para autenticación, autorización, cambios de
  preferencias y determinados errores.
- Exclusión del contenido de los mensajes y de los tokens de autenticación
  de los eventos de auditoría.

Estas medidas forman parte de la implementación del MVP. Su presencia no
equivale a una certificación de seguridad ni garantiza la ausencia de
vulnerabilidades.

## Pruebas implementadas

El proyecto incluye pruebas para comprobar:

- El flujo de autenticación y la validación de tokens.
- El control de acceso y la protección CSRF.
- La consulta y modificación de preferencias.
- La validación de entradas y respuestas.
- La minimización de identificadores.
- Los límites de uso y determinadas situaciones de concurrencia.
- La gestión de errores de Groq y ARASAAC.
- El comportamiento de componentes, paneles y diálogos.
- La persistencia de preferencias y la limpieza del contenido al cerrar sesión.

También incluye un procedimiento automatizado para preparar un entorno aislado
y ejecutar pruebas en navegador a través de la pila HTTPS local.

Las pruebas que simulan proveedores externos permiten comprobar el comportamiento
de la aplicación ante respuestas y errores controlados. La validación con los
servicios reales requiere comprobaciones adicionales.

## Competencias relacionadas con ciberseguridad

Este proyecto me ha permitido trabajar con:

- Identidad federada y validación de tokens.
- Control de acceso y gestión de sesiones.
- Validación de datos en los límites entre componentes.
- Aislamiento de servicios y separación de privilegios.
- Auditoría de eventos de seguridad.
- Protección de información sensible y minimización de datos.
- Control del consumo de servicios externos.
- Pruebas de comportamientos relevantes para la seguridad.
- Análisis de amenazas mediante STRIDE de Microsoft, sobre diagramas de flujo de datos.

Estas áreas conectan con mi interés profesional por la ciberseguridad y
constituyen una base para seguir profundizando en el campo, donde mi interés
se centra más en actividades como pentesting, análisis de amenazas y monitorización
de tráfico de red.

## Estado y limitaciones

La versión desarrollada es un MVP académico destinado a desarrollo,
demostración y pruebas locales.

La interpretación generada puede ser incorrecta y no determina con certeza
la intención del autor. Las señales de contenido son orientativas y la
aplicación no realiza diagnósticos.

Se han implementado medidas de accesibilidad y pruebas de interacción.
La conformidad completa con un estándar de accesibilidad requiere una
evaluación específica.

La evolución hacia un entorno de producción requiere preparar el despliegue
de producción, ampliar las validaciones y revisar las condiciones de uso
de los servicios y recursos externos.

## Créditos y recursos externos

La autenticación utiliza Google OpenID Connect y la interpretación integra
un modelo de lenguaje servido por Groq.

Los pictogramas proceden de ARASAAC:
- Autor: Sergio Palao.
- Propiedad: Gobierno de Aragón, España.
- Licencia: CC BY-NC-SA.

Referencias oficiales:

- [Google OpenID Connect](https://developers.google.com/identity/openid-connect/openid-connect)
- [Respuestas estructuradas de Groq](https://console.groq.com/docs/structured-outputs)
- [ARASAAC](https://arasaac.org)
- [Condiciones de uso de ARASAAC](https://arasaac.org/terms-of-use)
