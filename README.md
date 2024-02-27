# SQL CRUD

## Part 1: Restaurant finder

### Create restaurants table

- **Restaurant_id**(_PRIMARY KEY_) : An integer holds the unique identifier of each restaurant
- **Category**(_TEXT_) : Genre of food (Italian, Chinese, American, Mexican, Japanese, Korean, Thai, French)
- **Name**(_TEXT_) : Name of the restaurant (Ruby's, Konbon, Carbone, Torrisi, Raku, Ippudo, Golden Wuish, Up Thai, ABC Kitchen, Gramercy Tavern, Dudley's, Au Cheval, Take 31, Five Guys, Palma, Mareluna, Sadelle's, Nemesis, Cha Long, Che Li, Tatiana, Mamo, The Modern, Sonnyboy, Fish Cheeks, Chipotle, Dig)
- **Price_tier**(_TEXT_) : Price tier of each restaurant (Cheap, Medium, or Expensive)
- **Neighborhood**(_TEXT_) : A particular NYC neighborhood (Financial District, Tribeca, Soho, Greenwich Village, East Village, Lower East Side, Chinatown, Little Italy, Chelsea, Midtown East, Hell's Kitchen, Upper East Side, Upper West Side, Gramercy)
- **Opening_hours**(_TEXT_) : The opening hours for each restaurant in 24 hour format 
- **Closing_hours**(_TEXT_) : The closing hours for each restaurant in 24 hour format 
- **Average_rating**(_INTEGER_) : An integer (1 - 5) represents the average rating for each restaurant 
- **Good_for_kids**(_BOOLEAN_) : Whether the restaurant is good for kids (True or False)

```sql
create table restaurants (
    Restaurant_id INTEGER PRIMARY KEY,
    Category TEXT,
    Name TEXT,
    Price_tier TEXT,
    Neighborhood TEXT,
    Opening_hours TEXT,
    Closing_hours TEXT,
    Average_rating INTEGER,
    Good_for_kids BOOLEAN
);
```

### Create reviews table ( hold reviews for any restaurant )

- **Review_id**(_PRIMARY KEY_) : An integer holds the unique identifier of each review
- **Restaurant_id**(_Foreign KEY_) : A reference to the primary key of a record in the restaurants table.
- **Review**(_TEXT_) : Reviews for any restaurants

```sql
create table reviews (
    Review_id INTEGER PRIMARY KEY,
    Restaurant_id INTEGER,
    Review TEXT,
    FOREIGN KEY (Restaurant_id) REFERENCES restaurants(Restaurant_id)
);
```

### Import the data

In my computer, I use the command _.import /Users/leyiwang/Desktop/4-sql-crud-Ol1viaWang/data/restaurants.csv restaurants --skip 1_ to import the data [restaurants](./data/restaurants.csv)

```sql
.mode csv
.import ./data/restaurants.csv restaurants --skip 1 # informing SQLite to skip the first row with headers in the file
```

### Queries

1. Find all cheap restaurants in a particular neighborhood (pick Soho).

```sql
SELECT * FROM restaurants WHERE Price_tier = 'Cheap' AND Neighborhood = 'Soho';
```

> **Results**: 

| Restaurant_id | Category | Name          | Price_tier | Neighborhood | Opening_hours | Closing_hours | Average_rating | Good_for_kids |
|---------------|----------|---------------|------------|--------------|---------------|---------------|----------------|---------------|
| 37            | Italian  | Ruby's        | Cheap      | Soho         | 1:20          | 10:28         | 1              | True          |
| 49            | Italian  | Chipotle      | Cheap      | Soho         | 20:29         | 23:50         | 1              | True          |
| 60            | Chinese  | Ippudo        | Cheap      | Soho         | 5:24          | 11:56         | 1              | False         |
| 147           | French   | ABC Kitchen   | Cheap      | Soho         | 21:22         | 18:46         | 2              | True          |
| 159           | American | Mareluna      | Cheap      | Soho         | 17:09         | 5:55          | 2              | True          |
    
There are 25 cheap restaurants in Soho. For simplicity, I only show the first 5 rows.

2. Find all restaurants in a particular genre (pick Chinese) with 3 stars or more, ordered by the number of stars in descending order.

```sql
SELECT * FROM restaurants WHERE category = 'Chinese' AND Average_rating >= 3 ORDER BY Average_rating DESC;
```

> **Results**: 

| Restaurant_id | Category | Name            | Price_tier | Neighborhood       | Opening_hours | Closing_hours | Average_rating | Good_for_kids |
|---------------|----------|-----------------|------------|--------------------|---------------|---------------|----------------|---------------|
| 93            | Chinese  | Au Cheval       | Cheap      | East Village       | 7:01          | 10:14         | 5              | True          |
| 125           | Chinese  | Sadelle's       | Medium     | Little Italy       | 12:23         | 14:23         | 5              | False         |
| 126           | Chinese  | Raku            | Cheap      | Financial District | 17:28         | 6:26          | 5              | True          |
| 131           | Chinese  | Golden Wuish    | Medium     | Gramercy           | 11:11         | 17:19         | 5              | True          |
| 178           | Chinese  | The Modern      | Medium     | Little Italy       | 14:38         | 9:54          | 5              | True          |

There are 71 Chinese restaurants with 3 stars or more. For simplicity, I only show the first 5 rows.

3. Find all restaurants that are open now.

```sql
SELECT * FROM restaurants WHERE strftime('%H:%M', 'now') BETWEEN Opening_hours AND Closing_hours;
```

> **Results**: 

| Restaurant_id | Category | Name      | Price_tier | Neighborhood     | Opening_hours | Closing_hours | Average_rating | Good_for_kids |
|---------------|----------|-----------|------------|------------------|---------------|---------------|----------------|---------------|
| 4             | French   | Raku      | Expensive  | Chelsea          | 12:34         | 23:14         | 4              | False         |
| 6             | American | Au Cheval | Cheap      | Tribeca          | 10:21         | 1:04          | 3              | True          |
| 18            | Chinese  | Sadelle's | Cheap      | East Village     | 13:13         | 23:48         | 3              | True          |
| 27            | Thai     | Cha Long  | Expensive  | Lower East Side  | 15:29         | 23:51         | 1              | False         |
| 33            | Korean   | Nemesis   | Medium     | Lower East Side  | 15:02         | 7:21          | 1              | False         |

There are 208 restaurants that are open now. For simplicity, I only showed the first 5 rows.

4. Leave a review for a restaurant (pick Restaurant with id 1); note that leaving a review has no automatic effect on the average rating of the restaurant.

```sql
INSERT INTO reviews (Restaurant_id, Review) VALUES (1, 'Best restaurant in NYC!');
```

5. Delete all restaurants that are not good for kids.

```sql
DELETE FROM restaurants WHERE Good_for_kids = 'False';
```

6. Find the number of restaurants in each NYC neighborhood.

```sql
SELECT Neighborhood, COUNT(*) AS number_of_restaurants FROM restaurants GROUP BY Neighborhood;
```

> **Results**: 

| Neighborhood        | Number of Restaurants |
|---------------------|-----------------------|
| Chelsea             | 33                    |
| Chinatown           | 32                    |
| East Village        | 28                    |
| Financial District  | 41                    |
| Gramercy            | 40                    |
| Greenwich Village   | 35                    |
| Hell's Kitchen      | 31                    |
| Little Italy        | 41                    |
| Lower East Side     | 29                    |
| Midtown East        | 46                    |
| Soho                | 32                    |
| Tribeca             | 28                    |
| Upper East Side     | 37                    |
| Upper West Side     | 36                    |

## Part 2: Social media app

### Create users table

- **User_id** : An integer holds the unique identifier of each user
- **Email** : Each user has his/her own unique email
- **Password** : Each user has his/her own unique password
- **Username** : Each user has his/her own unique username

```sql
create table users (
    User_id INTEGER PRIMARY KEY,
    Email TEXT,
    Password TEXT,
    Username TEXT
);
```

### Create posts table (messages and stories)

**Messages**:
- Messages consist of text only.
- Messages are sent from one user to another specific user. 
- Messages become invisible immediately after view and don't show up in the app thereafter.
- Messages are never actually deleted from the database table, even when invisible to the user (the social media company 
that produces the app keeps 'deleted' content in its database for future data harvesting, monetization purposes, and more.)

**Stories**
- Stories consist of text only.
- Stories are public and every user can see them.
- Stories become invisible 24 hours after posting and don't show up in the app thereafter.
- Stories are never deleted from the database table, even when invisible to the user.

```sql
create table posts (
    Post_id INTEGER PRIMARY KEY,
    User_id INTEGER,
    Recipient_id INTEGER,
    Content TEXT,
    Type_is TEXT,
    Visible INTEGER,
    Created_at DATE,
    FOREIGN KEY (User_id) REFERENCES users(User_id),
    FOREIGN KEY (Recipient_id) REFERENCES users(User_id)
);
```

### Practice Data (Import the data)

In my computer, I use the command _.import /Users/leyiwang/Desktop/4-sql-crud-Ol1viaWang/data/users.csv users --skip 1_  and _.import /Users/leyiwang/Desktop/4-sql-crud-Ol1viaWang/data/posts.csv posts --skip 1_ to import the data [users](./data/users.csv) and [posts](./data/posts.csv)

```sql
.mode csv
.import ./data/users.csv users --skip 1 # informing SQLite to skip the first row with headers in the file
.import ./data/posts.csv posts --skip 1 # informing SQLite to skip the first row with headers in the file
```

### Queries

1. Register a new User.

```sql
INSERT INTO users (Email, Password, Username) VALUES ('lw2808@nyu.edu', 'leyiwang0718@', 'leyiwang123');
```

2. Create a new Message sent by a particular User (User 1) to a particular User (User 2).

```sql
INSERT INTO posts (User_id, Recipient_id, Content, Type_is, Visible, Created_at) VALUES (1, 2, 'hello', 'Message', 1, '2/26/24');
```

3. Create a new Story by a particular User (User 3).

```sql
INSERT INTO posts (User_id, Content, Type_is, Visible, Created_at) VALUES (3, 'Nice to meet you', 'Story', 1, '2/26/24');
```

4. Show the 10 most recent visible Messages and Stories, in order of recency.

Since my posts.csv use MM/DD/YY to represent the created date, which can't be read correctly by my SQL, I have to convert MM/DD/YY to YYYY-MM-DD firstly. The SQL reads the **Created_at** column as a string, so I can't directly ORDER BY **Created_at**. 
- The command -- '20' || substr(Created_at, -2) -- takes the last two characters of the year value (For example, for date 2/26/24, I take 24 and prefix it with 20 to create 2024)
- The command -- substr(Created_at, 1, instr(Created_at, '/') - 1) -- gets the month value through reading from the first character to the first slash (For example, for date 2/26/24, I read from 2 and stop at the first slash, obtaining 2)
- The command -- '0' || -- prefixes the month value with 0 ahead to make sure that the month value has two digits (For example, if the month value is 2, then it becomes 02)
- The command -- substr(..., -2) -- guarantees that I always take the last two digits of the month value if there are three characters after adding 0 before (For example, if the month value is 12, then it becomes 012, which is incorrect because I only want 12)
- The command -- instr(Created_at, '/') + 1 -- gets the day value through reading from the first slash 
- The command -- instr(substr(Created_at, instr(Created_at, '/') + 1), '/') - 1 -- lets me stop at the second slash (For example, for date 2/26/24, I read from 2 and stop at the second slash, obtaining 26)
- The command -- '0' || -- prefixes the day value with 0 ahead to make sure that the day value has two digits 
- The command -- substr(..., -2) -- guarantees that I always take the last two digits of the day value if there are three characters after adding 0 before
- I use "-" to concatenate all parts above to form YYYY-MM-DD

```sql
SELECT
    Post_id,
    User_id,
    Recipient_id,
    Content,
    Type_is,
    Visible,
    '20' || substr(Created_at, -2) || '-' || 
    substr('0' || substr(Created_at, 1, instr(Created_at, '/') - 1), -2) || '-' ||
    substr('0' || substr(Created_at, instr(Created_at, '/') + 1, instr(substr(Created_at, instr(Created_at, '/') + 1), '/') - 1), -2) AS sortable_date
FROM posts WHERE Visible = 1 ORDER BY sortable_date DESC LIMIT 10;
```

> **Results**: 

| Post_id | User_id | Recipient_id | Content                                                                 | Type_is | Visible | Created_at |
|---------|---------|--------------|-------------------------------------------------------------------------|---------|---------|------------|
| 2001    | 1       | 2            | hello                                                                   | Message | 1       | 2024-02-26 |
| 2002    | 3       |              | Nice to meet you                                                        | Story   | 1       | 2024-02-26 |
| 113     | 141     | 437          | non sodales sed tincidunt eu felis fusce posuere felis                  | Message | 1       | 2024-02-25 |
| 204     | 910     | 824          | id pretium iaculis diam erat fermentum justo                            | Message | 1       | 2024-02-25 |
| 206     | 958     | 415          | augue vestibulum rutrum rutrum neque aenean auctor gravida sem praesent | Message | 1       | 2024-02-25 |
| 214     | 302     | 171          | ut blandit non interdum in ante vestibulum ante ipsum                   | Message | 1       | 2024-02-25 |
| 239     | 428     | 108          | sit amet sapien dignissim vestibulum vestibulum ante ipsum              | Message | 1       | 2024-02-25 |
| 263     | 12      | 207          | auctor sed tristique in tempus sit                                      | Message | 1       | 2024-02-25 |
| 272     | 767     | 759          | placerat ante nulla justo aliquam quis                                  | Message | 1       | 2024-02-25 |
| 365     | 789     | 837          | donec semper sapien a libero                                            | Message | 1       | 2024-02-25 |

5. Show the 10 most recent visible Messages sent by a particular User(User 745) to a particular User (User 495), in order of recency.

```sql
SELECT * FROM posts WHERE User_id = 745  AND Recipient_id = 495 AND Type_is = 'Message' AND Visible = 1 ORDER BY Created_at DESC LIMIT 10;
```

> **Results**:

| Post_id | User_id | Recipient_id | Content                          | Type_is | Visible | Created_at |
|---------|---------|--------------|----------------------------------|---------|---------|------------|
| 667     | 745     | 495          | quam pharetra magna ac consequat | Message | 1       | 2/12/2024  |

In this dataset, User 745 only sent one message to User 495.

6. Make all Stories that are more than 24 hours old invisible.

```sql
UPDATE posts
SET Visible = 0
WHERE Type_is = 'Story'
AND ROUND((
    JULIANDAY('now') - JULIANDAY(
        '20' || substr(Created_at, -2) || '-' || 
        substr('0' || substr(Created_at, 1, instr(Created_at, '/') - 1), -2) || '-' ||
        substr('0' || substr(Created_at, instr(Created_at, '/') + 1, instr(substr(Created_at, instr(Created_at, '/') + 1), '/') - 1), -2)
    )
) * 24) > 24;
```

7. Show all invisible Messages and Stories, in order of recency.

Since my posts.csv use MM/DD/YY to represent the created date, which can't be read correctly by my SQL, I have to convert MM/DD/YY to YYYY-MM-DD firstly.

```sql
SELECT
    Post_id,
    User_id,
    Recipient_id,
    Content,
    Type_is,
    Visible,
    '20' || substr(Created_at, -2) || '-' || 
    substr('0' || substr(Created_at, 1, instr(Created_at, '/') - 1), -2) || '-' ||
    substr('0' || substr(Created_at, instr(Created_at, '/') + 1, instr(substr(Created_at, instr(Created_at, '/') + 1), '/') - 1), -2) AS sortable_date
FROM posts WHERE Visible = 0 ORDER BY sortable_date DESC;
```

 > **Results**:

| Post_id | User_id | Recipient_id | Content                                                        | Type_is | Visible | Created_at |
|---------|---------|--------------|----------------------------------------------------------------|---------|---------|------------|
| 39      | 986     | 54           | lectus in est risus auctor sed tristique in tempus sit         | Message | 0       | 2024-02-25 |
| 42      | 234     | 839          | quisque id justo sit amet                                      | Message | 0       | 2024-02-25 |
| 142     | 818     | 228          | volutpat quam pede lobortis ligula sit amet                    | Message | 0       | 2024-02-25 |
| 145     | 528     | 348          | tincidunt in leo maecenas pulvinar lobortis est phasellus sit  | Message | 0       | 2024-02-25 |
| 176     | 953     | 774          | libero nam dui proin leo odio porttitor                        | Message | 0       | 2024-02-25 |

These are the first 5 rows of invisible Messages and Stories in order of recency.

8. Show the number of posts by each User.

```sql
SELECT User_id, COUNT(*) as number_of_posts FROM posts GROUP BY User_id;
```
> **Results**:

| User_id | number_of_posts |
|---------|-----------------|
| 1       | 3               |
| 2       | 2               |
| 3       | 4               |
| 4       | 2               |
| 5       | 3               |
| 6       | 2               |
| 7       | 1               |
| 8       | 4               |
| 9       | 1               |
| 10      | 1               |

There are 1000 users. Table above only shows the number of posts by the first 10 users.

9. Show the post text and email address of all posts and the User who made them within the last 24 hours.

Since my posts.csv use MM/DD/YY to represent the created date, which can't be read correctly by my SQL, I have to convert MM/DD/YY to YYYY-MM-DD firstly.

```sql
SELECT
    p.Content AS Post_Text,
    u.Email AS User_Email
FROM
    posts p
JOIN
    users u ON p.User_id = u.User_id
WHERE
    p.Visible = 1 AND
    DATE('20' || substr(p.Created_at, -2) || '-' || 
    substr('0' || substr(p.Created_at, 1, instr(p.Created_at, '/') - 1), -2) || '-' ||
    substr('0' || substr(p.Created_at, instr(p.Created_at, '/') + 1, instr(substr(p.Created_at, instr(p.Created_at, '/') + 1), '/') - 1), -2)) 
    >= DATE('now', '-1 day');
```

> **Results**:

| Post_Text          | User_Email                  |
|--------------------|-----------------------------|
| hello              | kdurn0@istockphoto.com      |
| Nice to meet you   | hcorradengo2@nbcnews.com    |

10. Show the email addresses of all Users who have not posted anything yet.

```sql
SELECT Email FROM Users WHERE User_id NOT IN (SELECT DISTINCT User_id FROM posts);
```

> **Results**:

| Email Address                  |
|--------------------------------|
| npeetersa@bing.com             |
| sschoolfielde@businesswire.com |
| jlongom@prnewswire.com         |
| lpioli12@amazonaws.com         |
| ivedenyapin18@altervista.org   |

There are 124 users have not posted anything yet. The table only shows the first 5 email addresses.