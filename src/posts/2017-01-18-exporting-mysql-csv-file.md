---
title: 'Exporting from MySQL to CSV file'
date: 2017-01-18
---

We often have to export data from MySQL to other applications; this could be to further analyse the data, gather user emails for a newsletter or similar. Usually these applications, CSV is probably the most common format for data exporting like this.

Luckily SQL select output, with its rows and columns, if well suited for CSV output.

You can easily export straight from the MySQL client to a CSV file, by appending the expected CSV format to the end of the query:
 
<pre class="lang:mysql decode:true ">
[SQL QUERY]
INTO OUTFILE '[FILE]'
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n';
</pre> 

For example:

<pre class="lang:mysql decode:true ">
SELECT * FROM users
INTO OUTFILE '/var/lib/mysql-files/users.csv'
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n';
</pre> 

The last three lines can be customised based on your needs.

<b>NOTE:</b> The default MySQL settings only allows writing files to the <code>/var/lib/mysql-files/</code> directory.

If you do not have permissions to write files from the MySQL client, the same can be accomplished from the commandline:
 
<pre class="lang:sh decode:true ">
echo "[QUERY]"|mysql -u [DBUSER] -p [DBNAME]|sed s/'/\'/;s/\t/\",\"/g;s/^/\"/;s/$/\"/;s/\n//g" > [FILE]
</pre>

For example:
<pre class="lang:sh decode:true ">
echo "SELECT * FROM users"|mysql -u my_user -p my_database|sed s/'/\'/;s/\t/\",\"/g;s/^/\"/;s/$/\"/;s/\n//g" > users.csv
</pre>

The regex is, of course, courtesy <a href="http://stackoverflow.com/a/5395421/2217294">stackoverflow</a>.



<blockquote cite="http://stackoverflow.com/questions/356578/how-to-output-mysql-query-results-in-csv-format">
Regex Explanation:

s/// means substitute what's between the first // with what's between the second //
the "g" at the end is a modifier that means "all instance, not just first"
^ (in this context) means beginning of line
$ (in this context) means end of line
So, putting it all together:

s/'/\'/          replace ' with \'
s/\t/\",\"/g     replace all \t (tab) with ","
s/^/\"/          at the beginning of the line place a "
s/$/\"/          at the end of the line place a "
s/\n//g          replace all \n (newline) with nothing
</blockquote>
