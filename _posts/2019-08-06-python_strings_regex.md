---
layout: post
title: Python Regular Expressions and String Manipulation
date: 2019-08-06 00:00:00 +0100
description: # Add post description (optional)
img: 2019-08-06-main_pic.jpg # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [Python, Regular Expressions]
---

Text processing and manipulation are often critical in the process of data cleansing and preparation. Machine learning models which use such data include _sentiment analysis of social media, language translations, filtering spam messages, parsing and extracting data from websites_ and many more. Regular expressions allow for these tasks to be completed way faster and more efficiently.

## Basic String Methods
Python recognises any sequence of characters placed inside quotes as a string object. These quotes may be both single and double. If we want to actually use a quote as a part of a string, we need to enclose the whole string in the other type of quote, e.g.:

```python
print('This isn't going to work')
```

```
  File "<input>", line 1
    print('This isn't going to work!')
                    ^
SyntaxError: invalid syntax
```
<br>
```python
print("But this isn't going to cause any problems!")
```
```
But this isn't going to cause any problems!
```

**<span style="font-family:Courier; color:black">len()</span>** method can be used to count the number of characters in a string.

```python
my_string = "I wonder how many characters are in this string..."
len(my_string)
```
```
50
```

Two or more strings can be concatenated using **<span style="font-family:Courier; color:black">+</span>** operator.

```python
str_1 = 'just'
str_2 = 'random'
str_3 = 'words'

print(str_1 + " " + str_2 + " " + str_3)
```
```
'just random words'
```

Same thing can be done with **<span style="font-family:Courier; color:black">join()</span>** method with a list of strings as an argument. Note that the method is called on a separating element (here whitespace).

```python
print(" ".join([str_1, str_2, str_3]))
```
```
'just random words'
```

A specific character or a group of characters within a string can be accessed using bracket notation
**<span style="font-family:Courier; color:black">[start:stop:stride]</span>**.

```python
my_string = "fantastic"
print(my_string[0]) # first character
print(my_string[1:4]) # some middle characters
print(my_string[-1]) # last character
print(my_string[::2]) # every second character
print(my_string[::-1]) # reversed string
```
```
f
ant
c
fnatc
citsatnaf
```

Methods for adjusting string capitalization include: **<span style="font-family:Courier; color:black">lower()</span>**, **<span style="font-family:Courier; color:black">upper()</span>** and **<span style="font-family:Courier; color:black">capitalize()</span>**.

```python
my_string = "whAT HaPPened to THESE leTTerS?"
print(my_string.upper())
print(my_string.lower())
print(my_string.capitalize())
```
```
WHAT HAPPENED TO THESE LETTERS?
what happened to these letters?
What happened to these letters?
```

A string can be split into a list of substrings using **<span style="font-family:Courier; color:black">split()</span>** and **<span style="font-family:Courier; color:black">rsplit()</span>** methods. The former starts splitting the string from the left, the latter from the right. If **<span style="font-family:Courier; color:black">maxsplit</span>** paramater is not specified, both methods work in the same way - they give the maximum possible number of substrings. Also, the whitespace is the default split character so it does not need to be explicitly specified.

```python
my_string = "This string should be split into separate parts"

my_string.split(sep=" ", maxsplit=1)
my_string.rsplit(sep=" ", maxsplit=1)
my_string.split(sep=" ", maxsplit=len(my_string))
my_string.split(sep=" ")
my_string.split()
```

```
['This', 'string should be split into separate parts']
['This string should be split into separate', 'parts']
['This', 'string', 'should', 'be', 'split', 'into', 'separate', 'parts']
['This', 'string', 'should', 'be', 'split', 'into', 'separate', 'parts']
['This', 'string', 'should', 'be', 'split', 'into', 'separate', 'parts']
```

**<span style="font-family:Courier; color:black">strip()</span>** method can be used to remove leading and trailing whitespace. **<span style="font-family:Courier; color:black">rstrip()</span>** or **<span style="font-family:Courier; color:black">lstrip()</span>** can be used to remove only trailing or only leading whitespace respectively.

```python
my_string = "   This string should be stripped!    "

my_string.strip()
my_string.rstrip()
my_string.lstrip()
```
```
'This string should be stripped!'
'   This string should be stripped!'
'This string should be stripped!    '
```

Particular part of a string can be replaced with another string with **<span style="font-family:Courier; color:black">replace()</span>** method.

```python
my_string.replace('stripped', 'replaced')
```
```
'   This string should be replaced!    '
```

Please note that string methods can be **chained**.

```python
my_string.replace('stripped', 'replaced').strip().upper()
```
```
'THIS STRING SHOULD BE REPLACED!'
```

**<span style="font-family:Courier; color:black">find()</span>** method returns the starting position of the first occurence of a given string. Optionally we can include **<span style="font-family:Courier; color:black">start</span>** and **<span style="font-family:Courier; color:black">end</span>** parameters (where starting position is inclusive but ending position is not).

```python
my_string = "Mr Heckles has a cat. Or he could have a cat. You owe him a cat."
# Any Friends fans here? ;)

my_string.find('cat')
my_string.find('cat', 20)
```
```
17
41
```

If the substring is not found the method returns **<span style="font-family:Courier; color:black">-1</span>**.

```python
my_string.find('dog')
```
```
-1
```

**<span style="font-family:Courier; color:black">index()</span>** method works nearly the same way as **<span style="font-family:Courier; color:black">find()</span>**, except that now if the substring is not found it raises a ValueError.

```python
my_string.index('cat')
my_string.index('dog')
```
```
17
Traceback (most recent call last):
  File "<input>", line 1, in <module>
ValueError: substring not found
```

We can count how many times a substring occurs in a string with **<span style="font-family:Courier; color:black">count()</span>** method. Also this method can take optional **<span style="font-family:Courier; color:black">start</span>** and **<span style="font-family:Courier; color:black">end</span>** parameters.

```python
my_string.count('cat')
my_string.count('cat', 25)
```
```
3
2
```

Getting back to **<span style="font-family:Courier; color:black">replace()</span>** method - we can specify how many occurencies of a substring should be replaced.

```python
my_string.replace('cat', 'dog')
my_string.replace('cat', 'dog', 1)
```
```
'Mr Heckles has a dog. Or he could have a dog. You owe him a dog.'
'Mr Heckles has a dog. Or he could have a cat. You owe him a cat.'
```

Replace might be especially useful when preparing data for sentiment analysis algorithms. Some algorithms might have problems with negations. For example, a movie review might say the movie was _not good_. Sentiment analysis models will split the whole review into separate words or bags of words. They might ignore the _not_ part and conclude from the word _good_ that the sentiment of the review is positive. This issue could be avoided by replacing words preceded by _not_ with their antonyms.

## Positional String Formatting

String formatting is used for inserting a custom string or a variable in a predefined text. The placeholder for a string is marked by curly brackets **<span style="font-family:Courier; color:black">{}</span>**. Let's see an example:

```python
text = "Mr Heckles has a {}. Or he could have a {}. You owe him a {}."
text.format('cat', 'cat', 'cat')
text.format('cat', 'dog', 'fish')
```
```
'Mr Heckles has a cat. Or he could have a cat. You owe him a cat.'
'Mr Heckles has a cat. Or he could have a dog. You owe him a fish.'
```

This method replaces all placeholders with passed values in order of their appearance. This can be changed if we add reordered index numbers within the brackets.

```python
text = "Mr Heckles has a {1}. Or he could have a {2}. You owe him a {0}."
text.format('cat', 'dog', 'fish')
```
```
'Mr Heckles has a dog. Or he could have a fish. You owe him a cat.'
```

Now that we know this, we can make the very first example from this section more concise:

```python
text = "Mr Heckles has a {0}. Or he could have a {0}. You owe him a {0}."
text.format('cat')
```
```
'Mr Heckles has a cat. Or he could have a cat. You owe him a cat.'
```

Placeholders can also have their own names.

```python
text = "{owner} has a {animal}. Or he could have a {animal}. " \
       "You owe him a {animal}."
text.format(owner="Mr Heckles", animal="cat")
```
```
'Mr Heckles has a cat. Or he could have a cat. You owe him a cat.'
```

We can format the data we pass into placeholders which might be especially useful with numbers or dates.
In the example below **<span style="font-family:Courier; color:black">f</span>** was used to get float. Other common format specifiers include **<span style="font-family:Courier; color:black">e</span>** for scientific notation and **<span style="font-family:Courier; color:black">d</span>** for digit (integer number).

```python
text = "There is actually {chance:.2f}% chance that Mr Heckles had a cat."
text.format(chance=0.0512349)
```
```
'There is actually 0.05% chance that Mr Heckles had a cat.'
```

Similarly, a date can be formatted:

```python
from datetime import datetime

print("Today's date: {}".format(datetime.now()))
print("Today's date: {:%Y-%m-%d}".format(datetime.now()))
```
```
Today's date: 2019-08-06 16:25:23.892789
Today's date: 2019-08-06
```

Finally, a dictionary can be used to replace placeholders with specific values.

```python
my_dict = {
    "animal": "cat",
    "owner": "Mr Heckles"
}

text = "{d[owner]} has a {d[animal]}. Or he could have a {d[animal]}. " \
       "You owe him a {d[animal]}."

text.format(d=my_dict)
```
```
'Mr Heckles has a cat. Or he could have a cat. You owe him a cat.'
```

Similar operations can be done using **f-format** (adding the prefix **<span style="font-family:Courier; color:black">f</span>** before the string) as in the example:

```python
owner = "Mr Heckles"
animal = "cat"

print(f"{owner} has a {animal}. Or he could have a {animal}. You owe him a {animal}.")
```
```
Mr Heckles has a cat. Or he could have a cat. You owe him a cat.
```

There is just a small difference when accessing dictionaries in f-strings. The index of the dictionary has to be put in quotes:

```python
my_dict = {
    "animal": "cat",
    "owner": "Mr Heckles"
}

print(f"{my_dict[owner]} has a {my_dict[animal]}. "
      f"Or he could have a {my_dict[animal]}. "
      f"You owe him a {my_dict[animal]}.")
```
```
Traceback (most recent call last):
  File "<input>", line 1, in <module>
KeyError: 'Mr Heckles'
```
<br>
```python
print(f"{my_dict['owner']} has a {my_dict['animal']}. "
      f"Or he could have a {my_dict['animal']}. "
      f"You owe him a {my_dict['animal']}.")
```
```
Mr Heckles has a cat. Or he could have a cat. You owe him a cat.
```

One of the greatest advantages of f-strings is that they allow for inline operations...

```python
number_1 = 10
number_2 = 4

print(f"{number_1} times {number_2} equals {number_1*number_2}")
```
```
10 times 4 equals 40
```

...or we can even call functions inside!

```python
def multiply(a, b):
    return a*b

print(f"{number_1} times {number_2} equals {multiply(number_1, number_2)}")
```
```
10 times 4 equals 40
```

**<span style="font-family:Courier; color:black">f-strings</span>** were introduced in Python 3.6. Their use is advised as they are faster than **<span style="font-family:Courier; color:black">str.format()</span>** methods.

## Regular Expressions

**Regular Expressions** (**regex**, **regexp**) are strings containing a combination of normal characters and special metacharacters used to describe patterns for finding text within a larger text. Potential use includes finding and replacing text, string validation or renaming a bunch of files. Python provides a package named **<span style="font-family:Courier; color:black">re</span>** for handling regex.

```python
import re
```

To find all matches of a pattern we use **<span style="font-family:Courier; color:black">findall()</span>** method. The result is a list of all matches found.

```python
text = "Mr Heckles has a cat. Or he could have a cat. You owe him a cat."

re.findall(r"cat", text)
```
```
['cat', 'cat', 'cat']
```

To split a string at each pattern match we can use **<span style="font-family:Courier; color:black">split()</span>** method. In the example below the text is split by every match of exclamation mark. The result is a list of substrings.

```python
text = "Mr Heckles has a cat! Or he could have a cat! You owe him a cat!"

re.split(r"!", text)
```
```
['Mr Heckles has a cat', ' Or he could have a cat', ' You owe him a cat', '']
```

With **<span style="font-family:Courier; color:black">sub()</span>** method matches can be replaced with another string.

```python
re.sub(r"!", "...", text)
```
```
'Mr Heckles has a cat... Or he could have a cat... You owe him a cat...'
```

### Metacharacters

**<span style="font-family:Courier; color:black">\d</span>** represents a digit:

```python
tweets = "10 tweets by @user1. 15 tweets by @user624. 99 tweets by @userN"

re.findall(r"\d", tweets)
```
```
['1', '0', '1', '1', '5', '6', '2', '4', '9', '9']
```

**<span style="font-family:Courier; color:black">\D</span>** represents a non-digit:

```python
re.findall(r"\D", tweets)
```
```
[' ', 't', 'w', 'e', 'e', 't', 's', ' ', 'b', 'y', ' ', '@', 'u', 's', 'e', 'r', '.', ' ', ' ', 't', 'w', 'e', 'e', 't', 's', ' ', 'b', 'y', ' ', '@', 'u', 's', 'e', 'r', '.', ' ', ' ', 't', 'w', 'e', 'e', 't', 's', ' ', 'b', 'y', ' ', '@', 'u', 's', 'e', 'r', 'N']
```

Using what we already learned we could search for example for usernames that are followed by at least one digit:

```python
re.findall(r"user\d", tweets)
```
```
['user1', 'user6']
```

**<span style="font-family:Courier; color:black">\w</span>** represents any word character (alphanumeric and underscore). Let's find every occurence of string "user" followed by a word character:

```python
re.findall(r"user\w", tweets)
```
```
['user1', 'user6', 'userN']
```

**<span style="font-family:Courier; color:black">\W</span>** represents any non-word (special) character:

```python
re.findall(r"\W", tweets)
```
```
[' ', ' ', ' ', '@', '.', ' ', ' ', ' ', ' ', '@', '.', ' ', ' ', ' ', ' ', '@']
```

**<span style="font-family:Courier; color:black">\s</span>** represents a whitespace (i.e. the space, the tab, the newline and the carriage return):

```python
re.findall(r"\d\d\stweets", tweets)
```
```
['10 tweets', '15 tweets', '99 tweets']
```

**<span style="font-family:Courier; color:black">\S</span>** represents a non-whitespace:

```python
re.findall(r"user\S", tweets)
```
```
['user1', 'user6', 'userN']
```

With **<span style="font-family:Courier; color:black">sub()</span>** method we can replace a specific regex match with another string.

```python
re.sub(r"\sby", ":", tweets)
```
```
'10 tweets: @user1. 15 tweets: @user624. 99 tweets: @userN'
```

### Quantifiers

**<span style="font-family:Courier; color:black">search()</span>** method takes a regex pattern and a string and tells whether there is a match.

```python
re.search(r"\d\d\stweets", tweets)
```
```
<re.Match object; span=(0, 9), match='10 tweets'>
```
<br>
```python
pswd = "password9876"

re.search(r"\w\w\w\w\w\w\w\w\d\d\d\d", pswd)
```
```
<re.Match object; span=(0, 12), match='password9876'>
```

A quantifier **<span style="font-family:Courier; color:black">{n}</span>** can be used to tell the regex engine how many times exactly a character on its left should be matched.

```python
re.search(r"\w{8}\d{4}", pswd)
```
```
<re.Match object; span=(0, 12), match='password9876'>
```

A **<span style="font-family:Courier; color:black">+</span>** tells to search for a pattern where a character on its left is matched **one or more times**.

```python
pswds = "password9876, password 1, password123, password"

re.findall(r"password\d+", pswds)
```
```
['password9876', 'password123']
```

A **<span style="font-family:Courier; color:black">\*</span>** tells to search for a pattern where a character on its left is matched **zero or more times**.


```python
re.findall(r"password\d*", pswds)
```
```
['password9876', 'password', 'password123', 'password']
```

A **<span style="font-family:Courier; color:black">?</span>** tells to search for a pattern where a character on its left is matched **zero times or once**.

```python
text = "Center is American spelling. Centre is British"

re.findall(r"Cente?re?", text)
```
```
['Center', 'Centre']
```


A **<span style="font-family:Courier; color:black">{n,m}</span>** quantifier tells to search for a pattern where a character on its left is matched at least **n** and at most **m** times.

```python
pswds = "password9876, password11, password123, password"

re.findall(r"password\d{3,4}", pswds)
```
```
['password9876', 'password123']
```
<br>
```python
phone_numbers = "123-123-1234, 1-1-100, 999-456-123456"

re.findall(r"\d{3}-\d{3}-\d{4,}", phone_numbers)
```
```
['123-123-1234', '999-456-123456']
```

It's important to remember that quantifiers apply to exactly one character immediately on their left.

**<span style="font-family:Courier; color:black">.</span>** matches any character except newline. If we want to actually search for a dot, we have to precede it by an escape character **<span style="font-family:Courier; color:black">\\</span>**.

```python
my_blog = "https://etlgirl.github.io/"

re.findall(r"http.+", my_blog)
```
```
['https://etlgirl.github.io/']
```
<br>
```python
re.findall(r"http.+\.com", my_blog)
```
```
[]
```
<br>
```python
text = "This is some text. It has 3 sentences. Let's split them."

re.split(r"\.\s", text)
```
```
['This is some text', 'It has 3 sentences', "Let's split them."]
```

### search() vs match()

There are two different methods that can be used to find a regex match - **<span style="font-family:Courier; color:black">search()</span>** and **<span style="font-family:Courier; color:black">match()</span>**. The difference is that the **<span style="font-family:Courier; color:black">match()</span>** method starts searching at the very beginning of a string. So in the following case both methods return the same result...

```python
text = "Center is American spelling. Centre is British"

re.search(r"Cente?re?", text)

re.match(r"Cente?re?", text)
```
```
<re.Match object; span=(0, 6), match='Center'>
<re.Match object; span=(0, 6), match='Center'>
```

...but in this case the **<span style="font-family:Courier; color:black">match()</span>** method returns no result.

```python
re.search(r"Centre", text)

re.match(r"Centre", text)
```
```
<re.Match object; span=(29, 35), match='Centre'>

```

### Other Special Characters

**<span style="font-family:Courier; color:black">^</span>** indicates that search for a match should be started at the beginning of a string.

**<span style="font-family:Courier; color:black">$</span>** indicates that search for a match should be done at the end of a string.

```python
text = "the 70s music is not as good as the 80s, but my favourite is the 90s"

re.findall(r"the\s\d{2}s", text)

re.findall(r"^the\s\d{2}s", text)

re.findall(r"the\s\d{2}s$", text)
```
```
['the 70s', 'the 80s', 'the 90s']
['the 70s']
['the 90s']
```

**<span style="font-family:Courier; color:black">|</span>** is an **OR** operator.

```python
text = "The 70s music is not as good as the 80s, but my favourite is the 90s"

re.findall(r"the\s\d{2}s", text)

re.findall(r"The\s\d{2}s|the\s\d{2}s", text)
```
```
['the 80s', 'the 90s']
['The 70s', 'the 80s', 'the 90s']
```

Square brackets **<span style="font-family:Courier; color:black">[ ]</span>** indicate a set of characters.

```python
re.findall(r"[Tt]he\s\d{2}s", text)
```
```
['The 70s', 'the 80s', 'the 90s']
```

In below example we are looking for a **set of one or more letters a-z either lower or uppercase** followed by whitespace, two digits and an 's'.

```python
re.findall(r"[a-zA-Z]+\s\d{2}s", text)
```
```
['The 70s', 'the 80s', 'the 90s']
```

**<span style="font-family:Courier; color:black">^</span>** used within square brackets **transforms the expression to negative**. In the example below we look for usernames that do not start with a number.

```python
users = "@admin, @123crazyduck, @999guest"

re.findall(r"@[^0-9,]+", users)
```
```
['@admin']
```

## Greedy vs Non-Greedy Matching

All quantifiers presented above (\*, +, ?, {n,m}) are **greedy operators** by default. It means they try to match as many characters as possible (and so return the longest match possible).  

**Non-greedy (lazy)** quantifiers try to match as few characters as possible, therefore returning the shortest possible match. We can obtain a non-greedy quantifier by appending a **<span style="font-family:Courier; color:black">?</span>** sign to a greedy quantifier.

```python
example = "12345example"

re.match(r"\d+", example) # greedy
re.match(r"\d+?", example) # non-greedy
```
```
<re.Match object; span=(0, 5), match='12345'>
<re.Match object; span=(0, 1), match='1'>
```

In the first example the quantifier will start by matching the first digit and then go on and search for more digits. It will keep going and match more and more digits until no other digit can be matched. The final result will be _12345_. Non-greedy quantifier when given the same pattern looks for a digit and after it finds the first one it stops, since one digit is as few as we need. The final result will be _1_.

Below is an example of how non-greedy quantifiers can be used for removing HTML tags.

```python
html = "Example of an <b>HTML</b> text. Let's remove <i>all tags</i>!"

re.sub(r"<.+?>", "", html)
```
```
"Example of an HTML text. Let's remove all tags!"
```

If we used a greedy quantifier it would match everything there is between the first opening tag and the last closing tag (and give a result that in this case makes absolutely no sense).

```python
re.sub(r"<.+>", "", html)
```
```
'Example of an !'
```

## Capturing Groups

Let's start by looking at an example:

```python
text = "All my friends have pets. Anna has 2 dogs. " \
       "Mark has 2 cats. And George has 101 fish!"
```

The task is to obtain from the text information about the name of the pet owner, number of pets and the type of pet. This could be done as below:

```python
re.findall(r"[A-Za-z]+\shas\s\d+\s[a-z]+", text)
```
```
['Anna has 2 dogs', 'Mark has 2 cats', 'George has 101 fish']
```

Not bad. However, we can use groups to get a result which can be easily transformed to pandas DataFrame or other _analysis-friendly_ structure. Individual groups are placed in round brackets **<span style="font-family:Courier; color:black">()</span>**. They get numbers assigned to them starting from Group 1, and the whole regex expression is a Group 0.

```python
re.findall(r"([A-Za-z]+)\shas\s(\d+)\s([a-z]+)", text)

# Group 1: ([A-Za-z]+)
# Group 2: (\d+)
# Group 3: ([a-z]+)

# Group 0: ([A-Za-z]+)\shas\s(\d+)\s([a-z]+)
```
```
[('Anna', '2', 'dogs'), ('Mark', '2', 'cats'), ('George', '101', 'fish')]
```

The result is a list of tuples which can easily be converted to other data structures if needed.

This is not the only advantage of groups. As it was previously mentioned, quantifiers apply to the character on their immediate left. By capturing a group we can apply a quantifier to a bigger part of regex. It is important though to know the difference between **capturing a repeated group** and **repeating a capturing group**.

```python
numbers = "First number is 909. Second number is 1255."

re.findall(r"(\d+)", numbers)
re.findall(r"(\d)+", numbers)
```
```
['909', '1255']
['9', '5']
```

The second result might be somewhat surprising and not intuitive. More details about what happens there and why can be found here: [Understanding the (\D\d)+ Regex pattern in Python](https://stackoverflow.com/questions/49079552/understanding-the-d-d-regex-pattern-in-python)


## Alternation & Non-Capturing Groups

Round brackets can also be used to group optional characters. Suppose we have a list of URLs as below and we want to find only those that end with .org or .com:

```python
urls = "https://www.wikipedia.org/, https://www.google.com/, " \
       "https://www.regular-expressions.info/, https://pumpkin27.github.io/"

re.findall(r"www\.([a-zA-Z0-9]+)\.(org|com)", urls)
```
```
[('wikipedia', 'org'), ('google', 'com')]
```

Round brackets were used to group URLs endings we are interested in. Now, if we just want the middle part of a URL captured, we can add **<span style="font-family:Courier; color:black">?:</span>** inside the brackets just before the regex, to tell the regex engine to match the group, but not capture it.

```python
re.findall(r"www\.([a-zA-Z0-9]+)\.(?:org|com)", urls)
```
```
['wikipedia', 'google']
```

## Named Groups

As it was mentioned before, when groups are captured they receive their numbers starting from 1 and the whole regex gets number 0. We can use this to retrieve information about a specific group, as in the example below:

```python
urls = "https://www.wikipedia.org/, https://www.google.com/, " \
       "https://www.regular-expressions.info/, https://pumpkin27.github.io/"

result = re.search(r"www\.([a-zA-Z0-9]+)\.(org|com)", urls)

result.group(0)
result.group(1)
result.group(2)
```
```
'www.wikipedia.org'
'wikipedia'
'org'
```

This method works only with **<span style="font-family:Courier; color:black">search()</span>** and **<span style="font-family:Courier; color:black">match()</span>** methods. Instead of using numbers, we can give names to the capturing groups. The syntax for naming groups is **<span style="font-family:Courier; color:black">(?P\<name\>regex)</span>**.

```python
result = re.search(r"www\.([a-zA-Z0-9]+)\.(?P<top_level_domain>org|com)", urls)

result.group(2)
result.group('top_level_domain')
```
```
'org'
'org'
```

## Backreferences

Backreferencing is using a numbered capturing group to match the same text again. For example, we can search for repeated words in a text:

```python
text = "I am so so happy writing this regex paper. " \
       "I am serious, it makes me very very very happy."

re.findall(r"(\w+)\s\1", text)
```
```
['so', 'very']
```

In the regex pattern we match the group **(\w+)** which gets assigned number 1, then a whitespace and then we indicate that we want group number 1 captured again. Moreover, we can replace repeated words with only one occurence of it.

```python
re.sub(r"(\w+)\s\1", r"\1", text)
```
```
'I am so happy writing this regex paper. I am serious, it makes me very very happy.'
```

Notice that this worked nicely with the word _so_ but there are still two occurences of _very_ (we got rid of only one of them). Let's modify our regex so that it finds one or more repetition of the capturing group:

```
re.findall(r"(\w+)(?:\s\1)+", text)

re.sub(r"(\w+)(?:\s\1)+", r"\1", text)
```
```
['so', 'very']
'I am so happy writing this regex paper. I am serious, it makes me very happy.'
```

Problem solved.

Same thing can be done using names of groups. We can for example name our capturing group _repeated_ and then look for the repetition of this group using its name.

```python
re.findall(r"(?P<repeated>\w+)\s(?P=repeated)", text)

re.findall(r"(?P<repeated>\w+)(?:\s(?P=repeated))+", text)
```
```
['so', 'very']
['so', 'very']
```

However in my opinion, while the first of above regex patterns does not look very complicated, the second one appears hard to understand due to nested brackets.

To replace a named capturing group we need a special syntax in the replacement field **<span style="font-family:Courier; color:black">\\g\<name\></span>**.

```python
re.sub(r"(?P<repeated>\w+)\s(?P=repeated)", r"\g<repeated>", text)
```
```
'I am so happy writing this regex paper. I am serious, it makes me very very happy.'
```

## Looking Around

Lookaround groups are a type of non-capturing groups. Looking around is basically searching for what is surrounding a specific pattern. We can look around in four ways: **negative or positive look ahead** and **negative or positive look behind**. They are indicated in regex in following ways:

- positive look-ahead: **<span style="font-family:Courier; color:black">?=pattern</span>**  

- negative look-ahead: **<span style="font-family:Courier; color:black">?!pattern</span>**  

- positive look-behind: **<span style="font-family:Courier; color:black">?<=pattern</span>**  

- negative look-behind: **<span style="font-family:Courier; color:black">?<!pattern</span>**  

Let's consider four examples.


```python
files = "file1.txt: success, file2.txt: fail, " \
        "file1.csv: success, file2.xlsx: success"
```

1) Find all .txt files that were successfully processed.

```python
re.findall(r"\w+.txt(?=:\ssuccess)", files) # positive look-ahead
```
```
['file1.txt']
```

2) Find all .txt files that were not successfully processed.

```python
re.findall(r"\w+.txt(?!:\ssuccess)", files) # negative look-ahead
```
```
['file2.txt']
```

3) Find all processing statuses of .txt files.

```python
re.findall(r"\w+(?<=\.txt):\s(\w+)", files) # positive look-behind
```
```
['success', 'fail']
```

4) Find all processing statuses of files that are not .txt.

```python
re.findall(r"\w+(?<!\.txt):\s(\w+)", files) # negative look-behind
```
```
['success', 'success']
```

Congratulations on reaching the end of this post! Regex can be extremely difficult but they're also a very powerful tool.

_Hope you enjoyed this article! In case of any questions and remarks, please leave a comment._
