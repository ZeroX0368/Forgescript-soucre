# Google Search

{% hint style="info" %}
Google says 100 searches per day is the max, unless you pay.
{% endhint %}

## Variables
Before anything, make these variables!
<figure><img src="/files/26BEJqkbPTvFMke9D43m" alt="" width="563"><figcaption></figcaption></figure>
## Api Key
This code requires an api key, follow these images:
<figure><img src="/files/OKELVzts0QuOGK4pHpBM" alt="" width="563"><figcaption><p>look up "google search api" and click on this result</p></figcaption></figure>
<figure><img src="/files/Dp6DmDfJ4yp8UNpL3MLz" alt="" width="242"><figcaption><p>look on the left hand side and click on "Introduction" under JSON API</p></figcaption></figure>
<figure><img src="/files/SunukrKBXDGNZtNoNJCk" alt="" width="563"><figcaption><p>click "Get a Key" then once loaded, click on "Show Key" and copy and paste into the "googlekey" variable</p></figcaption></figure>
<figure><img src="/files/0GmESD0SQjnpTgwOnrVh" alt="" width="563"><figcaption><p>after, click "Control Panel" highlighted in blue</p></figcaption></figure>
<figure><img src="/files/jmZwTgtQUsJYiVISsp4o" alt="" width="563"><figcaption><p>name it whatever you want and make sure to click on "Search the entire web". Then scroll down and<br>click create. </p></figcaption></figure>
<figure><img src="/files/BllZWjEDx4MW93TgoCuc" alt="" width="563"><figcaption><p>copy your cx and paste it into the "googlcx" variable. now you can copy paste the codes below</p></figcaption></figure>

## (prefix)google

```
$nomention

$c[ # Error: Missing Argument ]
  $if[$message==]
Give me something to search
$stop
  $endif

$c[ # API Request ]
$var[search;$url[encode;$message]]
$httpGet[https://www.googleapis.com/customsearch/v1?key=$getVar[googlekey]&cx=$getVar[googlecx]&q=$var[search]]
$jsonParse[$httpResult]

$c[ # Error: Max Searches For The Day Reached ]
  $if[$httpResult[error]!=]
Max searches have been reached for the day. Try again later.
$stop
  $endif

$c[ # Check Num Of Results Returned ]
$try
$var[results;$jsonArrayCount[items]]
  $catch
$c[ # Error: 0 Results Found ]
No search results found
$stop
$endtry

$c[ # Check Num Of Pages Available ]
$enableDecimals[yes]
$textSplit[$divide[$var[results];3];.]
$var[pgs;$sum[$splitText[1];$if[$splitText[2]!=]1$else0$endif]]

$c[ # If Num Of Pages Available > 1, Add Buttons ]
  $if[$var[pgs]>1]
$addButton[no;gfirst-$authorID;;secondary;yes;⏪]
$addButton[no;gprev-$authorID;;secondary;yes;◀]
$addButton[no;gnext-$authorID;;secondary;no;▶]
$addButton[no;glast-$authorID;;secondary;no;⏩]
  $endif

$c[ # Store Search Result Info ]
$if[$var[pgs]==1]
$var[amount;$var[results]]
  $else
$var[amount;3]
$endif
$var[#;0]
$var[showresults;]
$eval[$replaceText[$cropText[$repeatMessage[10;$repeatMessage[10;$repeatMessage[10;$repeatMessage[10;0]]]];$var[amount];];0;

%{DOL}%var[r%{DOL}%var[#\]title\;%{DOL}%httpResult[items\;%{DOL}%var[#\]\;title\]\]
%{DOL}%var[r%{DOL}%var[#\]displaylink\;%{DOL}%httpResult[items\;%{DOL}%var[#\]\;displayLink\]\]
%{DOL}%var[r%{DOL}%var[#\]link\;%{DOL}%httpResult[items\;%{DOL}%var[#\]\;link\]\]
%{DOL}%var[r%{DOL}%var[#\]desc\;%{DOL}%httpResult[items\;%{DOL}%var[#\]\;snippet\]\]

%{DOL}%var[showresults\;%{DOL}%var[showresults\]

**%{DOL}%var[r%{DOL}%var[#\]title\]**
**[%{DOL}%var[r%{DOL}%var[#\]displaylink\]\\](%{DOL}%var[r%{DOL}%var[#\]link\])**
%{DOL}%if[%{DOL}%var[r%{DOL}%var[#\]desc\]!=\]-# %{DOL}%var[r%{DOL}%var[#\]desc\]%{DOL}%endif\]

%{DOL}%var[#\;%{DOL}%sum[%{DOL}%var[#\]\;1\]\]]]

$c[ # Show First Results ]
$authorIcon[https://upload.wikimedia.org/wikipedia/commons/thumb/c/c1/Google_%22G%22_logo.svg/800px-Google_%22G%22_logo.svg.png]
$author[Search Results]
$title[$message]
$description[$var[showresults]]
$footer[Page 1/$var[pgs]]
```

## $onInteraction

```
$nomention

$onlyIf[$checkContains[$customID;gfirst;gprev;gnext;glast]==true;]

$textSplit[$customID;-]
$var[b;$splitText[1]]
$var[a;$splitText[2]]

$c[ # Error: Only For Author ]
$onlyIf[$var[a]==$authorID;]

$c[ ############################## ]
$c[ # Check Page Currently On & Total Pages Available ]
$textSplit[$getEmbedData[$channelID;$messageID;1;footer];/]
  $var[pg;$replaceText[$splitText[1];Page ;]]
  $var[pgs;$splitText[2]]

$c[ # Go To First Page ]
  $if[$var[b]==gfirst]
  $c[ # Disable "gfirst" + "gprev" ]
$editButton[gfirst-$var[a];;secondary;yes;⏪]
$editButton[gprev-$var[a];;secondary;yes;◀]
  $c[ # If On Max Page, Enable "gnext" + "glast" ]
    $if[$var[pg]==$var[pgs]]
$editButton[gnext-$var[a];;secondary;no;▶]
$editButton[glast-$var[a];;secondary;no;⏩]
    $endif
$var[pg;1]

$c[ # Go To Previous Page ]
  $elseif[$var[b]==gprev]
$var[pg;$sub[$var[pg];1]]
  $c[ # If Going To First Page, Disable "gfirst" + "gprev" ]
    $if[$var[pg]==1]
$editButton[gfirst-$var[a];;secondary;yes;⏪]
$editButton[gprev-$var[a];;secondary;yes;◀]
    $endif
  $c[ # Enable "gnext" + "glast" When Out Of Max Page ]
$editButton[gnext-$var[a];;secondary;no;▶]
$editButton[glast-$var[a];;secondary;no;⏩]

$c[ # Go To Next Page ]
  $elseif[$var[b]==gnext]
$var[pg;$sum[$var[pg];1]]
  $c[ # If Going To Max Page, Disable "gnext" + "glast" ]
    $if[$var[pg]==$var[pgs]]
$editButton[gnext-$var[a];;secondary;yes;▶]
$editButton[glast-$var[a];;secondary;yes;⏩]
    $endif
  $c[ # Enable "gfirst" + "gprev" When Out of First Page ]
$editButton[gfirst-$var[a];;secondary;no;⏪]
$editButton[gprev-$var[a];;secondary;no;◀]

$c[ # Go To Last Page ]
  $elseif[$var[b]==glast]
$c[ # Disable "gnext" + "glast" ]
$editButton[gnext-$var[a];;secondary;yes;▶]
$editButton[glast-$var[a];;secondary;yes;⏩]
  $c[ # Enable "gfirst" + "gprev" If On First Page ]
  $if[$var[pg]==1]
$editButton[gfirst-$var[a];;secondary;no;⏪]
$editButton[gprev-$var[a];;secondary;no;◀]
  $endif
$var[pg;$var[pgs]]
  $endif
$c[ ############################## ]

$c[ # Api Request ]
$var[search;$url[encode;$getEmbedData[$channelID;$messageID;1;title]]]
$httpGet[https://www.googleapis.com/customsearch/v1?key=$getVar[googlekey]&cx=$getVar[googlecx]&q=$var[search]]
  $jsonParse[$httpResult]
$c[ # Error: Max Searches For The Day Reached ]
  $if[$httpResult[error]!=]
$ephemeral
$removeButtons
Max searches have been reached for the day. Try again later.
$stop
  $endif

$var[results;$jsonArrayCount[items]]

$c[ # Store Search Result Info ]
  $if[$var[pg]==1]
$var[#;0] $var[amount;3]
  $elseif[$var[pg]==$var[pgs]]
$var[#;$sub[$var[results];1]] $var[amount;1]
  $elseif[$var[pg]==2]
$var[#;3] $var[amount;3]
  $elseif[$var[pg]==3]
$var[#;6] $var[amount;3]
  $endif
$var[showresults;]
$eval[$replaceText[$cropText[$repeatMessage[10;$repeatMessage[10;$repeatMessage[10;$repeatMessage[10;0]]]];$var[amount];];0;

%{DOL}%var[r%{DOL}%var[#\]title\;%{DOL}%httpResult[items\;%{DOL}%var[#\]\;title\]\]
%{DOL}%var[r%{DOL}%var[#\]displaylink\;%{DOL}%httpResult[items\;%{DOL}%var[#\]\;displayLink\]\]
%{DOL}%var[r%{DOL}%var[#\]link\;%{DOL}%httpResult[items\;%{DOL}%var[#\]\;link\]\]
%{DOL}%var[r%{DOL}%var[#\]desc\;%{DOL}%httpResult[items\;%{DOL}%var[#\]\;snippet\]\]

%{DOL}%var[showresults\;%{DOL}%var[showresults\]

**%{DOL}%var[r%{DOL}%var[#\]title\]**
**[%{DOL}%var[r%{DOL}%var[#\]displaylink\]\\](%{DOL}%var[r%{DOL}%var[#\]link\])**
%{DOL}%if[%{DOL}%var[r%{DOL}%var[#\]desc\]!=\]-# %{DOL}%var[r%{DOL}%var[#\]desc\]%{DOL}%endif\]

%{DOL}%var[#\;%{DOL}%sum[%{DOL}%var[#\]\;1\]\]]]

$c[ # Show "X" Page Results ]
$authorIcon[https://upload.wikimedia.org/wikipedia/commons/thumb/c/c1/Google_%22G%22_logo.svg/800px-Google_%22G%22_logo.svg.png]
$author[Search Results]
$title[$getEmbedData[$channelID;$messageID;1;title]]
$description[$var[showresults]]
$footer[Page $var[pg]/$var[pgs]]
```
