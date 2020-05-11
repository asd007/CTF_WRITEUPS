# Problem Statement:

It looks like someone dumped our database. Please help us know what has been leaked ...

Creator: 2phi

screenshots on the way (soon)

# Analysis

Sooo many lines. So much data to process. Fire up WireShark. Basics first. Search for "shkCTF". Nada! Ok, slow and painful it is. 

First interesting thing, from the get-go we see an authentification doing some sql injection stuff.

[injection_1]

Tracing forward, we see that Postgres did not validate the syntax and a redirect later, the syntax validation error got reflected to the attacker, together with data about the  server and the database engine. Bad look!  


The pattern repeats, but the file seems big so we want to narrow down the search. Let's focus on the PGSQL protocol. Much better: 7254 rows (8.8%) of 84682, but still a lot of data to go through. Taking a step back, we're looking for patterns at the larger scale before digging in more detail. 

Scrolling through, we noticed some entries that were different than what we've seen before. What could they mean? 

Nothing particularly interesting about this one. Let's study what happened before.

information_schema, table_catalog? OFFSET 197. Pretty clever. It appears the attacker is exploiting the error message to dump the names of all the tables in the database. Probably an automated attack or full-blown insanity :)

Confirmation (a bit further up). The last table retrieved must have been "user_mapping_options" (out of probably 196 tables).

Further down we see the attacker using the information he had previously obtained information to get column data. (Our pattern seems to be to look for "Success"==<T<C<Z rather than "Error"==<E.

We're getting closer, we need to get to the data retrieval part, which we suspect follows the column retrieval part.

Sure enough, the attacker seems really interested in the users and developers tables. For some reason for this part of the attack he is more painstakingly extracting data, letter by letter. I can't imagine why he could not have exploited the same type of vulnerability as when retrieving the schema/columns. Anyway, moving on.

He found the first letter of a developer's username, it's a 'k', and we found a new "info" pattern for data retrieval. 


The full user name extracted through same process is: k3vin. He tries a whole bunch of other stuff for the 7th letter, but they all fail. A simple check for an empty string would have improved his bruteforce method.

On to passwords, and the first character: s. 




Heart pulsing =)) expecting next characters hkCTF{. Confirmed, the rest is history :)

Flag:

