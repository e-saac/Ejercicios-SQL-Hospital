# Consultas SQL: Básicas e Intermedias
 
En este documento presento las soluciones a 20 ejercicios seleccionados del sitio [SQL Practice](https://www.sql-practice.com/), escritas considerando la sintaxis y funciones propias de **SQL Server**. La base de datos empleada, `hospital.db`, está compuesta por las tablas `patients`, `doctors`, `admissions` y `province_names`. A continuación se muestra su esquema relacional:

![Diagrama de la base de datos "Hospital"](https://www.sql-practice.com/img/db1dark.png)

<details>
  <summary>
    <b>Básicas</b>
  </summary><br>

  1. Muestra los nombres de pacientes que comienzan con la letra 'C'.

      ```sql
      SELECT
        first_name AS [Nombre]
      FROM
        patients
      WHERE
        first_name LIKE 'C%';
      ```

  2. Muestra el nombre y apellido de los pacientes con un peso entre 100 kg y 120 kg (ambos límites incluidos).

      ```sql
      SELECT
        first_name AS [Nombre],
        last_name AS [Apellido]
      FROM
        patients
      WHERE
        weight BETWEEN 100 AND 120;
      ```

  3. Reemplaza los valores `NULL` del campo `allergies` por el texto 'NKA' (No Known Allergies / Sin alergias conocidas).

      ```sql
      UPDATE
        patients
      SET
        allergies = 'NKA'
      WHERE
        allergies IS NULL;
      ```

  4. Concatena el nombre y apellido de los pacientes en una única columna.

      ```sql
      SELECT
        CONCAT(first_name, ' ', last_name) AS [Nombre completo]
      FROM
        patients;
      ```

  5. Muestra el nombre y apellido de los pacientes, así como el nombre completo de su provincia.

      ```sql
      SELECT
        p.first_name AS [Nombre],
        p.last_name AS [Apellido],
        pn.province_name AS [Provincia]
      FROM
        patients p
      JOIN
        province_names pn
      ON
        p.province_id = pn.province_id;
      ```

  6. Muestra la cantidad de pacientes nacidos en el año 2010.

      ```sql
      SELECT
        COUNT(*) AS [Pacientes nacidos en 2010]
      FROM
        patients
      WHERE
        YEAR(birth_date) = 2010;
      ```

  7. Muestra el nombre, apellido y altura de los pacientes con la mayor altura.

      ```sql
      SELECT
        first_name AS [Nombre],
        last_name AS [Apellido],
        height AS [Altura]
      FROM
        patients
      WHERE
        height = (
          SELECT 
            MAX(height)
          FROM
            patients
        );
      ```

  8. Muestra toda la información de los pacientes con `patient_id` 1, 45, 534, 879, 1000.

      ```sql
      SELECT
        *
      FROM
        patients
      WHERE
        patient_id IN (1, 45, 534, 879, 1000);
      ```

  9. Muestra las ciudades pertenecientes a la provincia 'NS' sin duplicados.

      ```sql
      SELECT DISTINCT
        city AS [Ciudades en NS]
      FROM
        patients
      WHERE
        province_id = 'NS';
      ```

  10. Muestra el nombre, apellido y fecha de nacimiento de los pacientes con una altura mayor de 160 cm y un peso mayor de 70 kg.

      ```sql
      SELECT
        first_name AS [Nombre],
        last_name AS [Apellido],
        birth_date AS [Fecha de nacimiento]
      FROM
        patients
      WHERE
        height > 160
        AND weight > 70;
      ```
      
</details>

<details>
  <summary>
    <b>Intermedias</b>
  </summary><br>

  11. Muestra la cantidad de pacientes hombres y de pacientes mujeres en una misma fila.

      ```sql
      SELECT 
        SUM(CASE WHEN gender = 'M' THEN 1 ELSE 0 END) AS [Pacientes hombres],
        SUM(CASE WHEN gender = 'F' THEN 1 ELSE 0 END) AS [Pacientes mujeres]
      FROM
        patients;
      ```

  12. Muestra el id y el diagnóstico de los pacientes ingresados en múltiples ocasiones por el mismo diagnóstico.

      ```sql
      SELECT
        patient_id AS [ID de paciente],
        diagnosis AS [Diagnóstico]
      FROM
        admissions
      GROUP BY
        patient_id,
        diagnosis
      HAVING
        COUNT(*) > 1;
      ```

  13. Muestra el nombre, apellido y rol (paciente o doctor) de cada persona.

      ```sql
      SELECT
        first_name AS [Nombre],
        last_name AS [Apellido],
        'Paciente' AS [Rol] 
      FROM 
        patients
      UNION ALL
      SELECT
        first_name,
        last_name,
        'Doctor'
      FROM
        doctors;
      ```
      
14. Muestra el nombre completo de los pacientes en una sola columna, con el siguiente formato:
    - El apellido debe aparecer primero y en mayúsculas.
    - El nombre debe aparecer en minúsculas y separarse del apellido solo por una coma. Ejemplo: SMITH,jane.
    - El listado debe ordenarse por el campo `first_name`, de manera descendente.

    ```sql
    SELECT
      CONCAT(UPPER(last_name), ',', LOWER(first_name)) AS [Nombre formateado]
    FROM
      patients
    ORDER BY
      first_name DESC;
    ```

  15. Muestra el id de cada provincia y la suma de las alturas de sus pacientes; lo anterior solo si dicha suma es igual o mayor que 7,000 cm.

      ```sql
      SELECT
        province_id AS [ID de provincia],
        SUM(height) AS [Suma de alturas]
      FROM
        patients
      GROUP BY
        province_id
      HAVING
        SUM(height) >= 7000;
      ```

16. Muestra el id del paciente, el id del doctor que lo atiende y el diagnóstico; lo anterior solo si se cumple alguno de los siguientes puntos:
    - El id del paciente es un número impar y el id del doctor es 1, 5 o 19.
    - El id del doctor contiene un 2 y la longitud del id del paciente es de 3 caracteres.

    ```sql
    SELECT
      patient_id AS [ID de paciente],
      attending_doctor_id AS [ID de doctor],
      diagnosis AS [Diagnóstico]
    FROM
      admissions
    WHERE
      (
        patient_id % 2 != 0
        AND attending_doctor_id IN (1, 5, 19)
      )
      OR
      (
        CAST(attending_doctor_id AS VARCHAR(10)) LIKE '%2%'
        AND LEN(CAST(patient_id AS VARCHAR(10))) = 3
      );
    ```

  17. Muestra el id y nombre completo de cada doctor, así como la fecha del primer y último ingreso que atendió.

      ```sql
      SELECT
        d.doctor_id AS [ID de doctor],
        CONCAT(d.first_name, ' ', d.last_name) AS [Nombre completo],
        MIN(a.admission_date) AS [Primer ingreso atendido],
        MAX(a.admission_date) AS [Último ingreso atendido]
      FROM
        doctors d
      JOIN
        admissions a
      ON
        d.doctor_id = a.attending_doctor_id
      GROUP BY
        d.doctor_id,
        d.first_name,
        d.last_name;
      ```

  18. Muestra la cantidad de pacientes de cada provincia. Ordena el listado de manera descendente.

      ```sql
      SELECT
        pn.province_name AS [Provincia],
        COUNT(*) AS [Pacientes]
      FROM
        patients p
      JOIN 
        province_names pn
      ON
        p.province_id = pn.province_id
      GROUP BY
        pn.province_id,
        pn.province_name
      ORDER BY
        [Pacientes] DESC;
      ```

19. Muestra el nombre completo, altura en pies (redondeada a 1 decimal), peso en libras (redondeado sin decimales), fecha de nacimiento y género (sin abreviar) de cada paciente. Considera lo siguiente:
    - Para convertir de centímetros a pies, divide entre 30.48.
    - Para convertir de kilogramos a libras, multiplica por 2.205.

    ```sql
    SELECT
      CONCAT(first_name, ' ', last_name) AS [Nombre completo],
      ROUND(height / 30.48, 1) AS [Altura (ft)],
      ROUND(weight * 2.205, 0) AS [Peso (lb)],
      birth_date AS [Fecha de nacimiento],
      CASE
        WHEN gender = 'M' THEN 'Masculino'
        WHEN gender = 'F' THEN 'Femenino'
      END AS [Género]
    FROM
      patients;
    ```

  20. Muestra el id, nombre y apellido de los pacientes que no tienen registros en la tabla `admissions`.

      ```sql
      SELECT
        patient_id AS [ID de paciente],
        first_name AS [Nombre],
        last_name AS [Apellido]
      FROM
        patients
      WHERE
        patient_id NOT IN (
          SELECT 
            patient_id
          FROM
            admissions
        );
      ```
      
</details>
