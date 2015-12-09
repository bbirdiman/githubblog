# a far too wordy<br>orientation to z.m.l.


 by bowerbird intelligentleman

 i am doing another change to test the update checker,  
 which seems to be working quite nicely, thank you.

 you can "view source" for this document here:  
 http://zenmagiclove.com/zml/suite/suite.zml

 hi chris. if you have any questions, let me know.  
 you'll find the .mobi in the .mobi directory.  
 the .epub is in the main directory, because  
 the only files allowed in the .epub directory  
 are the files which actually go in the .epub.

 http://zenmagiclove.com/zml/suite/suite.jpg

![](http://zenmagiclove.com/zml/suite/suite.jpg)



## table of contents


 [a far too wordy orientation to z.m.l.](https://github.com/bbirdiman/githubblog/blob/master/githubblog2.md#a-far-too-wordyorientation-to-zml)  
 [table of contents](https://github.com/bbirdiman/githubblog/blob/master/githubblog2.md#table-of-contents)  
 [brief preface to this wordy orientation](https://github.com/bbirdiman/githubblog/blob/master/githubblog2.md#brief-preface-to-this-wordy-orientation)    
 [chapter 1 -- the "chunk" is the basic unit](https://github.com/bbirdiman/githubblog/blob/master/githubblog2.md#chapter-1the-chunk-is-the-basic-unit)    
 [chapter 2 -- chunks are stored in gourds](https://github.com/bbirdiman/githubblog/blob/master/githubblog2.md#chapter-2chunks-are-stored-in-gourds)    
 [chapter 3 -- chunks come in two flavors](https://github.com/bbirdiman/githubblog/blob/master/githubblog2.md#chapter-3chunks-come-in-two-flavors)    
 [chapter 4 -- styling your fancy words](https://github.com/bbirdiman/githubblog/blob/master/githubblog2.md#chapter-4styling-your-fancy-words)    
 [chapter 5 -- images in your book](https://github.com/bbirdiman/githubblog/blob/master/githubblog2.md#chapter-5images-in-your-book)   
 [chapter 6 -- creating your links](https://github.com/bbirdiman/githubblog/blob/master/githubblog2.md#chapter-6creating-your-links)  
 [chapter 7 -- using block-quotes](https://github.com/bbirdiman/githubblog/blob/master/githubblog2.md#chapter-7using-block-quotes)  
 [chapter 8 -- footnotes and endnotes](https://github.com/bbirdiman/githubblog/blob/master/githubblog2.md#chapter-8footnotes-and-endnotes)  
 [chapter 9 -- different types of lists](https://github.com/bbirdiman/githubblog/blob/master/githubblog2.md#chapter-9different-types-of-lists)  
 [chapter 10 -- the play is the thing](https://github.com/bbirdiman/githubblog/blob/master/githubblog2.md#chapter-10the-play-is-the-thing)  
 [chapter 11 -- how to lay out tables](https://github.com/bbirdiman/githubblog/blob/master/githubblog2.md#chapter-11how-to-lay-out-tables)  
 [chapter 12 -- justify poetry silly-willy](https://github.com/bbirdiman/githubblog/blob/master/githubblog2.md#chapter-12justify-poetry-silly-willy)  
 [chapter 13 -- unlucky number 13](https://github.com/bbirdiman/githubblog/blob/master/githubblog2.md#chapter-13unlucky-number-13)  
 [chapter 14 -- epigraphs and epitaphs](https://github.com/bbirdiman/githubblog/blob/master/githubblog2.md#chapter-14epigraphs-and-epitaphs)  
 [chapter 15 -- preformatted text](https://github.com/bbirdiman/githubblog/blob/master/githubblog2.md#chapter-15preformatted-text)  
 [chapter 16 -- hyphens and dashes](https://github.com/bbirdiman/githubblog/blob/master/githubblog2.md#chapter-16hyphens-and-dashes)  
 [chapter 17 -- hyphenation stinks](https://github.com/bbirdiman/githubblog/blob/master/githubblog2.md#chapter-17hyphenation)  
 [chapter 18 -- spaces after a sentence](https://github.com/bbirdiman/githubblog/blob/master/githubblog2.md#chapter-18spaces-after-a-sentence)  
 [chapter 19 -- the end-notes section](https://github.com/bbirdiman/githubblog/blob/master/githubblog2.md#chapter-19the-end-notes)  
 [chapter 20 -- meta-data for this book](https://github.com/bbirdiman/githubblog/blob/master/githubblog2.md#chapter-20meta-data-for-this-book)  




## brief preface to<br>this wordy orientation


this document gives you an orientation on
"z.m.l.", short for "zen markup language".

z.m.l. is a light-markup format, similar to
restructured-text, textile, asciidoc, and the
one most-widely-used currently, markdown.

z.m.l. was designed explicitly for "long-form"
documents, such as books, journal articles,
user-manuals, and similar types of material.

perhaps the best defining characteristic of
"long-form" documents is that they typically
consist of several sections, and thus require
a table of contents. if your document is one
that would benefit from a table of contents,
you'll probably find it wise to use z.m.l. for it,
because z.m.l. has the features you will need.




## chapter 1<br><br>the "chunk" is the basic unit


"zen markup language" -- z.m.l. for short --
is a system of "light-markup". as such, it is
a means by which the "structural elements"
of a long-form document can be indicated,
so that each element is treated "correctly",
whatever that means in various situations.

many structural elements of long-form texts
are well-known to people, and certainly the
most familiar one is the humble "paragraph",
a group of sentences related to each other,
gathered together in support of their topic.

a second common element is the "header",
such as a chapter-title, big and *bold* and
centered, and often starting on a new page.

indeed, there are many books which consist
of little more than headers and paragraphs,
once you pass a smattering of front-matter.

in order for a system to know how to treat
a header or a paragraph "correctly", it must
first know what that specific element _is_,
so we need to a way to inform the system
"this is a header" and "this is a paragraph".

z.m.l. -- zen markup language -- analyzes
a text-file to prepare it for published output
by first splitting it into its "sections", which
would be chapters, for instance, in a novel.

then each section is split into "paragraphs",
to use this term that we're all familiar with.

but there are several different kinds of
basic underlying structural elements that
can be grouped under a "paragraphs" label,
as we'll see, so z.m.l. calls them "chunks",
to have an unambiguous descriptive term
for the basic building-block of long-form.

"chunk" is thus the word we use generically,
to refer to every single element in our text.

"chunk" is one of the few cases of jargon
that we will use, but we'll use it a lot, so
you will need to feel comfortable with it.

the thing you must know about chunks is
that a "chunk" is defined as any non-blank
line(s) with a blank line above and below.

_every_ _word_ in your book is in a chunk,
separated from its neighboring chunks by
a blank line, and all chunks are numbered,
from the first one in the file -- the title --
to the last, holding the last word in the file.

each header is a chunk, and each paragraph,
and every type of element in the entire book.

we use a blank line to separate the chunks.

ergo, use a blank line between paragraphs.

while you're writing, put a blank line above
each paragraph, and a blank line below it.
(yes, the blank line "below" one paragraph
doubles as the blank line "above" the next.)

that might be something you already do,
putting a blank-line between paragraphs,
but if not, a global find-and-replace to
turn one return into two will do the trick.

also, if you're helping, or considering it,
do _not_ indent the first line of paragraphs
with spaces. and especially not with tabs.
(it is probably best not to use tabs at all.)

a first-line indent option is something that
gets handled by the system automatically,
not something to fret about when writing.

 ***

there are "special" types of paragraphs,
which we will need to differentiate later,
and other elements besides paragraphs,
which we'll discuss then too, but for now,
remember that "chunk" is our basic unit.

it's easy to understand, easy to recognize.

(it's also extremely easy to write code for.
as a quick side-bar for any programmers,
you will separate a z.m.l. file into chunks
by simply splitting the text on blank lines;
that is, you split on a pair of line-breaks.
now we return to regular-person readers.)




## chapter 2<br><br>chunks are stored in gourds


in a book, paragraphs join into "chapters".

and in a magazine, paragraphs compose
"articles", which are grouped in "sections".

or -- depending on how you look at it --
"sections" will be combined into "articles".

both "chapters" and "articles" are examples
of the types of "sections" we called _the_
defining characteristic of long-form writing.

but "section" is an ambiguous term as well.

for instance, in a novel, it will be chapters,
yes, but it'll also be the label for the index
in the back, as well as the preface up front.

looking at some cookbook, it might refer to
each recipe. alternatively, it can also mean
the recipes collected in a "desserts section".

obviously, this could get to be confusing;
thus we need an unambiguous word for it.

so we use the term "gourd" as our generic,
broad equivalent for "chapter" or "section".

every separate section of a long-form text
is called a "gourd". this includes things like
the cover/title-page, the table of contents,
the dedication, the footnotes, and all that.

so a novel might consist of many gourds,
most (but not all) of which are chapters.

and each gourd is comprised of chunks,
most (not all) of them being paragraphs.

so those are the two bits of jargon we use.

 ***

there are a couple things you have to know
about gourds, and how you'll "mark" them.

the _first_ thing to know about a gourd is
it must have at least 5 blank lines above it.

this is how you tell z.m.l. that the chunks
that follow are to be considered a gourd.

do not put 5 blank lines anywhere else,
because z.m.l. will consider that to be
a marker that you started a new gourd.

while there must be at least 5 blank lines,
more than 5 is also fine, so please do not
feel you must spend time counting lines.
just repeatedly press "return" many times.

(i find, however, it is innocuous to count,
out loud, and it's even strangely exciting,
knowing you are starting a new chapter.)

 ***

the next thing you need to know is that
the first chunk in a gourd is the _header_
for that gourd; if you are writing a novel,
a gourd's first chunk is the chapter title.

to repeat, the sequence of 5+ empty lines
says "i'm starting a new section here", and
the first non-empty line following it says,
"i am the beginning of the header here."

a gourd can have a header with two parts:
for instance, one part might be "chapter 1",
while the second part is "in the beginning".

in such a case, you would put a blank line
between the parts, since you want them
to be recognized as independent chunks.

to indicate that the header is all finished
-- and the body of the gourd will follow --
type in _2_ blank lines below its last line.

 ***

the first gourd in the book is the cover/title.
(in an e-book, the "cover" and "title page"
are one and the same, unless you want to
consider the icon of the file as its "cover".)

the book's title comes first, the initial chunk,
followed immediately by subtitle(s), if any.

next comes the chunk naming the author.
this chunk must begin with the word "by",
the signal z.m.l. uses to discern the author.

after that, you'll include anything you consider
to be of sufficient importance that it "belongs"
on the cover/title; things like publication date,
the web-page of the "view source" z.m.l. file,
e-mail address for the author, and of course
the u.r.l. for the image that's the book's cover.

the second gourd is the table of contents.
this is required, so readers know where it is,
and know that it's in a very convenient place,
helping create a shared understanding of this.

a table of contents will be auto-created and
injected as the second gourd of your book
if you don't already have a table of contents
as your second gourd. the table of contents
in an e-book should always have every item
linked to the relevant place in the book, so
z.m.l. makes such links for you automatically.

 ***

the next gourds are "front-matter" you have,
like a preface, introduction, prologue, etc.

though some front-matter pages, like the
dedication, do not usually have a header in
traditional printed-books, you will need to
provide a header -- such as "dedication" --
so z.m.l.'s automatic links can be created.

also, it's important in an e-book, which has
no "heft" to help inform a reader that they
are at the "front" or "back" of the e-book,
to provide some extra navigational hints to
help the reader feel "oriented", and a header
for each and every gourd serves that purpose.

 ***

and after the front-matter gourds, of course,
you will begin on the regular chapter gourds.
and after them, you'll have the "back-matter",
things like the index, footnotes, references,
colophon, note on other books by the author,
"thanks for reading", and all of that claptrap.

 ***

i shouldn't need to tell you programmers, in
a quick side-bar to this chapter, that you can
divide a z.m.l. file into an array of its gourds
by simply splitting on a set of 6 line-breaks,
because you'll have already figured that out.
but let me point out that you could also just
use the array of chunks that you split earlier,
because every sequence of 2 empty chunks
represents the start of the next gourd. nifty!
because, you see, z.m.l. has been specially
designed, by a programmer, to be nice for
programmers, in addition to regular people.




## chapter 3<br><br>chunks come in two flavors


now we get to the actual markup instructions.

you know that "chunks" are the basic unit of
the file, any contiguous non-blank line(s) that
are separated by blank lines above and below.

so a "plain old paragraph" -- like this one --
is a chunk, and a chunk is easy to recognize.

the trick is for z.m.l. to know what kind of
"structural element" each chunk represents;
i.e., what the chunk "is", in terms of the book.

sometimes, z.m.l. can ascertain it fairly easily.

for instance, this paragraph right here is just
another "plain old paragraph", and z.m.l. can
determine that without breaking any sweat.

if the text of a chunk starts at the left margin,
with a letter, z.m.l. says "that is a paragraph."

simple enough.

 ***

and if the text of a chunk starts with a number,
z.m.l. considers it as a "numbered paragraph".
which means it will be formatted specially, with
its body indented from the left, while its number
remains at the margin isolated as an "outdent".

so if that's the look you want, you know that
you must start your paragraph with a number.

(of course, that's what you were going to do
anyway, because that's the normal thing to do,
which is why light-markup is so simple, since
you just do what you were going to do anyway.)

 ***

if the text of a chunk starts with a left-bracket
z.m.l. determines that signal to be a footnote
-- or end-note, but let's not split hairs yet --
which means it too will get special formatting,
when the time comes to create output formats,
in a manner that you're familiar with, if you've
ever looked at the end-note section of a book.
(it's similar to the numbered paragraph, actually,
with the number of the end-note as an outdent.)

however, there's more to it than that, because
in addition to special formatting at output time,
z.m.l. also knows that a footnote/endnote is a
special type of element referring to some other
place in the document, a place with a matching
number (or name), likewise enclosed in brackets.

so z.m.l. sets out to find that matching location,
and when it does, automatically creates mutual
cross-links that join the two love-birds together.

you have to make sure, of course, that there are
matching numbers/names, but that's easy to do,
and once you've done your simple part correctly,
z.m.l. goes out and re-does all the grunge work
every time you ask it to generate output for you.

 ***

there are other types of paragraphs, as well, but
the point is that the first major flavor of chunk is
the "paragraph", usually the "plain old paragraph".
it's the meat-and-potatoes of structural elements.

the other major chunk flavor -- the spicy one --
we will call the "block". it's probably not the best
name, especially as it's already quite overloaded,
but it should work well enough for our purposes.

paragraphs are boring, but blocks are exciting.

if you know .html, most of the "tags" you know
(and love so much, i'm sure) are "blocks" in z.m.l.

they include block-quotes (of course), tables,
lists (both unordered and ordered) and so on.

 ***

z.m.l. has one simple way it recognizes a block:

whenever a chunk starts with a space, it's a block.

that is, to put it from the opposite perspective,
every time you want to signal a block to z.m.l.,
you will start the text of the chunk with a space.

there are a variety of different "tags" you will use,
to signal lists and blockquotes and tables and such,
but every one starts with a space in column one.

further, most of them take a specific extended form,
where the second column in the chunk is the "tag",
and the third column is then again another space.

so most block indicators are: space-tag-space.

here's a bunch of them, quickly, to get your feet wet.

space-asterisk-space == tag for an unordered list == " * "

space->-space == tag for a block-quote == " > "

space-|-space == tag for a table == " | "

space-#-space == tag for an ordered list == " # "

space-o-space == unordered list, different bullet == " o "

space-+-space == unordered list, different indent == " + "

space-=-space == unordered list, different bullet == " = "

space-x-space == unordered list, no bullet == " x "

all of those lines above are chunks which are in turn
"plain old paragraphs", since they start with a letter
as their initial character, so that gives a certain look.

but now let's see that same list of tags, as a list:

 * space-asterisk-space == tag for an unordered list  
 * space->-space == tag for a block-quote  
 * space-|-space == tag for a table  
 * space-#-space == tag for an ordered list  
 * space-o-space == unordered list, different bullet  
 * space-+-space == unordered list, different indent  
 * space-=-space == unordered list, different bullet  
 * space-x-space == unordered list, no bullet

once you've learned the tag for a structural element,
it's tremendously easy to use it in your writing, and
it's fast, plus it doesn't gunk up your text, which is
another reason light-markup is so good for writers.

 ***

as i said, most tags use this space-tag-space form.
but there are some exceptions. one of my favorite
is the shortcut that means "please center this line".

it's very simple. you just put a space in column one,
and then start in with your line. works like a charm.

indeed, it's the secret behind those lines you've seen
used right here in this text, consisting of 3 *asterisks,*
the kind that often serve as "scene-breaks" in novels.
if you look at the input-file, you'll see they have a
space in column one, followed by those 3 *asterisks,*
and they are automatically centered in rendered output.

so let's look at that list again, this time centered:

 space-asterisk-space == tag for an unordered list  
 space->-space == tag for a block-quote  
 space-|-space == tag for a table  
 space-#-space == tag for an ordered list  
 space-o-space == unordered list, different bullet  
 space-+-space == unordered list, different indent  
 space-=-space == unordered list, different bullet  
 space-x-space == unordered list, no bullet

well, it ain't gonna win any beauty contents, for sure,
but as far as doing the job, quickly and easily, it works.




## chapter 4<br><br>styling your fancy words


in light-markup systems, the input file is in
plain-text, whereas output can be rendered
in a browser, or an e-book file, or wherever.

so what we'll talk about, as we discuss this,
is what you need to do -- in the input file --
in order to obtain the output that you want.

as our first example, let's consider _italics._

as a plain-text file cannot "do" _italics,_ you'll
need to use regular plain-text characters to
show what you'd like _italicized_ in the output.

z.m.l. uses _underscores_ to _italicize_ words,
the first underscore at the start of the word,
the other one at the end of the word. easy.

this _word_ is _italicized._

and this _phrase_ _will_ _be_ _in_ _italics._

take a look at the input, and you should
note the _underscores_ must go _outside_
punctuation-characters next to the word
_(like_ _parentheses)_ or _"quote-marks"_
or phrase-terminators such as _commas,_
or _sentence-terminators_ like _periods._

starting _underscores_ _must_ have white-space
to their left; likewise, closing ones _must_
have white-space to their right. for a phrase,
each _word_ must have its own _underscores._

now, what you will see depends, of course,
on whether you're viewing input or output.

if you're looking at the input, you'll see
the _underscores,_ and no _italics_ (because,
as we noted, the input file is plain-text).

but if instead you are viewing the output,
you will see _italics,_ and not _underscores._

and this _phrase_ _will_ _be_ _in_ _italics._

it probably seems like extra work to have to
do each word individually, but we begin with
this rigid definition because it is rock-solid,
in the sense that it works dependably, without
producing unexpected or unpleasant effects.
(such as when you have _underscores_ in a u.r.l.)

before long, we'll introduce more flexibility,
and much more power. but this is our start.

*bold* words are indicated with *asterisks,* and
subject to the same rules on white-space.

these *words* will be rendered in *bold.*

and this *phrase* *will* *be* *bold* *too.*

nice.




## chapter 5<br><br>images in your book


to include an image in your book, just enter its u.r.l.

 http://z-m-l.com/go/alice/checking_watch.png
 ![](http://z-m-l.com/go/alice/checking_watch.png)


"what is the
use of a book,"
thought alice,
"without pictures
or conversation?"

 ***

you can put a caption on your image if you like.
list the caption first, then give the image u.r.l.

 here alice is holding a pig. imagine that!  
 http://z-m-l.com/go/alice/alice_holding.png

notice that even though you put the caption first,
it was actually placed below the image, which is
an ass-backwards glitch in the program i will fix.

 ***

    here is the caption for cat fades  
    http://z-m-l.com/go/alice/cat_fades.png
    ![](http://z-m-l.com/go/alice/cat_fades.png)
 ***




## chapter 6<br><br>creating your links


remember how, in chapter 2, we said
that the items in the table of contents
should link to the appropriate spots?
z.m.l. does that for you automatically.

similarly, there are often places in a book
that reference other chapters in the book.
in these cases, it's very nice to have a link
that transports the reader to the chapter
that is being referenced; it is convenient.

so z.m.l. automates those links for you,
too. just use the header (or subheader)
of the gourd, and z.m.l. will auto-link it.

for instance, the beginning of this chapter
has a reference to chapter 2. if a reader
clicked on those words -- "chapter 2" --
they will automatically go to chapter 2.
(and likewise with each of the references
to "chapter 2" here in this paragraph too.)

such internal links are one type needed,
but you also might want _external_ links,
ones that will jump out to some webpage.

to make an external link, just list the u.r.l.

 http://gutenberg.org

z.m.l. will create that link automatically.

a core element of the z.m.l. philosophy
is that a book's elements be transparent,
so there is no way to "hide" a link behind
some text. the u.r.l. must be fully exposed.

however, if the u.r.l. would be too bulky to
include there, you _can_ put it in a footnote.

i get ten times more traffic from google[goop]
than from yahoo[yoop] and m.s.n.[moop] combined.

[goop] http://google.com

[yoop] http://search.yahoo.com

[moop] http://search.msn.com




## chapter 7<br><br>using block-quotes


you can get a blockquote like this.

 : four score and seven years ago, our  
 : forefathers set forth upon this continent  
 : a new nation, conceived in liberty and  
 : dedicated to the proposition that  
 : all men[1] are created equal.

or like this:

 > four score and seven years ago, our  
 > forefathers set forth upon this continent  
 > a new nation, conceived in liberty and  
 > dedicated to the proposition that  
 > all men[2] are created equal.

you should note that the first method
(using the colon) rewraps the lines, and
the second (using the right-angle-bracket)
does not rewrap the lines. so use the one
which meets your needs better in each case.




## chapter 8<br><br>footnotes and endnotes


your book can have footnotes.[3]

for a footnote, place the footnote-referent in
square brackets.[3a]

note that there must be _no_ white-space
to the left of the referent, but that there
_must_ be whitespace to the right of it.

this is just a dummy paragraph,
to get some separation between
the footnote referent up above
and the actual footnote below,
so you can see the jump when
you click between the two of them.

this is another dummy paragraph,
to get some separation between
the footnote referent up above
and the actual footnote below,
so you can see the jump when
you click between the two of them.

this is a third dummy paragraph,
to get some separation between
the footnote referent up above
and the actual footnote below,
so you can see the jump when
you click between the two of them.

and a fourth dummy paragraph,
to get some separation between
the footnote referent up above
and the actual footnote below,
so you can see the jump when
you click between the two of them.

here's dummy paragraph #5,
to get some separation between
the footnote referent up above
and the actual footnote below,
so you can see the jump when
you click between the two of them.

this is a sixth dummy paragraph,
to get some separation between
the footnote referent up above
and the actual footnote below,
so you can see the jump when
you click between the two of them.

and the seventh dummy paragraph,
to get some separation between
the footnote referent up above
and the actual footnote below,
so you can see the jump when
you click between the two of them.

here's dummy paragraph #8,
to get some separation between
the footnote referent up above
and the actual footnote below,
so you can see the jump when
you click between the two of them.

i hope 9 dummy paragraphs is enough
to get some separation between
the footnote referent up above
and the actual footnote below,
so you can see the jump when
you click between the two of them.[4]

[3] you can put the footnote right underneath
the paragraph which contains it, or at the end
of the chapter containing it, or in a gourd at
the end of the book with all the other footnotes.




## chapter 9<br><br>different types of lists


you use the " * " tag at the start of a chunk
to make a list, an "unordered" list that has
the usual circular bullet-point as its marker.

 * one -- the list with *asterisk* tag  
 * two  
 * three  
 * four  
 * five  
 * six  
 * seven  
 * i forget what 8 was for.  
 * number 9, number 9...

as you can see in this example, every item
must be prefaced with the same character.

 ***

and you can use " o " for a list, a tag that
will use an _unfilled_ _bullet_ as its marker.

 o mercury -- the list with "o" tag  
 o venus  
 o earth  
 o mars  
 o jupiter  
 o saturn  
 o uranus  
 o neptune  
 o pluto

 ***

you can also use " = " to create a list;
this tag uses a solid square as its bullet.

 = one -- the list with "=" tag  
 = two  
 = three  
 = four  
 = five  
 = six  
 = seven  
 = i forget what 8 was for.  
 = number 9, number 9...

 ***

then you can use " + " for a list you want
to be indented one level, and which uses
a filled bullet-point as marker-character.

 + one -- the list with "+" tag  
 + two  
 + three  
 + four  
 + five  
 + six  
 + seven  
 + i forget what 8 was for.  
 + number 9, number 9...

remember if you're looking at the input,
you won't see the indented level, and
that when you're looking at the output,
you won't see the "+" signs, but instead
you will see the filled-circle bullet-point.

 ***

lastly, you can use " - " to make a list
that will get indented _two_ levels and
use an unfilled circle as its bullet-point.

 - one -- the list with "-" tag  
 - two  
 - three  
 - four  
 - five  
 - six  
 - seven  
 - i forget what 8 was for.  
 - number 9, number 9...


none of these bullets are set in stone.
they will be set collaboratively by the
zen-user-base, when it gets gathered.

 ***

finally, you can use " x " to make a list.

 x one -- the list with "x" tag  
 x two  
 x three  
 x four  
 x five  
 x six  
 x seven  
 x i forget what 8 was for.  
 x number 9, number 9...

the " x " prefix has _no_ bullet at all.
as we will now see, that has benefits.

 ***

sometimes you want a numbered list.

here's an example of an unordered list
that _masquerades_ as a numbered list,
but is actually a no-bullet unordered list,
with the number specifically included,
thanks to its use of the " x " tag.

 x 1. one  
 x 2. two  
 x 3. three  
 x 4. four  
 x 5. five  
 x 6. six  
 x 7. seven  
 x 8. i still forget what 8 was for.  
 x 9. number 9, number 9...

 ***

here's another fake "numbered list",
again with the bullet shut off, and
then the number specifically included,
but we mix things up a bit, to show
how to do non-sequential numbers.

 x 101. one  
 x 202. two  
 x 3. three  
 x 4444. four  
 x 55555. five  
 x 6. six  
 x 77. seven  
 x 88. i still forget what 8 was for.  
 x 9. number 9, number 9...

that example seems kind of silly, yes,
but there are many meaningful ones.

 x 800bc -- north america gets inhabited  
 x 1492 -- columbus "discovers" america  
 x 1620 -- pilgrams land on plymouth rock  
 x 1622 -- "indians" feed starving pilgrims  
 x 1809 -- white men start "indian" genocide  
 x 1964 -- civil rights act passes congress

 ***

here's another numbered list, except
this time it's a plain .html ordered list,
so the browser does the numbering.

 # one -- the list with "#" tag  
 # two  
 # three  
 # four  
 # five  
 # six  
 # seven  
 # i still forget what 8 was for.  
 # number 9, number 9...




## chapter 10<br><br>the play is the thing


z.m.l. *bolds* the first word of a paragraph
if it is followed with a colon. this is handy
when you are doing the dialog for a play.

dale: that's not what p.g. is all about.

bowerbird: i think it's important to
give people a good e-book experience.

dale: that's your opinion.

bowerbird: yes it is.

steve: (weakly) i can't...

dale: no it isn't.

steve: (weakly) get a...

bowerbird: is too.

steve: (weakly) word in edgewise...

dale: is not.

lurkers: will you two cut it out?

bowerbird: is so.

dale: is not...

fade to black.[6]

 ***

or, here's another way you can format that:

juliette:
romeo, romeo, wherefore art thou?

romeo:
right here, yum-yum darling, drinking this
nice delicious rum-and-cola which you so
kindly left for me, sweetheart my lover girl.

 ***

another use for this is for an interview:

magazine: what do you think of this?

interviewee: i like it a lot.

 ***

magazine: would you recommend it?

interviewee: i would recommend it highly.

 ***

magazine: thank you for speaking with us today.

interviewee: it was my pleasure.

 ***

or for question-and-answer segments:

q: what is the question?

a: that's a good question.


q: do you know the answer?

a: yes, i do.




## chapter 11<br><br>how to lay out tables


you can have tables in your book, simple ones.

to signify a table, you use the " | " or-bar tag.

then use multiple-spaces to separate the cells.
there must be at least 3 spaces between cells.

 | table 1         column 1   column 2  
 | plain-text    yes            yes  
 | x.m.l.           no             yes  
 | .html             yes            no  
 | .rtf               no              yes  
 | .pdf             no              no

 ***

as per usual, you can just use the tag on the
first line, skipping it on the subsequent lines,
but you must start those subsequent lines with
at least 3 spaces.

 |  table 1         column 1   column 2  
   plain-text    yes            yes  
    x.m.l.           no             yes  
    .html             yes            no  
    .rtf               no              yes  
    .pdf             no              no

 ***

you can also use or-bars to separate the cells.

 | table 1 | column 1 | column 2  
 | plain-text | yes | yes  
 | x.m.l. | no | yes  
 | .html | yes | no  
 | .rtf | no | yes  
 | .pdf | no | no

 ***

you can even mix up these two approaches.

 |      table 1           column 1  |      column 2  
 |    plain-text            yes                 yes  
 |    x.m.l.                no                 yes  
 |    .html                   yes | no  
 | .rtf | no | yes  
 |    .pdf            |          no                   no




## chapter 12<br><br>justify poetry silly-willy


most paper-books traditionally used justification,
where paragraph-lines are even on the right side,
but the web usually serves users unjustified text
(browsers were initially underpowered and crude),
so a long-form standard on justification is in flux.

whatever the answer is to that question, though,
there are structures in need of special treatment.

sometimes you want to set off an element
by giving it a different form of justification,
and z.m.l. makes it easy for you to do that.

on this, the most common need is a centered line.

indeed, since the width of the ultimate viewport is
always of various sizes, it ends up that _centering_
is often the most reliable way to set off an element.

so z.m.l. makes it extremely simple to center a line;
just use a space in the first column to get the effect.

the line below starts with a space, in the input file,
so when it's rendered out, the line will be centered.

 this line will be centered in the output.

this rule works as well for each line within a chunk;
each line in the haiku entered below has a space in
the first column, so each is centered in the output:

 t.v. will eat you  
 (a haiku by bowerbird intelligentleman)  
 .  
 t.v. will eat you  
 out of a satellite dish  
 with a tuning fork  
 .

sometimes you want to indent a _block_ of lines,
rather than have each line individually centered...

z.m.l. lets you obtain that kind of effect easily;
just use multiple spaces -- you need at least 5 --
to get a chunk that's left-justified and indented.

               a haiku for you  
               (by bowerbird intelligentleman)  
 .  
               haiku have three lines  
               and seventeen syllables  
               five, seven, and five  
 .

note that this won't necessarily _center_ the block
-- because we don't know the size of the viewport
in advance, and the width of the block will vary too,
depending on the font the reader has chosen to use
-- it becomes harder to specify the indent correctly.

but whatever the case, more spaces at the start of
a line will cause a bigger indent, so act accordingly.
it's best to be conservative; a small indent still works.

you can also vary spacing, as some poems like to do.

      six spaces at the start of this line  
            12 spaces at the start of this line  
      six spaces at the start of this line  
            12 spaces at the start of this line

            12 spaces at the start of this line  
                        24 spaces at the start of this line  
            12 spaces at the start of this line  
                        24 spaces at the start of this line

you can even get downwight fweaky!

      six spaces at the start of this line  
          ten spaces at the start of this line  
              14 spaces at the start of this line  
                  18 spaces at the start of this line  
                      22 spaces at the start of this line  
                          26 spaces at the start of this line  
                      22 spaces at the start of this line  
                  18 spaces at the start of this line  
              14 spaces at the start of this line  
          ten spaces at the start of this line  
      six spaces at the start of this line

 ***

 the input for the 2 chunks that follow has  
 a space in the first column of all of the lines.

 you know about a space in the first column.

 a space in the first column will center a line,  
 or -- applied to each line in a group of lines --  
 will end up centering the entire block of lines.

 ***

  this chunk, and the next chunk as well, has  
  two (2) spaces at the beginning of each line.

  if you want to explicitly declare a block as  
  left-justified, you start it with _two_ _spaces._  
  this puts it at the left margin, with no indent.

 ***

   this chunk, and the next chunk as well, has  
   three (3) spaces at the beginning of each line.  
   _three_ _spaces_ at the start of a line will  
   cause it to be centered, just like one space.

   yes, it's redundant, but it keeps continuity  
   as we move from 2 spaces to 4, taking the  
   alignment from left through center to right.

 ***

    this chunk, and the next chunk as well, has  
    four (4) spaces at the beginning of each line.

    so, filling out the left-center-right concept,  
    4 spaces at the start of a line will cause it to  
    be right-justified.

 ***

 so, to review, 1 space = centered

  2 spaces = left-justified

   3 spaces = centered as well

    4 spaces = right-justified




## chapter 13<br><br>unlucky number 13


there is no 13th floor in most buildings.




## chapter 14<br><br>epigraphs and epitaphs


    _there's_ _an_ _old_ _proverb_  
    _that_ _says_ _just_ _about_  
    _whatever_ _you_ _want_ _it_ _to..._  
    _--_ _slashdot_


sometimes a chapter starts with a nice pithy quote,
which is usually _italicized,_ and often right-justified.

so you wanna be able to handle that kind of thing.[5]




## chapter 15<br><br>preformatted text


some long-form texts have a need to present
preformatted text, like computer source-code.

for such situations, you'll use the ` backtick.

inside a paragraph, or some other element,
the `backtick` marks something as code,
exactly like _underscores_ mark _italics_ and
*asterisks* mark *bold.* specifically, backticks
that surround text cause it to be displayed
with the .html `preformatted` -- `pre.`

however, the backtick can also be used as
a block tag, to signal that an entire chunk
should be shown using the .html "code" tag.

while the " ` " can be used to start each line,
that would cause the code in the input file to
be "polluted" by those backticks, so it's often
best to begin the chunk with an empty tag:

 `
// who needs 4 characters to make a fence?
// the command is to not repeat oneself

 ~
// that is a tilde.
// i always spell that word wrong

 `  
 ` // now is the time for all good men to  
 ` // come to the aid of their good women

 ~  
 ~ // the backtick code in the line above has  
 ~ // signified that this chunk is source-code  
 ~ // that should be shown with .html's "code"  
 ~ // tag, i.e., monospaced and preformatted.  
 ~ <script type=text/javascript>  
 ~    $(document).ready(function () {  
 ~       gdi=setInterval(function(){doit()},1000)  
 ~    })  
 ~ </script>  
 ~ // this is known in light-markup circles as  
 ~ // "a fenced block", since the backtick(s)  
 ~ // up top sets a "fence" around the code.  
 ~ // the code block is then usually followed  
 ~ // by a closing "fence" of backtick(s), but  
 ~ // in z.m.l., a blank line closes all elements,  
 ~ // so we don't need a "closing" fence here.  
 ~ // however, this does mean that the code  
 ~ // cannot contain any blank lines inside it,  
 ~ // so you must stay cognizant of that fact.  
 ~ // comment out blank lines in your code.




## chapter 16<br><br>hyphens and dashes


use a double-dash -- like these in the input --
to get a nice smooth typographic em-dash
in your output, such as the .html. it's best to
put spaces around your double-dashes, or else
they can sometimes cause word-wrap problems.

don't use a single-dash - like this bad example -
where you want an em-dash, or it will look _bad._
even http://medium.com and http://kottke.org
make that mistake. it's rather embarrassing, eh?




## chapter 17<br><br>hyphenation stinks


do not hyphenate your text. e-books do not need it.
and you never know exactly how the text will reflow.




## chapter 18<br><br>spaces after a sentence


typesetters in the past set a bit of extra space
between sentences, so the separation is clear.
i support that, as i think it makes it look nicer.

now some people try to get this nicer look via
the old typewriter-trick of putting 2 spaces at
the end of a sentence. but that doesn't work
when the text is turned to .html. and further,
the extra spaces mess with our editing process,
where we often search the text for 2-spaces,
just to make sure we haven't inadvertently
introduced extra spaces in unwanted places.

so the best tactic to use these days is to put
only one space after a sentence, and _hope_
browsers will soon support better typography.




## chapter 19<br><br>the end-notes section


here are the footnotes used in this book.

[1] in later years, it was made clear that
lincoln was referring to all "people", and
not just men, that women are equally equal.

[2] again, in later years, it was made clear
lincoln was referring to all "people", and
not just men, that women are equally equal.

[3a] personally, i don't think we need to
make a distinction between footnotes
and endnotes any more, i believe that
all the types of notes should be stored
at the end of the file, like these notes,
but i think the person should be able to
_display_ them at the point of reference
in the actual body of the text. therefore,
they are actually a sort of hybrid between
footnotes _and_ endnotes, combining the
strengths and convenience of both types.

[4] this is a test footnote. because of that,
it's going to go on and on and on.
this is a test footnote. because of that,
it's going to go on and on and on.
this is a test footnote. because of that,
it's going to go on and on and on.
this is a test footnote. because of that,
it's going to go on and on and on.
this is a test footnote. because of that,
it's going to go on and on and on.

look, it even has a second paragraph!
this is a test footnote. because of that,
it's going to go on and on and on.
this is a test footnote. because of that,
it's going to go on and on and on.
this is a test footnote. because of that,
it's going to go on and on and on.

oh no! a third paragraph. way too long!
this is a test footnote. because of that,
it's going to go on and on and on.
this is a test footnote. because of that,
it's going to go on and on and on.
this is a test footnote. because of that,
it's going to go on and on and on.
this is a test footnote. because of that,
it's going to go on and on and on.
this is a test footnote. because of that,
it's going to go on and on and on.


this paragraph is preceded by 2 blank lines,
which means it is _not_ part of the footnote.
it should be treated as a regular paragraph.

[5] this is another test footnote. but it will be short.

[6] you probably shouldn't mix footnotes and endnotes,
like i have done in this book, because that'll just make it
confusing for your readers. so choose one or the other.




## chapter 20<br><br>meta-data for this book


here's the meta-data:

 o title = a brief introduction to z.m.l.  
 o author = bowerbird intelligentleman  
 o purpose = brief introduction to z.m.l.  
 o markup = zen markup language (.zml)  
 o isbn = urn:isbn:0000000000000  
 o publisher = jaguar(ps)  
 o subject = zen markup language, a.k.a. z.m.l.  
 o rights = copyright 2013 -- all rights reserved

 http://wellwellandwhathavewehere.com/islandscoupon.png
 
  ![](http://wellwellandwhathavewehere.com/islandscoupon.png)

xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

 https://raw.githubusercontent.com/bbirdiman/githubblog/master/githubblog2.md
