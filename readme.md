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

# Создаем экземпляры классов
student1 = Student('Ruoy', 'Eman', 'your_gender')
student2 = Student('Some', 'Student', 'your_gender')

lecturer1 = Lecturer('Some', 'Lecturer')
lecturer2 = Lecturer('Another', 'Lecturer')

reviewer1 = Reviewer('Some', 'Reviewer')
reviewer2 = Reviewer('Another', 'Reviewer')

# Добавляем курсы
student1.courses_in_progress += ['Python']
student2.courses_in_progress += ['Python']
student1.courses_in_progress += ['Git']
lecturer1.courses_attached += ['Python']
lecturer2.courses_attached += ['Git']
reviewer1.courses_attached += ['Python']
reviewer2.courses_attached += ['Git']

# Вызываем методы
reviewer1.rate_hw(student1, 'Python', 10)
reviewer1.rate_hw(student1, 'Python', 9)
reviewer1.rate_hw(student2, 'Python', 8)
reviewer2.rate_hw(student1, 'Git', 7)
reviewer2.rate_hw(student2, 'Git', 9)

student1.rate_lecturer(lecturer1, 'Python', 8)
student1.rate_lecturer(lecturer1, 'Python', 9)
student2.rate_lecturer(lecturer2, 'Git', 10)
student2.rate_lecturer(lecturer2, 'Git', 7)


# Реализуем функции для подсчета средних оценок
def calculate_average_hw_grade(students, course):
    grades_count = 0
    total_grades = 0
    for student in students:
        if course in student.grades:
            grades_count += len(student.grades[course])
            total_grades += sum(student.grades[course])
    if grades_count > 0:
        return total_grades / grades_count
    else:
        return 0  # Возвращаем 0, если оценок нет

def calculate_average_lecturer_grade(lecturers, course):
    grades_count = 0
    total_grades = 0
    for lecturer in lecturers:
        if hasattr(lecturer, 'lecturer_grades') and course in lecturer.lecturer_grades:
            grades_count += len(lecturer.lecturer_grades[course])
            total_grades += sum(lecturer.lecturer_grades[course])
    if grades_count > 0:
        return total_grades / grades_count
    else:
        return 0  # Возвращаем 0, если оценок нет



average_hw_python = calculate_average_hw_grade([student1, student2], 'Python')
average_hw_git = calculate_average_hw_grade([student1, student2], 'Git')

average_lecturer_python = calculate_average_lecturer_grade([lecturer1, lecturer2], 'Python')
average_lecturer_git = calculate_average_lecturer_grade([lecturer1, lecturer2], 'Git')

print(f"Средняя оценка за домашние задания по Python: {average_hw_python:.1f}")
print(f"Средняя оценка за домашние задания по Git: {average_hw_git:.1f}")

print(f"Средняя оценка за лекции по Python: {average_lecturer_python:.1f}")
print(f"Средняя оценка за лекции по Git: {average_lecturer_git:.1f}")

# Вывод информации с помощью __str__
print("\nИнформация об объектах:")
print(student1)
print()
print(student2)
print()
print(lecturer1)
print()
print(lecturer2)
print()
print(reviewer1)
print()
print(reviewer2)