# Replicaciòn en MySQL

✅ Requisitos previos

Dos servidores MySQL
(pueden ser dos PCs, dos VMs o dos instancias)

MySQL Server 5.7 o 8.0

MySQL Workbench instalado

IPs accesibles entre ambos servidores

Puertos abiertos (3306)

🧱 Ejemplo de escenario
Rol	IP	Nombre
Master	192.168.1.10	mysql-master
Slave	192.168.1.11	mysql-slave
🔹 PASO 1: Configurar el MASTER
1️⃣ Edita el archivo my.cnf / my.ini

En el Master:

[mysqld]
server-id=1
log-bin=mysql-bin
binlog-do-db=midatabase


⚠️ server-id debe ser único

Reinicia MySQL.

2️⃣ Crear usuario de replicación

Desde MySQL Workbench:

CREATE USER 'repl'@'%' IDENTIFIED BY 'repl123';
GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%';
FLUSH PRIVILEGES;

3️⃣ Obtener datos de replicación
SHOW MASTER STATUS;


Guarda:

File → mysql-bin.000001

Position → 1234

🔹 PASO 2: Configurar el SLAVE
4️⃣ Edita my.cnf / my.ini

En el Slave:

[mysqld]
server-id=2
relay-log=relay-bin
read-only=1


Reinicia MySQL.

5️⃣ Conectar Slave al Master (Workbench)

En MySQL Workbench → conexión al Slave → Query:

CHANGE MASTER TO MASTER_HOST='192.168.1.66',
MASTER_USER='esclavo',
MASTER_PASSWORD='123456',
MASTER_LOG_FILE='DESKTOP-M41FSCL bin.000009',
MASTER_LOG_POS=157;
GET_MASTER_PUBLIC_KEY=1;

6️⃣ Iniciar replicación
START SLAVE;

🔹 PASO 3: Verificar estado
SHOW SLAVE STATUS\G


Debe mostrar:

Slave_IO_Running: Yes
Slave_SQL_Running: Yes

🧪 Prueba rápida

En el Master:

CREATE DATABASE test_replica;


En el Slave:

SHOW DATABASES;


Si aparece → 🎉 replicación funcionando
