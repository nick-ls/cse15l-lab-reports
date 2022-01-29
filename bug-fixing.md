<style>pre{white-space:pre-wrap;} h1 code{font-size: 0.9em; padding: 5px;} code{padding: 3px;}</style>

# Fixing Markdown Parser Bugs
Over the course of the last 2 weeks and the upcoming weeks, we're going to be building and improving a program that can parse the links in a markdown file and extract their contents. I don't know about you, but to me, that sounds like the perfect job for some regular expressions (RegEx if you will). [Unlike HTML](https://stackoverflow.com/a/1732454), markdown files seem to lack sufficient complexity enough to warrant parsing by regular expressions without summoning an eldritch horror.
## First bug fix
![First code diff](/img/lab4/diff1.png)

Failing Inducing Input: [`empty-link.md`](https://github.com/Nicholas264/markdown-parse/blob/main/empty-link.md) [`(raw)`](https://raw.githubusercontent.com/Nicholas264/markdown-parse/main/empty-link.md)

Symptoms: 

![First symptom](/img/lab4/symptom1.png)

The regular expression was testing for any length of non-closing-parenthesis characters, but since a link is only valid if it has a destination of length 1 or longer as denoted by the [CommonMark Spec](https://spec.commonmark.org/0.30/#links), this file shouldn't contain a valid link. The fact that this is the problem is evidenced by the presence of the comma in the output array which signifies that there are two elements. This means that empty strings were added to the array when they shouldn't have been. This is fixed by requiring that there be 1 or more characters present in a link destination for the link to be added. Upon running the program again, the issue is fixed.

## Second bug fix
![Second code diff](/img/lab4/diff2.png)

Failure Inducing Input: [`image-file.md`](https://github.com/Nicholas264/markdown-parse/blob/main/image-file.md) [`(raw)`](https://raw.githubusercontent.com/Nicholas264/markdown-parse/main/image-file.md)

Symptoms:

![Second symptom](/img/lab4/symptom2.png)

The bug was that the regular expression wasn't checking if there was an exclamation point in front of the link, which would have signified an image instead. The symptom of this was the image's description and destination were retrieved by the function when they shouldn't have, as the input file doesn't contain any links. In the output, it clearly shows that the array is not empty;  it has the description `Image` and the url `ucsd.png` inside it, which is against the expected output. The solution to this was to make sure that there was any character but an exclamation point before the starting bracket so that images wouldn't get interpreted as links.

## Third bug fix
![Third code diff](/img/lab4/diff3.png)

Failure Inducing Input: [`hell.md`](https://github.com/Nicholas264/markdown-parse/blob/main/hell.md) [`(raw)`](https://raw.githubusercontent.com/Nicholas264/markdown-parse/main/hell.md)

Symptoms: 

![Third symptom](/img/lab4/symptom3.png)

This time the bug was that links within code blocks were getting recognized as being links where the markdown specification says that they shouldn't be. This is evident from there being many more links present in the command output than there should be. The only link that should be included is the one with the title `link__**` and url `sdf`. The reason this fails is because the code didn't take into account whether it was within backticks or inside codeblocks (both of which links can't appear in). This is fixed (rather poorly) using a regex solution that removes all backticks from largest to smallest in sequence so that only the links outside codeblocks are included in the final output.

# Conclusion
I was wrong. Don't try to write your own script to parsing markdown; use a library or something. There are too many variations on the ways you can make links and the order of precedence of certain features of markdown. In order to parse all links, you will essentially have to build an entire markdown parser from the ground up, since you have to take into account bullet points, tables, indentation, code blocks, stylings, HTML tags, and anything else I missed. The point is: markdown is *irregular*, therefore, one should not attempt to parse it using *regular* expressions unless they want to end up getting consumed by your own demonic code.