# Conociendo más de  MySQL && MariaDB

### Ver Usuarios  Disponibles

```mysql
SELECT HOST, USER SELECT_PRIV FROM  mysql.user;
```



### Creación de Usuarios 

La creación de un usuario en **MySQL o en MariaDB** es de **asignarle privilegios** para poder acceder a una **base de datos específica**.

```mysql
CREATE USER 'miusuario' IDENTIFIED BY 'mipassword';
```

> Crea un usuario y ademas le asigna un contraseña.



## Conceder permisos para poder acceder y usar el servidor MySQL

```mysql
GRANT USAGE ON *.* TO 'miusuario'@localhost IDENTIFIED BY 'mipassword';
```

> [** .* *] - El primer asterisco marca la Base de Datos, el segundo las tablas     
>
> PASSWORDS.*  -  Se especifica la BD "PASSWORD" y ( * ) todas las tablas 

##### Para permitir el acceso desde cualquier otra máquina que tenga acceso al servidor MySQL desde la red:

```mysql
GRANT USAGE ON *.* TO 'miusuario'@'%' IDENTIFIED BY 'mipassword';
```

> El usuario pueda trabajar con la base de datos desde cualquier ubicación tendremos que sustituir localhost por ‘**%**’



## Conceder todos los privilegios sobre la base de datos al usuario

```mysql
GRANT ALL privileges ON `basededatos`.* TO 'miusuario'@localhost;
```

```mysql
FLUSH PRIVILEGES;
```



## Verificar que nuestro nuevo usuario tiene los permisos correctos 

## Aplicar los cambios realizados

```mysql
SHOW GRANTS FOR 'miusuario'@localhost;     
+--------------------------------------------------------------+ 
| Grants for miusuario@localhost                                | 
+--------------------------------------------------------------+ 
| GRANT USAGE ON *.* TO 'miusuario'@'localhost'                 | 
| GRANT ALL PRIVILEGES ON `mibd`.* TO 'miusuario'@'localhost'   | 
+--------------------------------------------------------------+ 
2 rows in set (0,00 sec)
```



## Eliminar el usuario

```mysql
DROP USER miusuario@localhost;
```



## Cambiar contraseña a usuario ROOT en MariaDB

```mysql
UPDATE mysql.user SET plugin = 'mysql_native_password', Password = PASSWORD('secret') WHERE User = 'root';
```

```mysql
FLUSH PRIVILEGES;
```

