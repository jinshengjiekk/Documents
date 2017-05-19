- **MySQL中不支持 `FULL JOIN`,使用 `UNION` 加外链接来实现; ()**
- **MySQL中不支持更新从句的表，可用替代方案；()**


####**生日问题，获取最近的生日**

<pre>SELECT
	NAME,
	birthday,

IF (cur > today, cur, next) AS birth_day
FROM
	(
		SELECT
			name,
			birthday,
			today,
			DATE_ADD(
				cur,
				INTERVAL
			IF (
				DAY (birthday) = 29 && DAY (cur) = 28,
				1,
				0
			) DAY
			) AS cur,
			DATE_ADD(
				next,
				INTERVAL
			IF (
				DAY (birthday) = 29 && DAY (next) = 28,
				1,
				0
			) DAY
			) AS next
		FROM
			(
				SELECT
					NAME,
					birthday,
					today,
					DATE_ADD(birthday, INTERVAL diff YEAR) AS cur,
					DATE_ADD(
						birthday,
						INTERVAL diff + 1 YEAR
					) AS next
				FROM
					(
						SELECT
							CONCAT(last_name, '  ', first_name) AS NAME,
							birth_date AS BirthDay,
							(
								YEAR (NOW()) - YEAR (birth_date)
							) AS diff,
							NOW() AS today
						FROM
							employees
					) AS a
			) AS b
	) AS c;</pre>