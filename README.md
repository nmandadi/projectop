# projectop


1)Download http://www.google-analytics.com/ga.js to a file named ga_local.js on your machine

i.How did you accomplish this?

Answer:  There are many ways to achieve it copy and paste in vi editor or copy the file to your local maching and use winscp or other tool ftp this file to linux server.
I copied (ctrl+c) and pasted (ctrl+v) the contents of the file in linux vi editer and named the file as ga_local.js
vi ga_local.js
esc i
paste right click mouse
:wq

ii. Are there more instances of the character a or g?
There are more instances of character a than g, please see bellow. there are 2478 a's and  585 g's, used fgrep command to derermine this.

[ec2-user@ip-172-31-35-205 nandu]$ fgrep -o a ga_local.js | wc -l
2478
[ec2-user@ip-172-31-35-205 nandu]$ fgrep -o g ga_local.js | wc -l
585

iii. You wanted to rename the variables a, b, c, and g to apple, banana, carrot, and grapefruit. How would you do this?

Replaced these characters with string using sed command. Please see bellow.
[ec2-user@ip-172-31-35-205 nandu]$ sed -i -e 's/b/banana/g' ga_local.js
[ec2-user@ip-172-31-35-205 nandu]$ sed -i -e 's/c/carrot/g' ga_local.js
[ec2-user@ip-172-31-35-205 nandu]$ sed -i -e 's/g/grapefruit/g' ga_local.js

2. For the following questions use the ./src/main/resources/aSkDddKs.csv file found in this repo and assume the file is 100GB in size 
and too big to simply open in an editor

i. Does the file include a header row? How did you determine this?

If the file is too big to open in vi, we can see top few lines with head command.  Please see details bellow.  There is no header row.
[ec2-user@ip-172-31-35-205 nandu]$ head -3 aSkDddKs.csv
awh1_oz_001_barrington_tops.jpg,263202,898A7553,\Oz_Australia's_Wilderness_Heritage_1_World_Heritage_Areas\,
awh1_oz_002_kata_tjuta.jpg,254511,279C7248,\Oz_Australia's_Wilderness_Heritage_1_World_Heritage_Areas\,
awh1_oz_003_stirling_ranges.jpg,267292,2D0507A3,\Oz_Australia's_Wilderness_Heritage_1_World_Heritage_Areas\,

ii. How many lines, words, and characters are in the file? How did you determine this?

Used wc commands with lwc parameters, please see bellow. or simply wc aSkDddKs.csv
[ec2-user@ip-172-31-35-205 nandu]$ wc aSkDddKs.csv
  3371   6547 321741 aSkDddKs.csv
Or
[ec2-user@ip-172-31-35-205 nandu]$ wc -l aSkDddKs.csv
3371 aSkDddKs.csv
[ec2-user@ip-172-31-35-205 nandu]$ wc -w aSkDddKs.csv
6547 aSkDddKs.csv
[ec2-user@ip-172-31-35-205 nandu]$ wc -c aSkDddKs.csv
321741 aSkDddKs.csv

iii. Suppose you wanted to load this file into table in a database. How would you accomplish this?
I can do it in many ways, here I want to use the SQLLDR utility to load this csv file into oracle table. Details bellow.
	1.  Save the file as aSkDddKs.csv on your local machine.
	C:\Users\nandu\AWS\aSkDddKs.csv
	2.Create database table to match with csv file fields. Details bellow.
	CREATE TABLE image_askdddks_data
	(
	image  VARCHAR(200),
	image_id integer,
	image_ref VARCHAR(100),
	image_name VARCHAR(300)
	);
	3. Create the control file to load this csv file from command line using sqlldr. Details bellow, opted out the log file bad file ect in my ctl file. 
	load data
	infile 'C:\Users\nandu\AWS\aSkDddKs.csv'
	insert into table image_askdddks_data
	fields terminated by "," optionally enclosed by '"'
	(image, image_id,image_ref, image_name)

	4. Type the following on the command line and the data will be loaded into table.
	sqlldr SYSTEM/1176 control='C:\Users\nandu\AWS\image_data.ctl'

	5. Check the table for data.
	select * from image_askdddks_data
	
	
3.regular expression to capture the three components of a standard 10 digit US telephone number
	^\s*(?:\+?(\d{1,3}))?[-. (]*(\d{3})[-. )]*(\d{3})[-. ]*(\d{4})(?: *x(\d+))?\s*$
	
i. Adapt your regular expression to support the area code being optional.

	added a question mark after the (\d{3}) for the area code to make optional, see bellow
	^\s*(?:\+?(\d{1,3}))?[-. (]*(\d{3}?)[-. )]*(\d{3})[-. ]*(\d{4})(?: *x(\d+))?\s*$

4. Write standard SQL syntax that will create a users table, including indexes, with the following structure:

It is good practice to generate non important ids with sequence, so the supporting sequence and users table is created with following script.
No need to create the Index on the id which is a primary key as oracle creates indexes on primary key by default.  In case if examiner wants to see 
it code is there with comments.

CREATE SEQUENCE id_seq
  START WITH 1
  INCREMENT BY 1
  MAXVALUE 9223372036854775807
  NOCACHE
  NOCYCLE;
---DROP TABLE user_actions
----DROP TABLE users
CREATE TABLE users
(
  id integer DEFAULT id_seq.NEXTVAL NOT NULL,
  user_name VARCHAR(100),
  email VARCHAR(100),
  signup_date DATE,
  CONSTRAINT pk_id PRIMARY KEY (id)
);

----CREATE UNIQUE INDEX users_id_inx ON users (id);

i. Write SQL that would generate a list of the usernames and the most recent signup_date for that username for all usernames that have 
multiple instances in the table.
For Practical purpose let's insert duplicate user name in this table with following SQL and get the that have 
multiple instances

insert into users VALUES (id_seq.NEXTVAL, 'nmandadi', 'nmandadi1@yahoo.com', '28-AUG-2016');
insert into users VALUES (id_seq.NEXTVAL, 'nmandadi', 'nmandadi1@yahoo.com', '28-AUG-2016');
insert into users VALUES (id_seq.NEXTVAL, 'nmandadi1', 'nmandadi2@yahoo.com', '28-AUG-2016');
insert into users VALUES (id_seq.NEXTVAL, 'nmandadi2', 'nmandadi3@yahoo.com', '28-AUG-2016');
insert into users VALUES (id_seq.NEXTVAL, 'nmandadi3', 'nmandadi41@yahoo.com', '28-AUG-2016');
insert into users VALUES (id_seq.NEXTVAL, 'nmandadi4', 'nmandadi5@yahoo.com', '28-AUG-2016');


select user_name, max(signup_date), count(*)
from users
group by user_name
having count(*) >1;

ii.  Write SQL that will generate a list of unique email addresses for all the usernames returned in the previous question (part i)
This can be done in mulyiple ways with distinct, temporary table, sub query etc.  Please see bellow two answers as sample.
1.
select distinct email from users t1,
(
select user_name, max(signup_date), count(*)
from users
group by user_name
having count(*) >1
) t2
where t1.user_name = t2.user_name;
2. Other way
CREATE GLOBAL TEMPORARY TABLE duplicate_users AS
select user_name, signup_date
from users
group by user_name,signup_date
having count(*) >1;
commit;

select distinct email from users
where user_name not in
(
select user_name 
from duplicate_names
);

iii.
Imagine your schema includes a second table user_actions that contains a user_id column that is a foreign key to the users.id column.
Instead of imagination actual sample table bellow.


CREATE TABLE user_actions
(
  action_id integer NOT NULL,
  user_id integer,
  purchase_amount integer,
  purchase_date DATE,
  CONSTRAINT pk_action_id PRIMARY KEY (action_id),
  CONSTRAINT fk_users_id FOREIGN KEY (user_id)
  REFERENCES users(id)
);

iii. Write SQL that will return the usernames and emails for all users in the users tables that have no rows in the user_actions table.
Can be done in more than one way, please see two answers.
select user_name, email
from users
where id not in
(select user_id from user_actions)

select t1.user_name, t1.email
from users t1
where not exists
(select 1 from user_actions t2 where t1.id = t2.user_id)

iv.  Imagine your "corrupted" table has magically been cleaned such that no username exists on multiple rows in the table. 
Instead of imagination actual table data clean up.

delete from users a
where rowid >
(
  select min(rowid) from users b
  where b.user_name = a.user_name
);
iv.  Write SQL that will modify the structure of the table that will enforce that the multiple username scenario cannot reoccur.

ALTER TABLE users
ADD CONSTRAINT unique_user_name UNIQUE (user_name);

5. What utility would you use to schedule a job under UNIX?
CRONTAB or AT
i. What syntax would you use to schedule a script run.sh to run at 6:15PM every Tuesday during March, June, September and December.

15 18 * Mar,Jun,Sep,Dec 2 /home/nmandadi/run.sh

ii. Adapt this so that it runs only every other Tuesday during those same months

 corntab -e
15 18 * Mar,Jun,Sep,Dec Tue expr `date +\%s` / 604800 \% 2 >/dev/null || /home/nmandadi/run.sh

