# taller1P2
evaluacion de conocimientos hasta el corte 2
# 🏦 Sistema de Gestión Bancaria (SGB)

**Asignatura:** Programación Orientada a Objetos  
**Tema:** Clases Abstractas, Interfaces, Excepciones, Herencia y Polimorfismo

---

## 📋 Objetivo de Aprendizaje

Desarrollar una aplicación Java que integre los siguientes conceptos de POO:

- Clases abstractas con métodos abstractos y concretos
- Interfaces y contratos de comportamiento
- Excepciones personalizadas (checked y unchecked)
- Herencia en múltiples niveles
- Polimorfismo (sobrescritura y sobrecarga)
- Encapsulamiento con validaciones en setters
- Arrays estáticos de tamaño fijo para gestión de datos
- Diagramas UML (Clases y Casos de Uso)

---

## 📖 Historias de Usuario (Product Backlog)

A continuación se listan **todas** las historias de usuario del sistema. Cada historia debe estar implementada y demostrada en la clase principal.

### 👤 Gestión de Clientes

| ID | Historia | Criterios de Aceptación | Prioridad | Story Points |
|----|----------|------------------------|-----------|:------------:|
| HU-01 | Como **cajero**, quiero registrar un cliente natural con sus datos personales para aperturar una cuenta en el sistema. | - El sistema valida que `numeroDocumento` no esté repetido entre los clientes registrados.<br>- Se lanza `CapacidadExcedidaException` si el banco alcanzó su límite de 200 clientes (`capacidadMaxima` se incluye en la excepción).<br>- Datos nulos o vacíos en cualquier setter lanzan `DatoInvalidoException` con el nombre del campo y el valor recibido.<br>- `email` debe contener `"@"`; `fechaNacimiento` no puede ser futura; de lo contrario se lanza `DatoInvalidoException`.<br>- Se invoca `registrarModificacion()` automáticamente al crear el cliente.<br>- Si `aceptaNotificaciones()` es `true`, se llama a `notificar()` con mensaje de bienvenida.<br>- El cliente implementa `Consultable`, `Notificable` y `Auditable`.<br>- Se retorna el cliente creado para usarlo en flujos posteriores. | Alta | 5 |
| HU-02 | Como **cajero**, quiero registrar un cliente empresarial con su NIT y representante legal para asociarle cuentas corporativas. | - `nit` y `razonSocial` no pueden ser nulos ni vacíos; lanzan `DatoInvalidoException`.<br>- Comparte la validación de capacidad máxima (200 clientes en total) con `ClienteNatural`.<br>- `obtenerTipo()` retorna `"ClienteEmpresarial"`.<br>- `obtenerDocumentoIdentidad()` retorna el NIT formateado.<br>- Implementa `Consultable`, `Notificable` y `Auditable`.<br>- Se registra modificación inicial al momento de la creación. | Alta | 5 |
| HU-03 | Como **asesor financiero**, quiero buscar un cliente por su documento de identidad para consultar su información y cuentas asociadas. | - `Banco.buscarCliente(String id)` lanza `ClienteNoEncontradoException` si no existe, incluyendo el `idCliente` buscado.<br>- Si el cliente existe, se retorna el objeto y se puede llamar a `obtenerResumen()`.<br>- `estaActivo()` indica si el cliente puede operar.<br>- Se imprime el historial de cuentas asociadas (máximo 5). | Alta | 3 |

### 🏦 Gestión de Cuentas

| ID | Historia | Criterios de Aceptación | Prioridad | Story Points |
|----|----------|------------------------|-----------|:------------:|
| HU-04 | Como **cajero**, quiero abrir una cuenta de ahorros a un cliente para que pueda realizar depósitos y retiros. | - `Banco.abrirCuenta()` lanza `ClienteNoEncontradoException` si el cliente no existe.<br>- Lanza `CapacidadExcedidaException` si el cliente ya tiene 5 cuentas o el banco alcanzó 500 cuentas.<br>- `tasaInteres` debe ser mayor a 0; de lo contrario lanza `DatoInvalidoException`.<br>- `calcularInteres()` retorna `saldo * tasaInteres / 12`.<br>- `getTipoCuenta()` retorna `"AHORROS"`.<br>- Implementa `Consultable`, `Transaccionable` y `Auditable`. | Alta | 5 |
| HU-05 | Como **cajero**, quiero abrir una cuenta corriente a un cliente para que pueda manejar sobregiros y comisiones de mantenimiento. | - Mismas validaciones de capacidad que HU-04.<br>- `montoSobregiro` y `comisionMantenimiento` deben ser mayores o iguales a 0.<br>- `calcularInteres()` retorna 0; cobra `comisionMantenimiento` mensual fija.<br>- `calcularComision(double monto)` aplica la tarifa definida.<br>- `getTipoCuenta()` retorna `"CORRIENTE"`. | Alta | 5 |
| HU-06 | Como **gerente de sucursal**, quiero abrir una cuenta de crédito a un cliente para asignarle un límite y calcular intereses sobre la deuda. | - Mismas validaciones de capacidad que HU-04.<br>- `limiteCredito` debe ser mayor a 0; `deudaActual` no puede superar `limiteCredito`.<br>- `calcularInteres()` retorna `deudaActual * tasaInteres / 12`.<br>- `retirar()` lanza `CuentaBloqueadaException` si está bloqueada; no lanza `SaldoInsuficienteException` (crédito permite deuda hasta el límite).<br>- `getTipoCuenta()` retorna `"CREDITO"`. | Alta | 5 |
| HU-07 | Como **asesor financiero**, quiero bloquear y desbloquear una cuenta para proteger al cliente ante actividades sospechosas. | - Al bloquear, `bloqueada = true`; cualquier `depositar()` o `retirar()` lanza `CuentaBloqueadaException`.<br>- Al desbloquear, `bloqueada = false` y la cuenta vuelve a operar normalmente.<br>- Se llama a `registrarModificacion(String usuario)` en ambas acciones.<br>- `verificarBloqueada()` es el método reutilizable que lanza la excepción. | Media | 3 |

### 💳 Gestión de Transacciones

| ID | Historia | Criterios de Aceptación | Prioridad | Story Points |
|----|----------|------------------------|-----------|:------------:|
| HU-08 | Como **cajero**, quiero registrar un depósito en una cuenta para incrementar el saldo del cliente. | - `depositar(double monto)` lanza `CuentaBloqueadaException` si la cuenta está bloqueada.<br>- El monto debe ser mayor a 0; de lo contrario lanza `DatoInvalidoException`.<br>- Se crea un objeto `Transaccion` con estado `COMPLETADA` y se agrega al historial.<br>- Si el historial ya tiene 20 transacciones, lanza `CapacidadExcedidaException`.<br>- Se actualiza `ultimaModificacion` de la cuenta. | Alta | 5 |
| HU-09 | Como **cajero**, quiero registrar un retiro en una cuenta para que el cliente pueda disponer de su dinero. | - `retirar(double monto)` lanza `CuentaBloqueadaException` si la cuenta está bloqueada.<br>- Lanza `SaldoInsuficienteException` si el monto supera el saldo; la excepción incluye `saldoActual` y `montoSolicitado`.<br>- En `CuentaAhorros`, incrementa `retirosMesActual`; si supera `maxRetirosMes` lanza `DatoInvalidoException`.<br>- Se aplica `calcularComision(monto)` y se descuenta del saldo.<br>- Se registra la transacción con estado `COMPLETADA`. | Alta | 8 |
| HU-10 | Como **asesor financiero**, quiero realizar una transferencia entre dos cuentas para mover fondos de forma segura. | - Se llama a `retirar()` en `cuentaOrigen` y a `depositar()` en `cuentaDestino`; se propagan las excepciones correspondientes.<br>- Si el retiro falla, el depósito no se ejecuta (operación atómica en memoria).<br>- Se crea una `Transaccion` con ambas cuentas y estado final `COMPLETADA`.<br>- `generarComprobante()` imprime id, cuentas, monto, fecha y estado.<br>- La transacción se agrega al historial de `cuentaOrigen`. | Alta | 8 |
| HU-11 | Como **asesor financiero**, quiero cambiar el estado de una transacción para reflejar su progreso en el sistema. | - `cambiarEstado(EstadoTransaccion nuevo)` valida las transiciones permitidas.<br>- Lanza `EstadoTransaccionInvalidoException` con estado origen y destino si la transición no es válida.<br>- Transiciones válidas: `PENDIENTE→PROCESANDO/RECHAZADA`, `PROCESANDO→COMPLETADA/RECHAZADA`, `COMPLETADA→REVERTIDA`.<br>- `RECHAZADA` y `REVERTIDA` son estados finales; cualquier cambio desde ellos lanza la excepción. | Alta | 5 |

### 👷 Gestión de Personal

| ID | Historia | Criterios de Aceptación | Prioridad | Story Points |
|----|----------|------------------------|-----------|:------------:|
| HU-12 | Como **gerente de sucursal**, quiero registrar un cajero en el sistema para asignarle un turno y una sucursal. | - `Banco.registrarEmpleado(Empleado e)` lanza `CapacidadExcedidaException` si se superan 50 empleados.<br>- `turno` no puede ser nulo; lanza `DatoInvalidoException`.<br>- `salarioBase` debe ser mayor a 0; `fechaContratacion` no puede ser futura.<br>- `calcularSalario()` retorna `salarioBase` + bono por `transaccionesDia`.<br>- `calcularBono()` es proporcional al número de transacciones del día. | Media | 3 |
| HU-13 | Como **gerente de sucursal**, quiero registrar un asesor financiero para que pueda gestionar clientes y transferencias. | - Misma validación de capacidad que HU-12.<br>- `comisionBase` y `metasMensuales` deben ser mayores a 0.<br>- `calcularSalario()` retorna `salarioBase + comisionBase` si supera `metasMensuales`, o solo `salarioBase` en caso contrario.<br>- `clientesAsignados` es un array de máximo 20; lanza `CapacidadExcedidaException` si se supera.<br>- Implementa `Consultable` y `Auditable`. | Media | 3 |
| HU-14 | Como **gerente de sucursal**, quiero registrarme en el sistema para gestionar la nómina y aprobar créditos. | - Misma validación de capacidad que HU-12.<br>- `presupuestoAnual` debe ser mayor a 0.<br>- `calcularSalario()` retorna `salarioBase + bono antigüedad + bono fijo de gerencia`.<br>- `empleadosACargo` es un array de máximo 30.<br>- Solo el `GerenteSucursal` puede llamar a `aprobarCredito()`; si lo intenta otro tipo de empleado, lanza `PermisoInsuficienteException` con la acción denegada.<br>- Implementa `Consultable` y `Auditable`. | Media | 5 |
| HU-15 | Como **gerente de sucursal**, quiero calcular la nómina total del banco para conocer el costo mensual del personal. | - `Banco.calcularNominaTotal()` itera sobre `empleados[]` usando polimorfismo sobre `calcularSalario()`.<br>- El resultado es la suma de los salarios de todos los empleados activos (`activo == true`).<br>- Se imprime el desglose por tipo de empleado (cajeros, asesores, gerentes).<br>- El método no lanza excepciones checked. | Media | 3 |

### 🔍 Intereses y Auditoría

| ID | Historia | Criterios de Aceptación | Prioridad | Story Points |
|----|----------|------------------------|-----------|:------------:|
| HU-16 | Como **sistema**, quiero calcular los intereses mensuales de todas las cuentas para actualizar los saldos automáticamente. | - `Banco.calcularInteresesMensuales()` itera sobre `cuentas[]` usando polimorfismo sobre `calcularInteres()`.<br>- `CuentaAhorros`: suma `saldo * tasaInteres / 12` al saldo.<br>- `CuentaCorriente`: descuenta `comisionMantenimiento`.<br>- `CuentaCredito`: suma `deudaActual * tasaInteres / 12` a la deuda.<br>- Se llama a `registrarModificacion("SISTEMA")` en cada cuenta procesada. | Media | 3 |
| HU-17 | Como **asesor financiero**, quiero consultar el historial de modificaciones de una cuenta para auditar los cambios realizados. | - `registrarModificacion(String usuario)` actualiza `ultimaModificacion` y `usuarioModificacion`.<br>- `obtenerUltimaModificacion()` retorna un `LocalDateTime` no nulo tras cualquier modificación.<br>- `obtenerUsuarioModificacion()` retorna el legajo del último usuario que modificó.<br>- `getHistorial()` retorna una copia del array interno con `System.arraycopy()`, nunca el original.<br>- Aplica a `CuentaCorriente`, `CuentaAhorros`, `CuentaCredito`, `ClienteNatural`, `ClienteEmpresarial` y `Banco`. | Baja | 3 |
| HU-18 | Como **cliente**, quiero recibir notificaciones del banco sobre movimientos en mis cuentas para estar informado de mis operaciones. | - `notificar(String mensaje)` imprime el mensaje si `aceptaNotificaciones()` retorna `true`.<br>- Si `aceptaNotificaciones()` retorna `false`, el método no realiza ninguna acción.<br>- `obtenerContacto()` retorna el email o teléfono registrado (no nulo).<br>- Aplica a `ClienteNatural` y `ClienteEmpresarial` mediante la interfaz `Notificable`.<br>- Se demuestra con al menos un cliente que acepta y uno que no acepta notificaciones. | Baja | 2 |

**Total story points: 80**

---

## 🗂️ Escenarios de la Clase Principal

La clase `SistemaBancarioDemo` debe demostrar obligatoriamente los siguientes escenarios:

| # | Escenario | Concepto evaluado | HU relacionada |
|---|-----------|-------------------|:--------------:|
| 1 | Registrar al menos 2 clientes naturales y 1 empresarial | Herencia, constructores con `super()` | HU-01, HU-02 |
| 2 | Abrir al menos una cuenta de cada tipo (corriente, ahorros, crédito) | Polimorfismo, arrays estáticos | HU-04, HU-05, HU-06 |
| 3 | Depósito exitoso y capturar `CuentaBloqueadaException` en cuenta bloqueada | Excepciones checked | HU-07, HU-08 |
| 4 | Retiro exitoso y capturar `SaldoInsuficienteException` | Excepciones checked, atributos del error | HU-09 |
| 5 | Transferencia entre dos cuentas | Interfaces `Transaccionable` | HU-10 |
| 6 | Recorrer `Empleado[]` e imprimir `calcularSalario()` en cada instancia | Polimorfismo de sobrescritura | HU-12, HU-13, HU-14 |
| 7 | Recorrer `Cuenta[]` e imprimir `calcularInteres()` en cada tipo | Polimorfismo de sobrescritura | HU-04, HU-05, HU-06 |
| 8 | Cambiar estado de transacción con transición inválida y capturar `EstadoTransaccionInvalidoException` | Excepciones unchecked | HU-11 |
| 9 | Aprobar crédito desde un `Cajero` y capturar `PermisoInsuficienteException` | Excepciones unchecked, control de acceso | HU-14 |
| 10 | Llamar a `notificar()` en cliente que acepta y en uno que no | Interfaces `Notificable` | HU-18 |
| 11 | Llamar a `registrarModificacion()` e imprimir `obtenerUltimaModificacion()` y `obtenerUsuarioModificacion()` | Interfaces `Auditable` | HU-17 |
| 12 | Calcular e imprimir la nómina total del banco | Polimorfismo, composición | HU-15 |

---

## 📁 Estructura de Paquetes

```
src/
├── modelo/
│   ├── abstractas/
│   │   ├── Persona.java
│   │   ├── Empleado.java
│   │   └── Cuenta.java
│   ├── personas/
│   │   ├── ClienteNatural.java
│   │   └── ClienteEmpresarial.java
│   ├── empleados/
│   │   ├── Cajero.java
│   │   ├── AsesorFinanciero.java
│   │   └── GerenteSucursal.java
│   ├── cuentas/
│   │   ├── CuentaCorriente.java
│   │   ├── CuentaAhorros.java
│   │   └── CuentaCredito.java
│   ├── banco/
│   │   ├── Banco.java
│   │   └── Transaccion.java
│   ├── interfaces/
│   │   ├── Consultable.java
│   │   ├── Transaccionable.java
│   │   ├── Auditable.java
│   │   └── Notificable.java
│   ├── excepciones/
│   │   ├── SistemaBancarioException.java
│   │   ├── SaldoInsuficienteException.java
│   │   ├── CuentaBloqueadaException.java
│   │   ├── ClienteNoEncontradoException.java
│   │   ├── CapacidadExcedidaException.java
│   │   ├── BancoRuntimeException.java
│   │   ├── DatoInvalidoException.java
│   │   ├── EstadoTransaccionInvalidoException.java
│   │   └── PermisoInsuficienteException.java
│   └── enums/
│       ├── EstadoTransaccion.java
│       ├── TipoCuenta.java
│       ├── Turno.java
│       └── TipoDocumento.java
└── principal/
    └── SistemaBancarioDemo.java
docs/
├── diagrama-casos-uso.png
└── diagrama-clases.png
```

---

## ✅ Lista de Verificación Pre-Entrega

- [ ] Las clases `Persona`, `Empleado` y `Cuenta` son abstractas y tienen al menos un método abstracto cada una
- [ ] Todas las clases concretas implementan los métodos abstractos de sus clases padre
- [ ] Todas las clases hijas usan `super()` en su constructor
- [ ] Los getters de arrays retornan una copia con `System.arraycopy()` — nunca el array original
- [ ] Las 4 interfaces tienen implementación real en todas las clases indicadas — sin cuerpos vacíos `{}`
- [ ] Existe la jerarquía de excepciones checked con base en `SistemaBancarioException`
- [ ] Existe la jerarquía de excepciones unchecked con base en `BancoRuntimeException`
- [ ] Los setters lanzan `DatoInvalidoException` ante datos nulos, vacíos o fuera de rango
- [ ] `cambiarEstado()` en `Transaccion` lanza `EstadoTransaccionInvalidoException` en transiciones inválidas
- [ ] La clase principal demuestra todos los 12 escenarios de la tabla anterior
- [ ] Los diagramas UML coinciden con el código implementado — mismas clases, atributos y relaciones
- [ ] El código compila y ejecuta sin errores

---

## 📦 Entregables Obligatorios

- Código fuente Java organizado en la estructura de paquetes indicada
- Diagrama de Casos de Uso (PNG, JPG o `.puml`) en la carpeta `/docs`
- Diagrama de Clases (PNG, JPG o `.puml`) en la carpeta `/docs`
- Este `README.md` con la tabla de historias de usuario completada

### Opcionales

- Documentación JavaDoc en todas las clases públicas
- Video corto (máximo 2 minutos) mostrando la ejecución y el manejo de excepciones en consola

