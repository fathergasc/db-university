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

    SELECT * FROM `exams` WHERE `date` = '2020-06-20' AND `hour` > '14:00:00'

    or

    SELECT * FROM `exams` WHERE `date` = '2020-06-20' AND `hour` BETWEEN '14:00:00' AND '23:59:59'

6. Selezionare tutti i corsi di laurea magistrale (38)

    SELECT * FROM `degrees` where `level` = 'magistrale'

7. Da quanti dipartimenti è composta l'università? (12)

    SELECT COUNT(*) AS "number_of_departments" FROM `departments`

8. Quanti sono gli insegnanti che non hanno un numero di telefono? (50)

    SELECT COUNT(*) AS "teachers_with_no_phone_number" FROM `teachers` WHERE `phone` IS NULL


Queries con GROUP BY:

1. Contare quanti iscritti ci sono stati ogni anno

    SELECT COUNT(*) AS "enroled_students", YEAR(enrolment_date) AS "enrolment_year" FROM `students` GROUP BY YEAR(enrolment_date)

2. Contare gli insegnanti che hanno l'ufficio nello stesso edificio

    SELECT COUNT(*) AS "teachers", `office_address` FROM `teachers` GROUP BY(office_address)

3. Calcolare la media dei voti di ogni appello d'esame

    SELECT TRUNCATE(AVG(vote), 2) AS "average_grade", `exam_id` as "exam" FROM `exam_student` GROUP BY(exam_id)

4. Contare quanti corsi di laurea ci sono per ogni dipartimento

    SELECT COUNT(*) AS "number_of_degrees", `department_id` AS `department` FROM `degrees` GROUP BY(department_id)

    SELECT `students`.`surname`, `students`.`name`, `exams`.`id` AS "exam_id", `courses`.`name` AS "exam_name", COUNT(exam_student.student_id) AS "tries" FROM `students` JOIN `exam_student` ON `students`.`id` = `exam_student`.`student_id` JOIN `exams` ON `exam_student`.`exam_id` = `exams`.`id` JOIN `courses` ON `exams`.`course_id` = `courses`.`id` GROUP BY `exam_student`.`exam_id`, `exam_student`.`student_id`, `exam_student`.`vote` ORDER BY `students`.`name`, `students`.`surname` ASC




Queries con JOIN:

1. Selezionare tutti gli studenti iscritti al Corso di Laurea in Economia

    SELECT `degrees`.`id` AS "degrees_id", `degrees`.`name`, `students`.`name`, `students`.`surname`, `students`.`id` FROM `degrees` JOIN `students` ON `students`.`degree_id` = `degrees`.`id` WHERE `degrees`.`name` LIKE "Corso di Laurea in Economia"

2. Selezionare tutti i Corsi di Laurea del Dipartimento di Neuroscienze

    SELECT `departments`.`id` AS "department_id", `departments`.`name`, `degrees`.`id` AS "degree_id", `degrees`.`name` FROM `departments` JOIN `degrees` ON `departments`.`id` = `degrees`.`department_id` WHERE `departments`.`name` LIKE "Dipartimento di Neuroscienze"

3. Selezionare tutti i corsi in cui insegna Fulvio Amato (id=44)

    SELECT `courses`.`name` AS "course_name", `courses`.`id` AS "course_id", `teachers`.`name`, `teachers`.`surname`, `teachers`.`id` FROM `courses` JOIN `course_teacher` ON `courses`.`id` = `course_teacher`.`course_id` JOIN `teachers` ON `teachers`.`id` = `course_teacher`.`teacher_id` WHERE `teachers`.`id` = 44

4. Selezionare tutti gli studenti con i dati relativi al corso di laurea a cui sono iscritti e il
relativo dipartimento, in ordine alfabetico per cognome e nome

    SELECT `students`.`name`, `students`.`surname`, `degrees`.`name` AS `degree_name`, `departments`.`name` AS `department`, `degrees`.`level`, `degrees`.`address`, `degrees`.`website`, `degrees`.`email` FROM `students` JOIN `degrees` ON `students`.`degree_id` = `degrees`.`id` JOIN `departments` ON `degrees`.`department_id` = `departments`.`id` ORDER BY `students`.`name`, `students`.`surname` ASC

5. Selezionare tutti i corsi di laurea con i relativi corsi e insegnanti

    SELECT `degrees`.`id` AS "degree_id", `degrees`.`name` AS "degree_name", `courses`.`name` AS "course_name", `courses`.`id` AS "course_id", `teachers`.`name`, `teachers`.`surname` FROM `degrees` JOIN `courses` ON `degrees`.`id` = `courses`.`degree_id` JOIN `course_teacher` ON `courses`.`id` = `course_teacher`.`course_id` JOIN `teachers` ON `course_teacher`.`teacher_id` = `teachers`.`id` ORDER BY `degree_id` ASC


6. Selezionare tutti i docenti che insegnano nel Dipartimento di Matematica (54)

    SELECT DISTINCT `departments`.`name` AS "department_name", `teachers`.`name`, `teachers`.`surname` FROM `departments` JOIN `degrees` ON `departments`.`id` = `degrees`.`department_id` JOIN `courses` ON `degrees`.`id` = `courses`.`degree_id` JOIN `course_teacher` ON `courses`.`id` = `course_teacher`.`course_id` JOIN `teachers` ON `course_teacher`.`teacher_id` = `teachers`.`id` WHERE `departments`.`name` LIKE "Dipartimento di Matematica"


7. BONUS: Selezionare per ogni studente quanti tentativi d’esame ha sostenuto per
superare ciascuno dei suoi esami

   SELECT `students`.`name`, `students`.`surname`, `courses`.`name` AS "exam_name", COUNT(exam_student.vote) AS "tries", MAX(`exam_student`.`vote`) AS "higher_grade" FROM `students` JOIN `exam_student` ON `students`.`id` = `exam_student`.`student_id` JOIN `exams` ON `exam_student`.`exam_id` = `exams`.`id` JOIN `courses` ON `exams`.`course_id` = `courses`.`id` GROUP BY `students`.`id`, `courses`.`id` HAVING `higher_grade` >= 18 ORDER BY `students`.`surname`, `students`.`name` ASC
