# Acerca de Cron

**Cron** es un proceso de sistema que es usado para ejecutar tareas en segundo plano cada cierto tiempo, cron requiere un archivo llamado **crontab** que contiene la lista de tareas a ser ejecutadas en un tiempo en particular. Todos estos trabajos son ejecutados en segundo plano en un tiempo especificado.



```bash
* * * * * comando ha ser ejecutado
- - - - -
| | | | |
| | | | ----- Día de la semana (0 - 7)
| | | ------- Mes (1 - 12)
| | --------- Día del mes (1 - 31)
| ----------- Hora (0 - 23)
------------- Minuto (0 - 59)
```

```
Cada minuto: En intervalos de entre 0 a 59.
Cada hora: En intervalos de entre 0 a 23.
Cada día: En intervalos de entre 0 a 31.
Cada mes: En intervalos de entre 0 a 12 (0==12 y 12 == Diciembre).
Cada día de la semana: En intervalos de entre 0 a 7 (0==7 y 7 == domingo).
```



Ejecutar una tarea del Crontab cada 5 minutos.

```
*/10 * * * * /home/fedora/sh/backup.sh
```

Ejecutar cada hora.

```
0 * * * * /home/$user/Scripts/brightness.sh
```

Ejecutar una tarea cada 7 horas.

```
0 19 * * * python hello.py >> a.txt
```

Ejecutar cuando se inicia el sistema.

```
@reboot /home/$user/scripts/sensors.sh
```

Ver trabajos cron ejecutándose en tu sistema.

```
crontab -l
```

Para agregar un nuevo trabajo cron al **crontab**.

```
crontab -e
```

Elegir tu editor favorito para añadir tareas en crontab.

```
EDITOR=nvim crontab -e
```



## Uso de los operadores en una tarea para el Crontab

Con los operadores es posible especificar varios valores en un campo; existen tres posibles valores:

- **El asterisco (\*):** Este operador abarca todos los valores posibles; un asterisco en un campo minutos es igual a ejecutar el script cada minuto.
- **la coma (,):** Este operador permite especificar una lista de valores; por ejemplo si queremos ejecutar un comando los lunes y miércoles: "1,3" en el campo de los días.
- **El guión (-):** Este operador especifica una serie de valores de manera agrupada con un cota inferior y una superior; por ejemplo si quisiéramos ejecutar un comando de lunes a viernes podríamos colocar el siguiente valor en el campo día: "1-5", que es equivalente a 1,2,3,4,5.

```
0 22 * * 1-5 /home/fedora/sh/backup.sh
```



## Fuentes

[Code](https://code.tutsplus.com/es/tutorials/managing-cron-jobs-using-python--cms-28231)

[DesarrolloLibre](https://www.desarrollolibre.net/blog/linux/ejecutar-script-automaticamente-con-cron-en-linux)

[LinuxAdictos](https://www.linuxadictos.com/ejecutar-un-comando-linux-cada-cierto-tiempo-con-watch.html)

[Digitalocean](https://www.digitalocean.com/community/tutorials/how-to-use-cron-to-automate-tasks-ubuntu-1804)

