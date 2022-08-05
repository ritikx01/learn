## Regular expressions
A regular expression (or the acronym "regex") is a pattern that allows for complex text searches.

Different regex engines have a slight bit of variations in syntax, but are mostly the same.
Here I will talk about regular expressions supported by the Python Regex engine, but it should 
work with most of the other engines (Perl, PHP, Javascript) etc.

### Make regex work in python
In python, we need to import an inbuilt library called `re`. This module has many functions, but we will
only use `findall()` function.
According to Python docs, findall function returns all non-overlapping matches of pattern in string.
It takes three arguments, pattern, string and flags. Now let's look at some examples.

```python3
import re

pattern = r'cat'
string = "cat catcat tacat"

match = re.findall(pattern, string)
print(match)
```
Output: `['cat', 'cat', 'cat', 'cat']`

Here, python matches all the occurance of what we specified in-between `re'<pattern-here>'`
Apart from literal strings like cat, there are special reserved character combinations or tokens, that 
are used for matching whole strings.

### Regex Tokens
1.\[\] : Character classes are used to specify range.  
Examples:
|Pattern 	  |Matches							    |Not Matches						                              |
|-------------|-------------------------------------|-----------------------------------------------------------------|
|\[a-z] 	  |a, b, c, d ..... z					|4, 2, A, Z, .						                              |
|\[A-Z]		  |A, B, C, D ..... Z					|4, 2, a, z, .						                              |
|\[0-9] 	  |0, 1, 2, 3, 4 ..... 9				|A, Z, a, z, .						                              |
|\[a-zA-Z0-9] |a,b,c .... z, A, B, C .... Z, 0, 1	|., \", %							                              |
|\[abc]		  |a,b,c								|d, e, D, E		# Any one of these 	                              |
|\[^e-h]	  |a, ..  d, i ... z.					|# Any character not in the range of e-h, also matches numbers etc|
|\[^abc]	  |d, e, A, Z, 0, 9						|a, b, c 							                              |

All these match only single characters, meaning in 'cat' [a-z] will maych c,a,t seperately.
Moreover there are meta sequences
	Pattern		Matches					Not Matches
	\w		Any word character [0-9a-zA-Z_]		New line, space, tab etc.
	\W		Anything except word character				
	\s		matches any whitespace character		
	\S		Any non whitespace character 
	\d		Any digit
	\D		Any non digit  

2. { } : Quantifiers
Examples:
|Pattern	|Matches 																	 |
|-----------|----------------------------------------------------------------------------|
|\[a-z]{3}	|cat, bat, hat 		# Any string that has characters a-z exactly 3 times.	 |
|\[a-z]{3-5}	|cat, take, apple	# Any string that has characters a-z 3,4 or 5 times. |
|\[a-z]{3,}	|cat, apple, infinity	# Any string that has characters a-z 3 or more times.|
|\[0-9]{10}	|Phone numbers		# Any string that has 10 digits							 |

**Note: This will only output the matched part, say string is `abcd01234567890abcd` then [0-9]{10} will match only the 
digit part of the string and not the whole string. Output: 0123456789**

More Quantifiers:  
	1. * : Matches previous tokens 0 or more times  
	2. + : Matches previous tokens 1 or more times  
	3. ? : Matches previous token 0 or 1 times  
|Pattern|	Matches  																 |
|-------|----------------------------------------------------------------------------|
|\[a-z]*	|a, aa, asdasd, aksdfl	# Matches anything in range a-z, even matches nothing| 
|\[a-z]+	|Same as above but not matches nothing or null  							 |
|\[a-z]?	|Any character in the range once, or noting / null  						 |

3. Anchors:  
	1. ^ : Matches the next pattern only if it is at the start of the line  
	2. $ : Matches the previous pattern only if it at the end of the line  
	3. \b: Matches word boundary  
	4. \B: Matches non-word boundary  
Example:  
	string = "An apple a day keeps the doctor away.  

|Pattern	 |	Matches 																								  |
|------------|------------------------------------------------------------------------------------------------------------|	
|away		 |away			# String literal match  																	  |
|^away		 |Nothing			# away is not the start of the line                                                       |
|an		     |Nothing			# Regex matching is case sensitive, unless specified  									  |
|An$		 |Nothing			# An is not at the end of the line  													  |
|^An		 |An			  																							  |
|\[a-zA-z]{2} |An, ap, pl ... ay	# Match anything that is in the range of a-z and A-Z exactly two times without overlapping|  
|^\[a-zA-Z]{2}|An			# Same as above but it should be at the start of the line  									  |
|\[a-zA-Z]{2}$|ay			# Same as above but it should be at the end of the line  									  |

As in the case of points 3 and 4, Word boundary is a position between \w and \W or at the beginning or end of a string
if it begins or ends with a word character  
Example:  
	string = "colour color lord flavour flavor humour humor word adore"  
	Some words in the above string as spelled in british and american system. To find the words that are in the   
	american system, we create the below regex  
|Pattern 			 |Matches  																																												  |
|--------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|	
|\[a-z]{0,}or\b 		 |color, flavor, humor	# We are selecting words that have 'or' at the end. \b simply indicates that the 'word bounday' is present at the last position of the regex pattern              |  
|\[a-z]{0,}or\B       |lor, wor, ador		# We have specified non-word boundary just after the 'or' so, any word that has characters after 'or' will be selected, then the word will be selected again upto 'or'|
|\[a-z]{0,}or\B[a-z]+ |lord, word, adore	  																																									  |

4. More :  
	1. | : OR operator. (a|b) Either a or b  
	2. Groups:  
		1. \[a-z](?:[0-9]): Matches 'k9' in string 'k9'  
		2. \[a-z]([0-9]):   Matches 9 in string 'k9'  
	   If we greate a group by enclosing a pattern in braces, the regex will only output what is inside the braces. (Capturing group)  
	   To output the matching of whole regex, we need to add ?: inside the braces to make it a non-capturing group.  
