# Vuelos
## Creacion de tablas
```sql
CREATE TABLE Pais (
id VARCHAR(5) PRIMARY KEY,
nombre VARCHAR(30)
);

CREATE TABLE Ciudad (
id VARCHAR(5) PRIMARY KEY,
nombre VARCHAR(30),
idpais VARCHAR(5),
FOREIGN KEY (idpais) REFERENCES Pais(id)
);

CREATE TABLE Aeropuerto (
id VARCHAR(5) PRIMARY KEY,
nombre VARCHAR(50),
idciudad VARCHAR(5),
FOREIGN KEY (idciudad) REFERENCES Ciudad(id)
);

CREATE TABLE Aerolinea (
id INT AUTO_INCREMENT PRIMARY KEY,
nombre VARCHAR(30)
);

CREATE TABLE Fabricante (
id INT AUTO_INCREMENT PRIMARY KEY,
nombre VARCHAR(40)
);

CREATE TABLE Estado (
id INT AUTO_INCREMENT PRIMARY KEY,
nombre VARCHAR(30)
);

CREATE TABLE Modelo (
id INT AUTO_INCREMENT PRIMARY KEY,
nombre VARCHAR(30),
idfabricante INT,
FOREIGN KEY (idfabricante) REFERENCES Fabricante(id)
);

CREATE TABLE Avion (
nromatricula VARCHAR(30) UNIQUE,
capacidad INT,
fechafabricacion DATE,
idestado INT,
modelo VARCHAR(30),
idaerolinea INT,
idmodelo INT,
PRIMARY KEY (nromatricula),
FOREIGN KEY (idestado) REFERENCES Estado(id),
FOREIGN KEY (idaerolinea) REFERENCES Aerolinea(id),
FOREIGN KEY (idmodelo) REFERENCES Modelo(id)
);

CREATE TABLE Trayecto (
id INT AUTO_INCREMENT PRIMARY KEY,
fechatrayecto DATE,
valor DOUBLE,
ciudadOrigen VARCHAR(5),
ciudadDestino VARCHAR(5),
FOREIGN KEY (ciudadOrigen) REFERENCES Ciudad(id),
FOREIGN KEY (ciudadDestino) REFERENCES Ciudad(id)
);

CREATE TABLE Escala (
id INT AUTO_INCREMENT PRIMARY KEY,
idtrayecto INT,
idavion VARCHAR(30),
nnrovuelo INT,
idaeropuerto VARCHAR(5),
FOREIGN KEY (idtrayecto) REFERENCES Trayecto(id),
FOREIGN KEY (idavion) REFERENCES Avion(nromatricula),
FOREIGN KEY (idaeropuerto) REFERENCES Aeropuerto(id)
);

CREATE TABLE Gates (
id INT PRIMARY KEY,
nropuerta INT,
idaeropuerto VARCHAR(5),
FOREIGN KEY (idaeropuerto) REFERENCES Aeropuerto(id)
);

CREATE TABLE Roltripulacion (
id INT PRIMARY KEY,
nombre VARCHAR(30)
);

CREATE TABLE Empleado (
id INT PRIMARY KEY,
nombre VARCHAR(50),
idrol INT,
fechaingreso DATE,
idaerolinea INT,
idaeropuerto VARCHAR(5),
FOREIGN KEY (idrol) REFERENCES Roltripulacion(id),
FOREIGN KEY (idaerolinea) REFERENCES Aerolinea(id),
FOREIGN KEY (idaeropuerto) REFERENCES Aeropuerto(id)
);

CREATE TABLE TrayectoTripulacion (
idempleado INT,
idescala INT,
FOREIGN KEY (idempleado) REFERENCES Empleado(id),
FOREIGN KEY (idescala) REFERENCES Escala(id)
);

CREATE TABLE Revision (
id INT PRIMARY KEY,
fecha DATE,
idavion VARCHAR(30),
FOREIGN KEY (idavion) REFERENCES Avion(nromatricula)
);

CREATE TABLE RevEmpleado (
idempleado INT,
idrevision INT,
FOREIGN KEY (idempleado) REFERENCES Empleado(id),
FOREIGN KEY (idrevision) REFERENCES Revision(id)
);

CREATE TABLE DetalleRevision (
idrevision INT,
descripcion TEXT,
fecharev DATE,
idempleado INT,
FOREIGN KEY (idrevision) REFERENCES Revision(id),
FOREIGN KEY (idempleado) REFERENCES Empleado(id)
);

CREATE TABLE Tipodocumento (
id INT AUTO_INCREMENT PRIMARY KEY,
nombre VARCHAR(30)
);

CREATE TABLE Clientes (
id VARCHAR(20) PRIMARY KEY,
edad INT,
idtipodoc INT,
FOREIGN KEY (idtipodoc) REFERENCES Tipodocumento(id)
);

CREATE TABLE Reservaviaje (
id INT AUTO_INCREMENT PRIMARY KEY,
fecha DATE,
idtrayecto INT,
FOREIGN KEY (idtrayecto) REFERENCES Trayecto(id)
);

CREATE TABLE Tarifasvuelo (
id INT PRIMARY KEY,
descripcion VARCHAR(50),
valor DOUBLE
);

CREATE TABLE DetalleReserva (
id INT AUTO_INCREMENT PRIMARY KEY,
idcliente VARCHAR(20),
idtarifa INT,
valor DOUBLE,
FOREIGN KEY (idcliente) REFERENCES Clientes(id),
FOREIGN KEY (idtarifa) REFERENCES Tarifasvuelo(id)
);

CREATE TABLE Asientos (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nromatricula VARCHAR(30),
    numero_asiento VARCHAR(10),
    clase VARCHAR(20),
    FOREIGN KEY (nromatricula) REFERENCES Avion(nromatricula)
);

CREATE TABLE Equipaje (
    id INT AUTO_INCREMENT PRIMARY KEY,
    iddetalle_reserva INT,
    peso DECIMAL(5,2),
    tipo VARCHAR(20),
    FOREIGN KEY (iddetalle_reserva) REFERENCES DetalleReserva(id)
);

CREATE TABLE ServiciosAdicionales (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(50),
    descripcion TEXT,
    costo DECIMAL(10,2)
);

CREATE TABLE ReservaServicios (
    iddetalle_reserva INT,
    idservicio INT,
    fecha_solicitud DATE,
    FOREIGN KEY (iddetalle_reserva) REFERENCES DetalleReserva(id),
    FOREIGN KEY (idservicio) REFERENCES ServiciosAdicionales(id),
    PRIMARY KEY (iddetalle_reserva, idservicio)
);

CREATE TABLE AsignacionAsientos (
    iddetalle_reserva INT,
    idasiento INT,
    fecha_asignacion DATETIME,
    FOREIGN KEY (iddetalle_reserva) REFERENCES DetalleReserva(id),
    FOREIGN KEY (idasiento) REFERENCES Asientos(id),
    PRIMARY KEY (iddetalle_reserva, idasiento)
);

CREATE TABLE HistorialReservas (
    id INT AUTO_INCREMENT PRIMARY KEY,
    idreserva INT,
    estado_anterior VARCHAR(20),
    estado_nuevo VARCHAR(20),
    fecha_cambio DATETIME,
    motivo TEXT,
    FOREIGN KEY (idreserva) REFERENCES Reservaviaje(id)
);

CREATE TABLE ProgramacionVuelos (
    id INT AUTO_INCREMENT PRIMARY KEY,
    idescala INT,
    hora_programada_salida DATETIME,
    hora_programada_llegada DATETIME,
    idgate_salida INT,
    idgate_llegada INT,
    FOREIGN KEY (idescala) REFERENCES Escala(id),
    FOREIGN KEY (idgate_salida) REFERENCES Gates(id),
    FOREIGN KEY (idgate_llegada) REFERENCES Gates(id)
);

CREATE TABLE MantenimientoProgramado (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nromatricula VARCHAR(30),
    fecha_programada DATE,
    tipo_mantenimiento VARCHAR(50),
    estado VARCHAR(20),
    FOREIGN KEY (nromatricula) REFERENCES Avion(nromatricula)
);

```
## Atributos faltantes
```sql
ALTER TABLE Clientes ADD COLUMN nombre VARCHAR(50) NOT NULL;
ALTER TABLE Clientes ADD COLUMN apellido VARCHAR(50) NOT NULL;
ALTER TABLE Clientes ADD COLUMN email VARCHAR(100);
ALTER TABLE Clientes ADD COLUMN telefono VARCHAR(20);

ALTER TABLE Avion ADD CONSTRAINT chk_capacidad CHECK (capacidad > 0);
ALTER TABLE Gates ADD CONSTRAINT chk_nropuerta CHECK (nropuerta > 0);
ALTER TABLE Clientes ADD CONSTRAINT chk_edad CHECK (edad > 0);

ALTER TABLE Trayecto MODIFY COLUMN valor DECIMAL(10,2);
ALTER TABLE Tarifasvuelo MODIFY COLUMN valor DECIMAL(10,2);

ALTER TABLE TrayectoTripulacion 
ADD PRIMARY KEY (idempleado, idescala);

CREATE INDEX idx_fechatrayecto ON Trayecto(fechatrayecto);
CREATE INDEX idx_fecharevision ON Revision(fecha);

ALTER TABLE Escala 
ADD COLUMN hora_real_salida DATETIME,
ADD COLUMN hora_real_llegada DATETIME,
ADD COLUMN estado VARCHAR(20);

ALTER TABLE Reservaviaje 
ADD COLUMN estado VARCHAR(20) DEFAULT 'PENDIENTE',
ADD COLUMN fecha_modificacion DATETIME;


ALTER TABLE DetalleReserva 
ADD COLUMN idreserva INT,
ADD FOREIGN KEY (idreserva) REFERENCES Reservaviaje(id);

```

Hecho por (Luis Alberto Talero Martinez)

> [!NOTE]
> complicaciones

> [!IMPORTANT]  
> las opciones faltantes se agregaron en el ultimo analisis que hice :) 
