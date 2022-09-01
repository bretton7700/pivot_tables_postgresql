# pivot_tables_postgresql
this is an sql readme file that will create a pivot using table functions.

######Create Pivot Table in PostgreSQL using Crosstab function
PostgreSQL also provides a built-in Crosstab function that allows you to easily create pivot table in PostgreSQL. However, you need to install the table_func extension to enable Crosstab function.

######
 #####
 
 Crosstab works with a SELECT query as its input parameter which must follow 3 requirements

1. It must return 3 columns
2. The first column is the row identifier for your final pivot table 
3. The 2nd column is the category column to be pivoted - more like the values
    The 3rd column is the value column that you want to pivot e.g score

#####
postgresql> CREATE EXTENSION IF NOT EXISTS tablefunc;

#####
CREATE TABLE marks (
id serial ,
name varchar(15),
exam int,
score int,
PRIMARY KEY (id)
);


insert into marks (name,exam,score) values ('dan',1,70);
insert into marks (name,exam,score) values ('dan',2,77);
insert into marks (name,exam,score) values ('dan',3,71);
insert into marks (name,exam,score) values ('dan',4,70);

insert into marks (name,exam,score) values ('joy',1,89);
insert into marks (name,exam,score) values ('joy',2,87);
insert into marks (name,exam,score) values ('joy',3,88);
insert into marks (name,exam,score) values ('joy',4,89);

####
mysql> select * from marks;
+------+------+------+-------+
| id   | name | exam | score |
+------+------+------+-------+
|   1  |  dan |   1  |   70  |
|   2  |  dan |   2  |   77  |
|   3  |  dan |   3  |   71  |
|   4  |  dan |   4  |   70  |
|   5  |  joy |   1  |   89  |
|   6  |  joy |   2  |   87  |
|   7  |  joy |   3  |   88  |
|   8  |  joy |   4  |   89  |
+------+------+------+-------+


#####
Let’s say you want to pivot the above table by Exam column, such that for each student you get 1 row, with all exam scores as separate columns, as shown below.


####
 name | exam_1 | exam_2 | exam_3 | exam_4
------+-------+-------+-------+-------
 dan  |    70 |    77 |    71 |    70
 joy  |    89 |    87 |    88 |    89
 
 
 ####
 Since we have enabled Crosstab function for our database, you can use the following query to create a crosstab in PostgreSQL.

postgres=# SELECT * FROM crosstab('select name, exam, score from marks order by 1,2')
             AS final_result (name varchar(15),exam_1 int, exam_2 int, exam_3 int, exam_4 int);
 name | exam_1 | exam_2 | exam_3 | exam_4
------+-------+-------+-------+-------
 dan  |    70 |    77 |    71 |    70
 joy  |    89 |    87 |    88 |    89
 
 The Crosstab will take the result of your SELECT query, and build pivot table out of it, based on the columns you mention for your pivot table. In the above query, the pivot table is stored in a temporary table final_result (name varchar(15),exam_1 int, exam_2 int, exam_3 int, exam_4 int). You have to define the column names and data types of your final pivot table.

