## Analysis using SQL

#### Task 1. Find the 5 oldest users

<pre>
SELECT id, username, created_at
FROM users
ORDER BY created_at
LIMIT 5;
</pre>


#### Task 2. What day of the week do most users register on?

<pre>
SELECT
  DAYNAME(created_at) AS "day of the week",
  COUNT(username) AS "total registration"
FROM users
GROUP BY DAYNAME(created_at)
ORDER BY COUNT(username) DESC;
</pre>


#### Task 3. Find the users who have never posted a photo

<pre>
SELECT username
FROM users
LEFT JOIN photos
  ON users.id = photos.user_id
WHERE image_url IS NULL;
</pre>


#### Task 4. We're running a new contest to see who can get the most likes on a single photo. Who Won?

<pre>
SELECT username, photo_id AS id, image_url,
  COUNT(likes.user_id) AS Total_Likes
FROM photos
JOIN likes
  ON likes.photo_id = photos.id
JOIN users
  ON photos.user_id = users.id
GROUP BY photo_id, username, image_url
ORDER BY Total_Likes DESC
LIMIT 1;
</pre>


#### Task 5. How many times does the average user post?

<pre>
SELECT
  ROUND((SELECT COUNT(*) FROM photos) / 
	(SELECT COUNT(*) FROM users), 2);
  </pre>


#### Task 6. User Ranking by postings higher to lower

<pre>
SELECT username, COUNT(image_url) AS num_images
FROM photos
JOIN users
  ON users.id = photos.user_id
GROUP BY user_id, username
ORDER BY num_images DESC;
</pre>


#### Task 7. Total Posts by users 

<pre>
SELECT COUNT(*)
FROM photos;
</pre>


#### Task 8. Total numbers of users who have posted at least one time

<pre>
SELECT COUNT(*)
FROM users
WHERE username IN (SELECT username FROM users
JOIN photos
  ON users.id = photos.user_id
GROUP BY username);
</pre>

#### Task 9. What are the top 5 most commonly used hashtags?

<pre>
SELECT tag_name, COUNT(photo_id)
FROM photo_tags
JOIN tags
  ON photo_tags.tag_id = tags.id
GROUP BY tag_name
ORDER BY COUNT(photo_id) DESC;
</pre>


#### Task 10. We have a small problem with bots on our site. Find users who have liked every single photo on the site

<pre>
SELECT user_id, username, COUNT(photo_id)
FROM likes
JOIN users
  ON likes.user_id = users.id
GROUP BY user_id, username
HAVING COUNT(photo_id) = 257;
</pre>


#### Task 11. We also have a problem with celebrities. Find users who have never commented on a photo

<pre>
SELECT username, comment_text
FROM users
LEFT JOIN comments
  ON users.id = comments.user_id
WHERE comment_text IS NULL;
</pre>


#### Task 12. Are we overrun with bots and celebrity accounts? Find the percentage of our users who have either never commented on a photo or have commented on every photo

<pre>
SELECT (SELECT COUNT(*)
       FROM users
       WHERE username IN (SELECT username FROM users
       LEFT JOIN comments
         ON users.id = comments.user_id
       GROUP BY username
       HAVING COUNT(comment_text) = 0))
       AS "Number Of Users who never commented",
       (SELECT COUNT(*) FROM users
       WHERE username IN (SELECT username FROM users
       LEFT JOIN comments
         ON users.id = comments.user_id
       GROUP BY username
       HAVING COUNT(comment_text) = 257))
       * 100 / 100 AS "%",
       (SELECT COUNT(*) FROM users
       WHERE username IN (SELECT username FROM users
       LEFT JOIN comments
         ON users.id = comments.user_id
       GROUP BY username
       HAVING COUNT(comment_text) = 257))
       AS "Number of Users who likes every photos";
</pre>


#### Task 13. Find users who have ever commented on a photo

<pre>
SELECT username, comment_text
FROM users
LEFT JOIN comments
  ON users.id = comments.user_id
GROUP BY users.id
HAVING comment_text IS NOT NULL;
</pre>



#### Task 14. Are we overrun with bots and celebrity accounts? Find the percentage of our users who have either never commented on a photo or have commented on photos before.

<pre>
SELECT (SELECT COUNT(*) FROM users
       WHERE username IN (SELECT username FROM users
       LEFT JOIN comments
         ON users.id = comments.user_id
       GROUP BY username
       HAVING COUNT(comment_text) = 0))
       AS "Number Of Users who never commented",
       (SELECT COUNT(*) FROM users
       WHERE username IN (SELECT username FROM users
       LEFT JOIN comments
         ON users.id = comments.user_id
       GROUP BY username
       HAVING COUNT(comment_text) != 0))
       * 100 / 100 AS "%",
       (SELECT COUNT(*) FROM users
       WHERE username IN (SELECT username FROM users
       LEFT JOIN comments
         ON users.id = comments.user_id
       GROUP BY username
       HAVING COUNT(comment_text) != 0))
       AS "Number of Users who commented on photos";
</pre>

