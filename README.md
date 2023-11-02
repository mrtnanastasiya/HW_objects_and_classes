class Student:
    def __init__(self, name, surname, gender):
        self.name = name
        self.surname = surname
        self.gender = gender
        self.finished_courses = []
        self.courses_in_progress = []
        self.grades = {}
        self.courses_attached = []

    def _get_avg_grade(self):
        sum_hw = 0
        count = 0
        for course in self.grades.values():
            sum_hw += sum(course)
            count += len(course)
        return round(sum_hw / count, 2)

    def __str__(self):
        res = f'Имя: {self.name}\nФамилия: {self.surname}\nСредняя оценка за домашние задания: {self._get_avg_grade()}\nКурсы в процессе изучения: {self.courses_in_progress}\nЗавершенные курсы: {self.finished_courses}'
        return res

    def rate_hw(self, lecturer, course, grade):
        if isinstance(lecturer, Lecturer) and course in self.courses_in_progress and course in lecturer.courses_attached:
            if course in lecturer.grades:
                lecturer.grades[course] += [grade]
            else:
                lecturer.grades[course] = [grade]
        else:
            return 'Ошибка'

    def __lt__(self, other):
        return self._get_avg_grade() < other._get_avg_grade()


class Mentor:
    def __init__(self, name, surname):
        self.name = name
        self.surname = surname
        self.courses_attached = []

class Lecturer(Mentor):
    def __init__(self, name, surname):
        super().__init__(name, surname)
        self.courses_in_progress = []
        self.grades = {}

    def _get_avg_grade_lecturer(self):
        sum_hw = 0
        count = 0
        for course in self.grades.values():
            sum_hw += sum(course)
            count += len(course)
        if count:
            return round(sum_hw / count, 2)
        else:
            return "Оценок нет"

    def __str__(self):
        res = f'Имя: {self.name}\nФамилия: {self.surname}\nСредняя оценка за лекции: {self._get_avg_grade_lecturer()}'
        return res

    def __lt__(self, other):
        return self._get_avg_grade_lecturer() < other._get_avg_grade_lecturer()

class Reviewer(Mentor):
    def rate_hw(self, student, course, grade):
        if isinstance(student, Student) and course in self.courses_attached and course in student.courses_in_progress:
            if course in student.grades:
                student.grades[course] += [grade]
            else:
                student.grades[course] = [grade]
        else:
            return 'Ошибка'

    def __str__(self):
        res = f'Имя: {self.name}\nФамилия: {self.surname}'
        return res

def avg_grade_all_st(course, students_list = []):
        all_grades = []
        if students_list:
            for student in students_list:
                for key, value in student.grades.items():
                    if key == course:
                        all_grades += value
            return round(sum(all_grades)/len(all_grades),2)

def avg_grade_all_lec(course, lecturers_list=[]):
    all_grades = []
    if lecturers_list:
        for lecturer in lecturers_list:
            for key, value in lecturer.grades.items():
                if key == course:
                    all_grades += value
        return round(sum(all_grades) / len(all_grades), 2)


best_student_1 = Student('Ruoy', 'Eman', 'M')
best_student_1.finished_courses += ['Введение в программирование']
best_student_1.courses_in_progress += ['Python', 'Git']
best_student_2 = Student('Сергей', 'Иванов', 'M')
best_student_2.finished_courses += ['Введение в программирование']
best_student_2.courses_in_progress += ['Python']

students_list = [best_student_1, best_student_2]

cool_reviewer = Reviewer('Some', 'Buddy')
cool_reviewer.courses_attached += ['Python']

cool_reviewer.rate_hw(best_student_1, 'Python', 10)
cool_reviewer.rate_hw(best_student_1, 'Python', 8)
cool_reviewer.rate_hw(best_student_1, 'Python', 10)
cool_reviewer.rate_hw(best_student_2, 'Python', 8)
cool_reviewer.rate_hw(best_student_2, 'Python', 9)
cool_reviewer.rate_hw(best_student_2, 'Python', 5)

cool_lecturer_1 = Lecturer('Sam', 'Rexon')
cool_lecturer_1.courses_attached += ['Python']
cool_lecturer_1.courses_in_progress += ['Python']
cool_lecturer_2 = Lecturer('Петр', 'Петров')
cool_lecturer_2.courses_attached += ['Python']
cool_lecturer_2.courses_in_progress += ['Python']

lecturers_list = [cool_lecturer_1, cool_lecturer_2]

best_student_1.rate_hw(cool_lecturer_1, 'Python', 5)
best_student_1.rate_hw(cool_lecturer_1, 'Python', 10)
best_student_1.rate_hw(cool_lecturer_1, 'Python', 9)
best_student_2.rate_hw(cool_lecturer_2, 'Python', 9)
best_student_2.rate_hw(cool_lecturer_2, 'Python', 10)
best_student_2.rate_hw(cool_lecturer_2, 'Python', 10)

print('Reviever:')
print(cool_reviewer)
print()

print('Student:')
print(best_student_1)
print()

print('Lecturer:')
print(cool_lecturer_1)
print()

print('Сравнение студентов по ср.оценке:')
print(best_student_1 > best_student_2)
print()

print('Сравнение лекторов по ср.оценке:')
print(cool_lecturer_1 > cool_lecturer_2)
print()

print('Ср. оценка за курс по всем студентам:')
print(avg_grade_all_st("Python", students_list))
print()

print('Ср. оценка за курс по всем лекторам:')
print(avg_grade_all_lec("Python", lecturers_list))
print()
