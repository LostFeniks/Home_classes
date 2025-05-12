class Student:
    def __init__(self, name, surname, gender):
        self.name = name
        self.surname = surname
        self.gender = gender
        self.finished_courses = []
        self.courses_in_progress = []
        self.grades = {}

    def rate_lecturer(self, lecturer, course, grade):
        """Выставляет оценку лектору."""
        if not isinstance(lecturer, Lecturer):
            return "Ошибка: Недопустимый тип лектора."
        if course not in self.courses_in_progress:
            return "Ошибка: Студент не изучает этот курс."
        if course not in lecturer.courses_attached:
            return "Ошибка: Лектор не ведет этот курс."
        if not (1 <= grade <= 10):
            return "Ошибка: Оценка должна быть от 1 до 10."
        if not hasattr(lecturer, 'lecturer_grades'):
            lecturer.lecturer_grades = {}
        if course in lecturer.lecturer_grades:
            lecturer.lecturer_grades[course].append(grade)
        else:
            lecturer.lecturer_grades[course] = [grade]
        return "Оценка успешно выставлена"

    def _calculate_average_grade(self):
        """Вычисляет среднюю оценку за домашние задания."""
        all_grades = []
        for course_grades in self.grades.values():
            all_grades.extend(course_grades)
        if all_grades:
            return sum(all_grades) / len(all_grades)
        else:
            return 0

    def __str__(self):
        """Возвращает строковое представление студента."""
        courses_in_progress_str = ", ".join(self.courses_in_progress)
        finished_courses_str = ", ".join(self.finished_courses)
        average_grade = self._calculate_average_grade()  #Вычисляем средний бал
        return (f"Имя: {self.name}\n"
                f"Фамилия: {self.surname}\n"
                f"Средняя оценка за домашние задания: {average_grade:.1f}\n"
                f"Курсы в процессе изучения: {courses_in_progress_str}\n"
                f"Завершенные курсы: {finished_courses_str}")


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

    def _calculate_average_grade(self):
      """Вычисляет среднюю оценку за лекции."""
      if hasattr(self, 'lecturer_grades'):
          all_grades = []
          for course_grades in self.lecturer_grades.values():
              all_grades.extend(course_grades)
          if all_grades:
              return sum(all_grades) / len(all_grades)
          else:
              return 0
      else:
          return 0 # Если оценок нет, возвращаем 0

    def __str__(self):
        """Возвращает строковое представление лектора."""
        average_grade = self._calculate_average_grade() #Вычисляем средний бал
        return (f"Имя: {self.name}\n"
                f"Фамилия: {self.surname}\n"
                f"Средняя оценка за лекции: {average_grade:.1f}")

    def __lt__(self, other):
        """Сравнивает лекторов по средней оценке (меньше)."""
        if not isinstance(other, Lecturer):
            raise TypeError("Невозможно сравнить Лектора с экземпляром другого класса.")
        return self._calculate_average_grade() < other._calculate_average_grade()

    def __le__(self, other):
        """Сравнивает лекторов по средней оценке (меньше или равно)."""
        if not isinstance(other, Lecturer):
            raise TypeError("Невозможно сравнить Лектора с экземпляром другого класса.")
        return self._calculate_average_grade() <= other._calculate_average_grade()

    def __eq__(self, other):
        """Сравнивает лекторов по средней оценке (равно)."""
        if not isinstance(other, Lecturer):
            raise TypeError("Невозможно сравнить Лектора с экземпляром другого класса.")
        return self._calculate_average_grade() == other._calculate_average_grade()

    def __ne__(self, other):
        """Сравнивает лекторов по средней оценке (не равно)."""
        if not isinstance(other, Lecturer):
            raise TypeError("Невозможно сравнить Лектора с экземпляром другого класса.")
        return self._calculate_average_grade() != other._calculate_average_grade()

    def __gt__(self, other):
        """Сравнивает лекторов по средней оценке (больше)."""
        if not isinstance(other, Lecturer):
            raise TypeError("Невозможно сравнить Лектора с экземпляром другого класса.")
        return self._calculate_average_grade() > other._calculate_average_grade()

    def __ge__(self, other):
        """Сравнивает лекторов по средней оценке (больше или равно)."""
        if not isinstance(other, Lecturer):
            raise TypeError("Невозможно сравнить Лектора с экземпляром другого класса.")
        return self._calculate_average_grade() >= other._calculate_average_grade()


class Reviewer(Mentor):
    def __init__(self, name, surname):
        super().__init__(name, surname)

    def rate_hw(self, student, course, grade):
        if isinstance(student, Student) and course in self.courses_attached and course in student.courses_in_progress:
            if course in student.grades:
                student.grades[course] += [grade]
            else:
                student.grades[course] = [grade]
        else:
            return 'Ошибка'

    def __str__(self):
        """Возвращает строковое представление проверяющего."""
        return (f"Имя: {self.name}\n"
                f"Фамилия: {self.surname}")


best_student = Student('Ruoy', 'Eman', 'your_gender')
best_student.courses_in_progress += ['Python']
best_student.finished_courses += ['Введение в программирование']

cool_reviewer = Reviewer('Some', 'Buddy')
cool_reviewer.courses_attached += ['Python']

cool_reviewer.rate_hw(best_student, 'Python', 10)
cool_reviewer.rate_hw(best_student, 'Python', 9)
cool_reviewer.rate_hw(best_student, 'Python', 8)


cool_lecturer = Lecturer('Some', 'Lecturer')
cool_lecturer.courses_attached += ['Python']

best_student.rate_lecturer(cool_lecturer, 'Python', 9)
best_student.rate_lecturer(cool_lecturer, 'Python', 10)
best_student.rate_lecturer(cool_lecturer, 'Python', 10)

# Вывод информации
print(cool_reviewer)
print()
print(cool_lecturer)
print()
print(best_student)
print()

# Сравнение Лекторов
lecturer1 = Lecturer("John", "Doe")
lecturer1.courses_attached = ["Python"]
lecturer1.lecturer_grades = {"Python": [9, 10, 8]}

lecturer2 = Lecturer("Jane", "Smith")
lecturer2.courses_attached = ["Python"]
lecturer2.lecturer_grades = {"Python": [7, 8, 6]}

print(f"Лектор 1 > Лектор 2: {lecturer1 > lecturer2}")
print(f"Лектор 1 < Лектор 2: {lecturer1 < lecturer2}")
print(f"Лектор 1 == Лектор 2: {lecturer1 == lecturer2}")