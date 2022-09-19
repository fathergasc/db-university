# db-university


Queries con SELECT:

1. Selezionare tutti gli studenti nati nel 1990 (160)

    SELECT * FROM `students` WHERE YEAR(date_of_birth) = 1990

2. Selezionare tutti i corsi che valgono più di 10 crediti (479)

    SELECT * FROM `courses` WHERE `cfu` > 10


3. Selezionare tutti gli studenti che hanno più di 30 anni

    SELECT * FROM `students` WHERE (YEAR(CURRENT_DATE) - date_of_birth)) >= 30)         - non tiene in considerazione giorno e mese
    SELECT * FROM `students` WHERE TIMESTAMPDIFF(YEAR, `date_of_birth`, CURDATE()) >= 30           - tiene in conto giorno e mese, più preciso


4. Selezionare tutti i corsi del primo semestre del primo anno di un qualsiasi corso di
laurea (286)

    SELECT * FROM `courses` WHERE `period` LIKE "I semestre" AND `year` = 1

5. Selezionare tutti gli appelli d'esame che avvengono nel pomeriggio (dopo le 14) del
20/06/2020 (21)

    SELECT * FROM `exams` WHERE `date` = '2020-06-20' AND `hour` BETWEEN '14:00:00' AND '23:59:59'

6. Selezionare tutti i corsi di laurea magistrale (38)

    SELECT * FROM `degrees` where `level` = 'magistrale'

7. Da quanti dipartimenti è composta l'università? (12)

    SELECT COUNT(*) AS "number_of_departments" FROM `departments`

8. Quanti sono gli insegnanti che non hanno un numero di telefono? (50)

    SELECT COUNT(*) AS "teachers_with_no_phone_number" FROM `teachers` WHERE `phone` IS NULL