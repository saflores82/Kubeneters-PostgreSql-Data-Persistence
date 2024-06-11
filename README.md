# Kubernetes-PostgreSQL-Data-Persistence

## Deploy a PostgreSQL Database in a Kubernetes Pod with Data Persistence

En esta práctica nos vamos a centrar en el despliegue de una base de datos PostgreSQL en un pod de Kubernetes con persistencia de datos. Veremos cómo, al eliminar el pod y volverlo a crear, los datos de la base de datos se mantienen.

### Pasos a seguir:

1. **Desplegar el PersistentVolume:**

   Desplegamos el PersistentVolume a partir del fichero `pv.yaml` con el comando:

   ```bash
   kubectl apply -f pv.yaml
   ```

2. **Comprobar el resultado del PersistentVolume:**

   Comprobamos el resultado con el comando:

   ```bash
   kubectl get pv
   ```

3. **Desplegar el PersistentVolumeClaim:**

   Desplegamos el PersistentVolumeClaim a partir del fichero `pvc.yaml` con el siguiente comando:

   ```bash
   kubectl apply -f pvc.yaml
   ```

4. **Comprobar el resultado del PersistentVolumeClaim:**

   Comprobamos el resultado con el comando:

   ```bash
   kubectl get pvc -n paradigma
   ```

5. **Verificar la vinculación del PVC al PV:**

   El PVC ha quedado vinculado al PV desarrollado anteriormente.

6. **Desplegar el Pod:**

   Desplegamos el Pod a partir del fichero `deployment-postgre.yaml` de antes con el siguiente comando:

   ```bash
   kubectl apply -f deployment-postgre.yaml
   ```

7. **Comprobar la asignación del volumen persistente al Pod:**

   A continuación, comprobamos que el pod tiene asignado el volumen persistente con el siguiente comando:

   ```bash
   kubectl describe pod mipostgresvolumenpersistente -n paradigma
   ```

8. **Acceder al Pod y gestionar la base de datos:**

   Continuaremos accediendo al pod para crear una base de datos, una tabla e insertar registros.

   ```bash
   kubectl exec -it mipostgresvolumenpersistente -n paradigma -- /bin/bash
   ```

   Dentro del pod:

   ```bash
   createdb -U postgres mibbdd

   psql -U postgres mibbdd

   CREATE TABLE productos (id int, nombre varchar(100));

   INSERT INTO productos (id, nombre) VALUES (1, 'Coche');

   SELECT * from productos;
   ```

   Con los anteriores comandos hemos accedido al pod, creado una base de datos en PostgreSQL, accedido a ella, creado una tabla, insertado un registro y, por último, hemos hecho una consulta sobre la tabla.

9. **Eliminar el Pod y comprobar la persistencia de los datos:**

   Continuamos saliendo del pod y eliminándolo para poder comprobar la persistencia de los datos con los siguientes comandos:

   ```bash
   kubectl delete pod mipostgresvolumenpersistente -n paradigma

   kubectl get pods -n paradigma
   ```

10. **Verificar la existencia del PV y PVC:**

    Después de eliminar el pod, podemos comprobar que tanto el PV como el PVC siguen existiendo.

    ```bash
    kubectl get pv
    kubectl get pvc -n paradigma
    ```

11. **Recrear el Pod y verificar la persistencia de los datos:**

    Si creamos el pod de nuevo y accedemos a él, vemos que sigue existiendo la base de datos y el registro que insertamos inicialmente en la tabla.

    ```bash
    kubectl apply -f deployment-postgre.yaml

    kubectl exec -it mipostgresvolumenpersistente -n paradigma -- /bin/bash

    psql -U postgres mibbdd

    SELECT * from productos;
    ```

12. **Conclusión:**

    Esto ha sido posible al usar volúmenes persistentes que son independientes al pod y que cuando el pod es eliminado, el volumen no se elimina y se puede volver a utilizar.
