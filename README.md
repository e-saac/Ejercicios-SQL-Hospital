# Ejercicios SQL | Hospital

Muestro aquí una selección de los ejercicios propuestos en el sitio [sql-practice](https://www.sql-practice.com/). La base de datos empleada, Hospital, consta de 4 tablas: <i>patients</i>, <i>doctors</i>, <i>admissions</i> y <i>province_names</i>. Adjunto el diagrama de la misma:

![Diagrama de la base de datos "Hospital"](https://www.sql-practice.com/img/db1dark.png)

<details open>
  <summary>
    <b>Básicos</b>
  </summary><br>

  1. Muestra los nombres de pila que empiezan con la letra 'C' (pacientes).

      ```sql
      SELECT
        first_name
      FROM
        patients
      WHERE
        first_name LIKE 'C%';
      ```

  2. Muestra el nombre y apellido de los pacientes cuyo peso está dentro del rango 100 kg - 120 kg (incluyendo ambos límites).

      ```sql
      SELECT
        first_name,
        last_name
      FROM
        patients
      WHERE
        weight BETWEEN 100 AND 120;
      ```

  3. Reemplaza los valores null del campo <i>allergies</i> por el texto 'NKA'.

      ```sql
      UPDATE
        patients
      SET
        allergies = 'NKA'
      WHERE
        allergies IS NULL;
      ```

  4. Concatena el nombre y apellido de los pacientes en una columna de nombre completo.

      ```sql
      SELECT
        CONCAT(first_name, ' ', last_name) AS 'Nombre completo'
      FROM
        patients;
      ```

  5. Muestra el nombre y apellido de los pacientes, así como el nombre completo de su provincia.

      ```sql
      SELECT
        first_name,
        last_name,
        province_name
      FROM
        patients p
        JOIN province_names pn ON p.province_id = pn.province_id;
      ```

  6. Muestra la cantidad de pacientes nacidos en el año 2010.

      ```sql
      SELECT
        COUNT(*) AS 'Nacidos en 2010'
      FROM
        patients
      WHERE
        YEAR(birth_date) = 2010;
      ```

  7. Muestra el nombre, apellido y altura del paciente con la mayor altura.

      ```sql
      SELECT
        first_name,
        last_name,
        MAX(height) AS height
      FROM
        patients;
      ```

  8. Muestra toda la información de los pacientes que tienen alguno de los siguientes <i>patient_id</i>: 1, 45, 534, 879, 1000.

      ```sql
      SELECT
        *
      FROM
        patients
      WHERE
        patient_id IN (1, 45, 534, 879, 1000);
      ```

  9. Muestra las ciudades pertenecientes a la provincia 'NS', sin duplicados.

      ```sql
      SELECT
        DISTINCT city AS 'Ciudades en NS'
      FROM
        patients
      WHERE
        province_id = 'NS';
      ```

  10. Muestra el nombre, apellido y fecha de nacimiento de los pacientes que tienen una altura mayor que 160 cm y un peso mayor que 70 kg.

      ```sql
      SELECT
        first_name,
        last_name,
        birth_date
      FROM
        patients
      WHERE
        height > 160
        AND weight > 70;
      ```
      
</details>
