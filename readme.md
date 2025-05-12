class Student:
    def __init__(self, name, surname, gender):
        self.name = name
        self.surname = surname
        self.gender = gender
        self.finished_courses = []
        self.courses_in_progress = []
        self.grades = {}

    def rate_lecturer(self, lecturer, course, grade):
        if not isinstance(lecturer, Lecturer):
            return "Ошибка: Недопустимый тип лектора."

        if course not in self.courses_in_progress:
            return "Ошибка: Студент не изучает этот курс."

        if course not in lecturer.courses_attached:
            return "Ошибка: Лектор не ведет этот курс."

        if not (1 <= grade <= 10):
            return "Ошибка: Оценка должна быть от 1 до 10."

        if not hasattr(lecturer, 'lecturer_grades'):  # Проверяем, существует ли атрибут
            lecturer.lecturer_grades = {}

        if course in lecturer.lecturer_grades:
            lecturer.lecturer_grades[course].append(grade)
        else:
            lecturer.lecturer_grades[course] = [grade]
        return "Оценка успешно выставлена"


class Mentor:
    def __init__(self, name, surname):
        self.name = name
        self.surname = surname
        self.courses_attached = []

    def rate_hw(self, student, course, grade):
        if isinstance(student, Student) and course in self.courses_attached and course in student.courses_in_progress:
            if course in student.grades:
                student.grades[course] += [grade]
            else:
                student.grades[course] = [grade]
        else:
            return 'Ошибка'

class Lecturer(Mentor):
    def __init__(self, name, surname):
        super().__init__(name, surname)

class Reviewer(Mentor):
    def __init__(self, name, surname):
        super().__init__(name, surname)

    def rate_hw(self, student, course, grade):  # Only reviewer can rate homeworks
        if isinstance(student, Student) and course in self.courses_attached and course in student.courses_in_progress:
            if course in student.grades:
                student.grades[course] += [grade]
            else:
                student.grades[course] = [grade]
        else:
            return 'Ошибка'

best_student = Student('Ruoy', 'Eman', 'your_gender')
best_student.courses_in_progress += ['Python']
best_student.finished_courses += ['Введение в программирование']

cool_reviewer = Reviewer('Some', 'Buddy')
cool_reviewer.courses_attached += ['Python']

cool_reviewer.rate_hw(best_student, 'Python', 10)
cool_reviewer.rate_hw(best_student, 'Python', 9)
cool_reviewer.rate_hw(best_student, 'Python', 8)

print(f"Оценки студента за домашние задания: {best_student.grades}")

cool_lecturer = Lecturer('Some', 'Lecturer')
cool_lecturer.courses_attached += ['Python']


print(best_student.rate_lecturer(cool_lecturer, 'Python', 9))
print(best_student.rate_lecturer(cool_lecturer, 'Python', 10))
print(best_student.rate_lecturer(cool_lecturer, 'Python', 10))


print(f"Оценки лектора: {cool_lecturer.lecturer_grades}")

print(best_student.rate_lecturer(cool_lecturer, 'Введение в программирование', 5))
print(best_student.rate_lecturer(cool_lecturer, 'Python', 11))
print(best_student.rate_lecturer(cool_lecturer, cool_lecturer, 5))
