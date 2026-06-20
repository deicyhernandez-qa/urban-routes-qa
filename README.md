# Urban Routes — QA de Diseño, Validación de Formularios y Lógica de Reserva

![Status](https://img.shields.io/badge/status-completado-success)
![Casos de prueba](https://img.shields.io/badge/casos%20ejecutados-218-blue)
![Bugs encontrados](https://img.shields.io/badge/bugs%20documentados-23-red)
![Browsers](https://img.shields.io/badge/cross--browser-Chrome%20%7C%20Firefox-orange)
![Método](https://img.shields.io/badge/método-clases%20de%20equivalencia%20%7C%20valores%20límite-9cf)

## Contexto y problema de negocio

Urban Routes es una función de car-sharing (alquiler de automóvil compartido) dentro de una plataforma de movilidad urbana. El flujo crítico —desde elegir el vehículo, completar el formulario de pago, hasta confirmar la reserva— es el punto donde la plataforma genera ingresos. Cualquier inconsistencia visual o de validación en ese flujo puede traducirse directamente en reservas abandonadas o pagos rechazados.

Antes de esta ronda de pruebas, el equipo no contaba con verificación sistemática del diseño contra las especificaciones, ni con pruebas de validación rigurosas en los campos de pago (número de tarjeta y código de seguridad), dejando expuesta la posibilidad de que datos inválidos pasaran sin control.

**Pregunta central:**
> ¿El flujo de reserva de Urban Routes (selección de tarifa, agregar método de pago, confirmar y cancelar viaje) se comporta exactamente como especifica el diseño, en distintos navegadores y resoluciones, y rechaza correctamente los datos de pago inválidos?

## Qué hice

Ejecuté **218 verificaciones** distribuidas en 4 frentes de prueba, documentando **23 bugs** con su respectivo reporte de error:

| # | Frente de prueba | Casos ejecutados | Bugs encontrados | Técnica |
|---|---|---|---|---|
| 1 | Checklist de diseño (UI) — mapa, tarifas, ventanas emergentes | 183 verificaciones | 7 | Comparación contra diseño, cross-browser |
| 2 | Checklist "Método de pago" — número de tarjeta y código | 26 verificaciones | 14 | Clases de equivalencia y valores límite |
| 3 | Casos de prueba — lógica del botón "Reservar" | 5 casos formales | 2 | Casos de prueba estructurados (ID, precondiciones, pasos, resultado esperado) |
| 4 | Casos de prueba — lógica de reserva con campos vacíos | 4 casos formales | 0* | Casos de prueba estructurados |

*La hoja 4 incluye casos bloqueados por dependencias de bugs previos en el flujo.

### Cobertura cross-browser

Las pruebas de diseño se ejecutaron en dos entornos con resoluciones distintas para detectar inconsistencias específicas de renderizado:
- **Google Chrome** — 800x600
- **Mozilla Firefox** — 1920x1080

### Metodología de validación de formularios

Para los campos de pago (número de tarjeta, código de seguridad) apliqué **clases de equivalencia y valores límite**, probando sistemáticamente:
- Cantidad exacta de caracteres permitidos, un carácter de menos, un carácter de más
- Solo números, solo letras, combinaciones alfanuméricas, símbolos especiales
- Comportamiento del botón "Agregar" según el estado de validación de cada campo
- Comportamiento del botón "Cancelar" en cada estado del formulario

## Hallazgos clave

De los 23 bugs documentados, clasificados por severidad real asignada en cada reporte:

- **🔴 Crítico — Reserva sin método de pago (ERROR-23)**: si el usuario completa todos los campos obligatorios excepto el método de pago y hace clic en "Reservar", el sistema **confirma la reserva igual** (abre la ventana "Automóvil reservado" en lugar de exigir agregar la tarjeta). Es el hallazgo de mayor impacto del proyecto: representa un riesgo directo de viajes confirmados sin garantía de cobro.
- **🟠 Alta — Validación de pago insuficiente (14 bugs en la hoja "Método de pago", ERROR-8 a ERROR-21)**: el campo de número de tarjeta y el código de seguridad permitían, en distintos escenarios, longitudes incorrectas, letras, símbolos especiales y combinaciones alfanuméricas, además de no enmascarar los últimos 4 dígitos de la tarjeta guardada — sin que el formulario las rechazara de forma consistente.
- **🟠 Alta — Inconsistencia en la confirmación de reserva (ERROR-6, ERROR-22)**: la ventana "Automóvil reservado" mostraba el nombre de la tarifa en vez de la marca y placa del vehículo, y el flujo no abría la ventana de licencia de conducir cuando ese campo quedaba pendiente.
- **🟡 Baja/Media — Elementos visuales bloqueados en cascada (ERROR-1 a ERROR-7)**: varios bugs de diseño bloquearon la verificación de elementos dependientes (el ícono de vehículo no aprobado impidió validar el recuadro flotante asociado), evidenciando una dependencia frágil entre componentes de la interfaz.

### Distribución de severidad

| Severidad | Cantidad de bugs |
|---|---|
| Crítica | 1 |
| Alta | 15 |
| Media | 1 |
| Baja | 6 |

## Estructura del repositorio

```
urban-routes-qa/
├── checklists/
│   └── checklists-y-casos-de-prueba.xlsx     # Archivo fuente completo (4 hojas)
├── evidencias/
│   └── capturas-errores/
│       ├── ERROR-1-reporte-jira.png / ERROR-1-evidencia-navegadores.png
│       ├── ERROR-2-reporte-jira.png / ERROR-2-evidencia-navegadores.png
│       ├── ...
│       └── ERROR-23-reporte-jira.png / ERROR-23-evidencia-automovil-reservado.png
│       (46 archivos: reporte + evidencia visual para cada uno de los 23 bugs)
└── README.md
```

Cada bug cuenta con: descripción del problema, pasos de reproducción, resultado actual vs. esperado, configuración del entorno, prioridad, severidad, y capturas de pantalla en Chrome y/o Firefox tomadas directamente del reporte en Jira.

## Stack y herramientas

`Excel` `Jira` `Google Chrome DevTools` `Diseño/especificación visual` `Pruebas cross-browser`

## Sobre este proyecto

Proyecto de práctica desarrollado como parte de mi formación en QA (Bootcamp QA Engineer), aplicando metodología formal de pruebas de diseño, validación de formularios y casos de prueba estructurados sobre una funcionalidad de car-sharing.

---
**Deicy Hernández Zamora** — QA Tester
[LinkedIn](http://www.linkedin.com/in/deicy-hernandez-zamora) · [GitHub](https://github.com/deicyhernandez-qa) · licdeicyhernandez@gmail.com
