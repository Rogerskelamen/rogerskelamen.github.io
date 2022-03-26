---
title: Flask数据库操作
date: 2021-08-18 14:13:43
author: Rogers Kelamen
index_img: https://s2.loli.net/2022/03/26/sQKE14ZPVMDSCcn.jpg
tags:
- python
categories:
- 后端
---


# Flask数据库操作

## 首先配置一下

```python
from flask_sqlalchemy import SQLAlchemy
from flask import Flask
import pymysql

app = Flask(__name__)
# app.config["SQLALCHEMY_DATABASE_URI"] = "mysql+pymysql://root:password@127.0.0.1:3306/test"
app.config["SQLALCHEMY_DATABASE_URI"] = "sqlite:///" + "/home/zyw/Documents/Program/Python/myFlask/test.db"
app.config["SQLALCHEMY_TRACK_MODIFICATIONS"] = False
app.config["SECRET_KEY"] = b'afsasdfs'

db = SQLAlchemy(app)
```

这里是将一个flask实例和一个数据库进行绑定，然后使用SQLAlchemy来实例化这个数据库

这样相当于一个数据库就建好了，并且和app绑定

*ps: 你也可以使用mysql当然，使用上面的路径格式*

## 创建一个表

比如就用班级来类比

*建一个学生表*

```python
# 学生表
class Student(db.Model):
    __tablename__ = "student"
    id =
    name =
    gender =
    phone =
```

这个`db.Model`就是数据库中的所有表的模型父类，这里相当于是将表和数据库相关联。


## 创建主键

*先拿Student的主键举个例子*

```python
# 学生表
class Student(db.Model):
    __tablename__ = "student"
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(64), nullable=False)
    gender = db.Column(db.Enum("男", "女"), nullable=False)
    phone = db.Column(db.String(11))
    
# 课程表
class Course(db.Model):
    __tablename__ = "course"
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(64), nullable=False)
    teacher_id = db.Column(db)


# 教师表
class Tercher(db.Model):
    __tablename__ = "teacher"
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(64), nullable=False)
    gender = db.Column(db.Enum("男", "女"), nullable=False)
    phone = db.Column(db.String(11))

if __name__ == '__main__':
    db.create_all()
```

db.Column()指的是这一列，也就是这个字段是什么内容，定义了<u>这个字段是什么类型，是否为主键，是否可以为空等</u>

然后这个`db.create_all()`就可以把表建起来了



### 删除表

当我们把所有的表都写好之后运行一下后，发现我们不需要这个表了就得删除表了

```python
db.drop_all()
```

## 增删改查

```python
# 导入实例化的数据库
from learn_sql import db, Student

# 增
# 实例化数据
s1 = Student(name='张三', gender="男", phone="12345678900")
s2 = Student(name='张四', gender="男")
s3 = Student(name='张五', gender="男")
s4 = Student(name='张六', gender="男", phone="12345678900")

db.session.add(s1)  # 添加数据表中的一列
db.session.add_all([s2, s3, s4])
db.session.commit()  # 提交操作


# 查 get(id)
stu = Student.query.get(1)  # 通过id来获取一行数据
print(stu.phone)
print(stu.gender)
# 查全部
stu = Student.query.all()
for student in stu:
    print(student.name, student.gender, student.phone)
    
# 查 filter() 默认返回list
stu = Student.query.filter(Student.id < 3)
for i in stu:
    print(i.name, i.id)
stu = Student.query.filter(Student.name == "张三")
for i in stu:
    print(i.name, i.id)
    

# 改 update() 返回更改了几条数据
stu = Student.query.filter(Student.id == 1).update({
    "name": "张毅"
})
db.session.commit()
# 多条更改
stu = Student.query.filter(Student.gender == "男").update({
    "name": "女"
})
db.session.commit()

# 删 delete()
stu = Student.query.filter(Student.gender == "男").delete()
print(stu)
db.session.commit()
```

**注意主键自动会帮我们设置，从<u>1</u>开始自增**

### 使用filter_by()

```python
# 使用filter_by()
stu = Student.query.filter_by(name="张三").all()
print(stu)
stu = Student.query.filter_by(name="张三").first()
```

这里的filter_by是一种途径，通过什么条件来查，返回一个sql语句，需要`all()`等方法才能得到数据

你还可以多次过滤限制多个条件

```python
stu = Student.query.filter_by(name="张三").filter(Student.id > 2)
```

**记住只要修改数据库的数据就要提交(commit)**


## 设置外键

```python
# 学生表
class Student(db.Model):
    __tablename__ = "student"
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(64), nullable=False)
    gender = db.Column(db.Enum("男", "女"), nullable=False)
    phone = db.Column(db.String(11))
    grades = db.relationship("Grade", backref="student")  # 和成绩表相关联


# 成绩表
class Grade(db.Model):
    __tablename__ = "grade"
    id = db.Column(db.Integer, primary_key=True)
    # course_id =
    grade = db.Column(db.String(3), nullable=False)
    student_id = db.Column(db.Integer, db.ForeignKey("student.id"))
```

这里的`backref`就是反向指引，查Grade的时候直接说student就可以指向Student表了。

也就是说一张表根据一个字段来连接另一张表，这个字段专门用来指向另一张表

### 根据外键查

```python
# 一对多访问
stu = Student.query.get(1)
for i in stu.grades:
    print(stu.name, i.grade)

# 多对一访问
grade = Grade.query.filter(Grade.grade == "100").all()
for i in grade:
    print(i.student.name, i.student.gender)
```

一对多的‘多’在谁身上就在谁身上加外键

## 多对多关系

**需要注意的是多对多关系就需要重新定义一张映射表**

```python
# 学生表
class Student(db.Model):
    __tablename__ = "student"
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(64), nullable=False)
    gender = db.Column(db.Enum("男", "女"), nullable=False)
    phone = db.Column(db.String(11))
    courses = db.relationship("Course", secondary="student_to_course", backref="students")
    grades = db.relationship("Grade", backref="student")  # 和成绩表相关联


# 学生课程映射表
class StudentToCourse(db.Model):
    __tablename__ = "student_to_course"
    id = db.Column(db.Integer, primary_key=True)
    student_id = db.Column(db.Integer, db.ForeignKey("student.id"))
    course_id = db.Column(db.Integer, db.ForeignKey("course.id"))


# 课程表
class Course(db.Model):
    __tablename__ = "course"
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(64), nullable=False)
    # teacher_id = db.Column(db)
    grades = db.relationship("Grade", backref="course")  # 和成绩表相关联
    teacher_id = db.Column(db.Integer, db.ForeignKey("teacher.id"))

```

### 对多对查询

```python
# 首先插入数据
cs = Course.query.filter(Course.id >= 2).all()
print(cs)

stu = Student.query.filter(Student.id >= 2).all()
for s in stu:
    s.courses = cs
    db.session.add(s)
    db.session.commit()

# 学生查询课程
stu = Student.query.get(1)
print(stu.courses)
for s in stu.courses:
    print(s.name)

# 课程查询学生
c = Course.query.get(2)
print(c.students)
for s in c.students:
    print(s.name)
```

**总结：主要是建一个映射表**