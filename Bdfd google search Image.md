# Google Image Search

## Variables & API Key

Code requires variables and google API key & cx. Click [here](https://bdfdguy.gitbook.io/docs/codes/google-search) to see step by step.\
\
I recommend making another google key and cx if you plan to use both search and image search.

{% hint style="info" %}
If you would like "safe search" enabled, add "\&safe=active" at the end of the link of $httpGet
{% endhint %}

## (prefix)image

```
$nomention

  $c[ # Error: Missing Argument ]
  $if[$message==]
Give something to search.
$stop
  $endif

$c[ # API Request ]
$var[search;$url[encode;$message]]
$httpGet[https://www.googleapis.com/customsearch/v1?key=$getVar[googlekey]&cx=$getVar[googlecx]&q=$var[search]&searchType=image]
$jsonParse[$httpResult]

  $c[ # Error: Max Daily Searches Reached ]
  $if[$httpResult[error]!=]
Max daily searches has been reached. Try again later.
$stop
  $endif

$c[ # Check Num Of Results Found ]
  $try
$var[results;$jsonArrayCount[items]]
  $c[ # Error: 0 Results Found ]
  $catch
**0** results found.
$stop
  $endtry

$c[ # Store First Image Result Info ]
$var[title;$httpResult[items;0;title]]
$var[displaylink;$httpResult[items;0;displayLink]]
$var[img;$httpResult[items;0;link]]

$c[ # Show First Image Result ]
$authorIcon[https://upload.wikimedia.org/wikipedia/commons/thumb/c/c1/Google_%22G%22_logo.svg/800px-Google_%22G%22_logo.svg.png]
$author[Image Search Results]
$title[$message]
$description[**$var[title]**
**[$var[displaylink]\]($var[img])**]
$image[$var[img]]
$footer[Page 1/$var[results]]

  $c[ # If > 1 Results Found, Add Buttons ]
  $if[$var[results]>1]
$addButton[no;ifirst-$authorID;;secondary;yes;⏪]
$addButton[no;iprev-$authorID;;secondary;yes;◀️]
$addButton[no;inext-$authorID;;secondary;no;▶️]
$addButton[no;ilast-$authorID;;secondary;no;⏩]
  $endif
```

## $onInteraction

```
$nomention
$onlyIf[$checkContains[$customID;ifirst;iprev;inext;ilast]==true;]
$defer
$textSplit[$customID;-]
  $var[button;$splitText[1]]
  $var[author;$splitText[2]]

  $c[ # Error: Only For Author ]
  $if[$authorID!=$var[author]]
$ephemeral $removeButtons
These are not your buttons.
$stop
  $endif

$c[ # API Request ]
$var[search;$url[encode;$getEmbedData[$channelID;$messageID;1;title]]]
$httpGet[https://www.googleapis.com/customsearch/v1?key=$getVar[googlekey]&cx=$getVar[googlecx]&q=$var[search]&searchType=image]
$jsonParse[$httpResult]

  $c[ # Error: Max Daily Searches Reached ]
  $if[$httpResult[error]!=]
$ephemeral $removeButtons
Max daily searches has been reached. Try again later.
$stop
  $endif

$c[ # Check Page On & Total Pages ]
$textSplit[$getEmbedData[$channelID;$messageID;1;footer];/]
  $var[pg;$replaceText[$splitText[1];Page ;]]
  $var[pgs;$splitText[2]]

$c[ ###################################### ]
$c[ # Go To First Page ]
  $if[$var[button]==ifirst]
  $c[ # Disable "First" + "Previous" Buttons ]
$editButton[ifirst-$var[author];;secondary;yes;⏪]
$editButton[iprev-$var[author];;secondary;yes;◀]
  $c[ # If On Max Page, Enable "Next" + "Last" Buttons ]
  $if[$var[pg]==$var[pgs]]
$editButton[inext-$var[author];;secondary;no;▶]
$editButton[ilast-$var[author];;secondary;no;⏩]
  $endif
$var[pg;1]

$c[ # Go To Previous Page ]
  $elseif[$var[button]==iprev]
$var[pg;$sub[$var[pg];1]]
  $c[ # If Going To First Page, Disable "First" + "Previous" Buttons ]
  $if[$var[pg]==1]
$editButton[ifirst-$var[author];;secondary;yes;⏪]
$editButton[iprev-$var[author];;secondary;yes;◀]
  $endif
  $c[ # Enable "Next" + "Last" Buttons When Out Of Max Page ]
$editButton[inext-$var[author];;secondary;no;▶]
$editButton[ilast-$var[author];;secondary;no;⏩]

$c[ # Go To Next Page ]
  $elseif[$var[button]==inext]
$var[pg;$sum[$var[pg];1]]
  $c[ # If Going To Max Page, Disable "Next" + "Last" Buttons ]
  $if[$var[pg]==$var[pgs]]
$editButton[inext-$var[author];;secondary;yes;▶]
$editButton[ilast-$var[author];;secondary;yes;⏩]
  $endif
  $c[ # Enable "First" + "Previous" Buttons When Out of First Page ]
$editButton[ifirst-$var[author];;secondary;no;⏪]
$editButton[iprev-$var[author];;secondary;no;◀]

$c[ # Go To Last Page ]
  $elseif[$var[button]==ilast]
  $c[ # Disable "Next" + "Last" Buttons ]
$editButton[inext-$var[author];;secondary;yes;▶]
$editButton[ilast-$var[author];;secondary;yes;⏩]
  $c[ # Enable "First" + "Previous" Buttons If On First Page ]
  $if[$var[pg]==1]
$editButton[ifirst-$var[author];;secondary;no;⏪]
$editButton[iprev-$var[author];;secondary;no;◀]
  $endif
$var[pg;$var[pgs]]
  $endif

$c[ ###################################### ]

$c[ # Store "X" Image Result Info ]
$var[title;$httpResult[items;$sub[$var[pg];1];title]]
$var[displaylink;$httpResult[items;$sub[$var[pg];1];displayLink]]
$var[img;$httpResult[items;$sub[$var[pg];1];link]]

$c[ # Show "X" Image Result ]
$authorIcon[https://upload.wikimedia.org/wikipedia/commons/thumb/c/c1/Google_%22G%22_logo.svg/800px-Google_%22G%22_logo.svg.png]
$author[Image Search Results]
$title[$getEmbedData[$channelID;$messageID;1;title]]
$description[**$var[title]**
**[$var[displaylink]\]($var[img])**]
$image[$var[img]]
$footer[Page $var[pg]/$var[pgs]]
```
