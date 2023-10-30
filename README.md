def averageAllStudents(student_list: list, course: str):                 #Средние за ДЗ и лекции по всем
    course_n = {}
    for student in student_list:
        if course in student.grades:
            result = sum(student.grades[course]) / len(student.grades[course])
            course_n[course] = [result]
    return course_n

def averageAllLecturers(lecturer_list: list, course: str):
    course_n = {}
    for lector in lecturer_list:
        if course in lector.grades:
            result = sum(lector.grades[course]) / len(lector.grades[course])
            course_n[course] = [result]
    return course_n

class Student:
    def __init__(self, name, surname, gender):
        self.name = name
        self.surname = surname
        self.gender = gender
        self.finished_courses = []
        self.courses_in_progress = []
        self.grades = {}
 
    def add_courses(self, course_name):
        self.finished_courses.append(course_name)
        

    def rate_hw_lec(self, lector, course, grade):
        if isinstance(lector, Lecturer) and course in lector.courses_attached and course in self.courses_in_progress:
            if grade in range(1, 10):                       #Недопустимые оценки не расчитываются в средней
                if course in lector.grades:
                    lector.grades[course] += [grade]
                else:
                    lector.grades[course] = [grade]
        else:
            return 'Ошибка'  
        
    def __median(self):
        res = {key : float(sum(values)) / len(values) for key, values in self.grades.items()} # в дальнейшем для среднего по каждому предмету
        if len(self.grades) == 0:
            print("Не учился")                     
            return 0
        else:
            res_1 = list(res.values())
            med = sum(res_1) / len(res_1)
            return med                        #средние оценки ДЗ все, без учета предмета
                
    def __str__(self):
        return (f'Имя: {self.name}\n'
                f'Фамилия: {self.surname} \n'
                f'Средняя оценка за домашние задания: {self.__median():.1f}\n'
                f'Курсы в процессе изучения: {", ".join(self.courses_in_progress)}')
    
    def __gt__(self, other):
        if not isinstance(other, Student):
            print('Невозможно сравнить!')
            return
        return (self.__median() > other.__median())
            
        
class Mentor:
    def __init__(self, name, surname):
        self.name = name
        self.surname = surname
        self.courses_attached = []
        
   
class Lecturer(Mentor):
    def __init__(self, name, surname):
        super().__init__(name, surname)
        self.grades = {}
        self.courses_attached = [] 
                
    def __median(self):
        res = {key : float(sum(values)) / len(values) for key, values in self.grades.items()} # в дальнейшем для среднего по каждому предмету
        if len(self.grades) == 0:
            print('Занятия не проводил')                    
            return 0
        else:
            res_1 = list(res.values())
            med = sum(res_1) / len(res_1)
            return med                        #средние оценки все, без учета предмета
      
    def __str__(self):
        return (f'Имя: {self.name}\n'
                f'Фамилия: {self.surname}\n'
                f'Средняя оценка за лекции: {self.__median():.1f}')      #оценки округлены
    
    def __gt__(self, other):
        if not isinstance(other, Lecturer):
            print('Невозможно сравнить!')
            return
        return (self.__median() > other.__median())
    

class Reviewer(Mentor):
    
    def __init__(self, name, surname):
        super().__init__(name, surname)
    
    def rate_hw_st(self, student, course, grade):
        if isinstance(student, Student) and course in self.courses_attached and course in student.courses_in_progress:
            if course in student.grades:
                student.grades[course] += [grade]
            else:
                student.grades[course] = [grade]
        else:
            return 'Ошибка'
        
    def __str__(self):
        return f'Имя: {self.name} \nФамилия: {self.surname}'
        
    
best_student = Student('Ruoy', 'Eman', 'your_gender')
best_student.courses_in_progress += ['Python']
best_student.courses_in_progress += ['Git']
some_one_student = Student('Petr', 'Vasechkin', 'floating_gender')
some_one_student.courses_in_progress += ['Python']
 
cool_rev = Reviewer('Some', 'Buddy')
cool_rev.courses_attached += ['Python']
 
cool_rev.rate_hw_st(best_student, 'Python', 10)
cool_rev.rate_hw_st(best_student, 'Python', 10)
cool_rev.rate_hw_st(best_student, 'Python', 10)
cool_rev.rate_hw_st(some_one_student, 'Python', 9)
 
print("Оценки лучшего студента")
print(best_student.grades)

print("Выставленная оценка лектору")
cool_lector = Lecturer('Some', 'Else')
cool_lector.courses_attached += ['Python']
cool_lector.courses_attached += ['Git']
another_lector = Lecturer('Volodimir', 'Polezhaykyn')
another_lector.courses_attached += ['Python']

best_student.rate_hw_lec(cool_lector, 'Python', 5)
best_student.rate_hw_lec(cool_lector, 'Python', 4)
best_student.rate_hw_lec(cool_lector, 'Git', 4)
best_student.rate_hw_lec(cool_lector, 'Git', 4)
some_one_student.rate_hw_lec(cool_lector, 'Python', 4)

print(cool_lector.grades)

print("Проверяющий")
print(cool_rev)

print("Выставленная средняя оценка лектору")
neo_lec = Lecturer('Neo', 'Lectorov')
print(neo_lec)
print(cool_lector)

print("Средняя по домашним заданиям")
print(best_student)
print(some_one_student)

print("Сравнение студента")
print(some_one_student > best_student)

print("Сравнение лекторов")
print(another_lector > cool_lector)

print("Средняя по курсу за ДЗ")
print(averageAllStudents([best_student, some_one_student], 'Python'))

print("Средняя по курсу за лекции")
print(averageAllLecturers([cool_lector, another_lector], 'Python'))