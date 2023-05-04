Download Link: https://assignmentchef.com/product/solved-csci-3155-lab-assignment-6
<br>
Unlike the last few labs, our primary focus in the lab is not new language features. Instead, we will explore some related topics that we have bypassed in prior labs, namely parsing. We will also have a chance to play with the cool interpreters that we have built.

Concretely, we will consider regular expressions. We write construct a parser for a language of regular expressions and implement a regular expression matcher in Scala. For fun, we extend our Lab 5 interpreters with regular expression literals and regular expression matching (like JavaScript) using your parser and expression matcher.

<strong>PL Ideas </strong>Recursive descent parsing. Regular languages.

<strong>FP Skills </strong>Backtracking search with continuations.

<strong>Instructions. </strong>From your team of 8-10 persons in your lab section, find a new partner for this lab assignment (different from your Lab 1 partner). You will work on this assignment closely with your partner. However, note that <strong>each student needs to submit </strong>and are individually responsible for completing the assignment.

You are welcome to talk about these questions in larger groups. However, we ask that you write up your answers in pairs. Also, be sure to acknowledge those with which you discussed, including your partner and those outside of your pair.

Recall the evaluation guideline from the course syllabus.

<em>Both your ideas and also the clarity with which they are expressed matter—both in your English prose and your code!</em>

<em>We will consider the following criteria in our grading:</em>

<ul>

 <li>How well does your submission answer the questions? <em>For example, a common mistake is to give an example when a question asks for an explanation. An example may be useful in your explanation, but it should not take the place of the explanation.</em></li>

 <li>How clear is your submission? <em>If we cannot understand what you are trying to say, then we cannot give you points for it. Try reading your answer aloud to yourself or a friend; this technique is often a great way to identify holes in your reasoning. For code, not every program that “works” deserves full credit. We must be able to read and understand your intent. Make sure you state any preconditions or invariants for your functions (either in comments, as assertions, or as </em><em>require clauses as appropriate).</em></li>

</ul>

1

Try to make your code as concise and clear as possible. Challenge yourself to find the most crisp, concise way of expressing the intended computation. This may mean using ways of expression computation currently unfamilar to you.

Finally, make sure that your file compiles and runs on COG. A program that does not compile will <em>not </em>be graded.

<strong>Submission Instructions.                 </strong>Upload to the moodle exactly three files named as follows:

<ul>

 <li>Lab6-<em>YourIdentiKey</em>.scala with your answers to the coding exercises</li>

 <li>Lab6Spec-<em>YourIdentiKey</em>.scala with any updates to your unit tests. Note that there are no closed autograder tests for this assignment.</li>

 <li>Lab6-<em>YourIdentiKey</em>.jsy with a challenging test case for your JAVASCRIPTY Do not use the extra credit regular expressions.</li>

 <li>Lab6-<em>YourIdentiKey</em>.pdf with your answers to the written questions (scanned, clearly legible handwritten write-ups are acceptable)</li>

</ul>

Replace <em>YourIdentiKey </em>with your IdentiKey (e.g., I would submit Lab6-bec.pdf and so forth). Don’t use your student identification number. To help with managing the submissions, we ask that you rename your uploaded files in this manner.

Submit your Lab6.scala file to COG for auto-testing. We ask that you submit both to COG and to moodle in case of any issues.

Sign-up for an interview slot for an evaluator. To fairly accommodate everyone, the interview times are strict and <strong>will not be rescheduled</strong>. Missing an interview slot means missing the interview evaluation component of your lab grade. Please take advantage of your interview time to maximize the feedback that you are able receive. Arrive at your interview ready to show your implementation and your written responses. Implementations that do not compile and run will not be evaluated.

<strong>Getting Started. </strong>Clone the code from the Github repository with the following command: git clone -b lab6 https://github.com/bechang/pppl-labs.git lab6

<ol>

 <li><strong>Feedback</strong>. Complete the survey on the linked from the moodle after completing this assignment. Any non-empty answer will receive full credit.</li>

 <li><strong>Regular Expressions. </strong>Consider the following syntax for a language of regular expressions. We note the corresponding Scala <strong>case class </strong>or <strong>case object </strong>used to construct abstract syntax trees of type RegExpr (shown below the grammar in full).</li>

</ol>

<table width="525">

 <tbody>

  <tr>

   <td width="142">regular expressions</td>

   <td width="109"><em>re </em>::<em>= </em>! <em>| </em>#<em>| </em>. <em>| c</em><em>| re</em><sub>1</sub><em>re</em><sub>2 </sub><em>| re</em><sub>1</sub>‘<em>|</em>’<em>re</em><sub>2</sub><em>| re</em><sub>1</sub><em>∗</em><em>| re</em><sub>1</sub><em>+</em><em>| re</em><sub>1</sub>? <em>| re</em><sub>1</sub>&amp;<em>re</em><sub>2</sub><em>|∼re</em><sub>1</sub></td>

   <td width="274">no string (RNoString) empty string (REmptyString) any character (RAnyChar) the character <em>c </em>(RSingle(<em>c</em>)) concatenation (RConcat(<em>re</em><sub>1</sub>,<em>re</em><sub>2</sub>)) union, “or” (RUnion(<em>re</em><sub>1</sub>,<em>re</em><sub>2</sub>))Kleene star, “0-or-more” (RStar(<em>re</em><sub>1</sub>))“1-or-more” (RPlus(<em>re</em><sub>1</sub>)) “0-or-1” (ROption(<em>re</em><sub>1</sub>)) intersection, “and” (RIntersect(<em>re</em><sub>1</sub>,<em>re</em><sub>2</sub>))complement, “not” (RNeg(<em>re</em><sub>1</sub>))</td>

  </tr>

 </tbody>

</table>

<strong>sealed abstract class </strong>RegExpr <strong>case object </strong>RNoString <strong>extends </strong>RegExpr <strong>case object </strong>REmptyString <strong>extends </strong>RegExpr <strong>case class </strong>RSingle(c: Char) <strong>extends </strong>RegExpr <strong>case class </strong>RConcat(re1: RegExpr, re2: RegExpr) <strong>extends </strong>RegExpr <strong>case class </strong>RUnion(re1: RegExpr, re2: RegExpr) <strong>extends </strong>RegExpr <strong>case class </strong>RStar(re1: RegExpr) <strong>extends </strong>RegExpr <strong>case object </strong>RAnyChar <strong>extends </strong>RegExpr <strong>case class </strong>RPlus(re1: RegExpr) <strong>extends </strong>RegExpr <strong>case class </strong>ROption(re1: RegExpr) <strong>extends </strong>RegExpr <strong>case class </strong>RIntersect(re1: RegExpr, re2: RegExpr) <strong>extends </strong>RegExpr <strong>case class </strong>RNeg(re1: RegExpr) <strong>extends </strong>RegExpr

A regular expression defines a regular language (language = a set of strings). The first six constructors are the basic regular expression constants and operators. Let us write <em>L </em>(<em>re</em>) for the language specified by the regular expression <em>re</em>:

<ul>

 <li><em>L </em>(!) <em>=</em><sup>def</sup>def<em>;</em>, that is, the empty set.</li>

 <li><em>L </em>(#) <sup>def</sup><em>= </em>{“”}, that is, the set with the empty string.</li>

 <li><em>L </em>(<em>c</em>) <em>= </em>{“<em>c</em>“}, that is, the set with the string matching the single character <em>c</em>.</li>

 <li><strong>Concatenation</strong>. A string <em>s = s</em><sub>1</sub><em>s</em><sub>2 </sub><em>∈L </em>(<em>re</em><sub>1</sub><em>re</em><sub>2</sub>) iff <em>s</em><sub>1 </sub><em>∈L </em>(<em>re</em><sub>1</sub>) and <em>s</em><sub>2 </sub><em>∈L </em>(<em>re</em><sub>2</sub>).</li>

 <li><strong>Union</strong>. A string <em>s ∈L </em>(<em>re</em><sub>1</sub><em>|re</em><sub>2</sub>) iff <em>s ∈L </em>(<em>re</em><sub>1</sub>) or <em>s ∈L </em>(<em>re</em><sub>2</sub>) (i.e., <em>s ∈L </em>(<em>re</em><sub>1</sub>)<em>∪L </em>(<em>re</em><sub>2</sub>)).</li>

 <li><strong>Kleene Star</strong>. A string <em>s ∈L </em>(<em>re</em><sub>1</sub><em>∗</em>) iff <em>s </em>is in zero-or-more concatenations of <em>re</em><sub>1</sub>.</li>

</ul>

The basic mathematical definition of regular expressions given above are often extended with more operators in programming languages as a convenience for developers. We consider five extended operators:

<ul>

 <li><strong>Any Character</strong>. A string <em>s ∈L </em>(.) iff <em>s </em>consists of any single character.</li>

 <li><strong>One-Or-More</strong>. A string <em>s ∈ L </em>(<em>re</em><sub>1</sub><em>+</em>) iff <em>s </em>is in one-or-more concatenations of <em>re</em><sub>1 </sub>(i.e., <em>s ∈L </em>(<em>re</em><sub>1</sub><em>re</em><sub>1</sub><em>∗</em>)).</li>

 <li><strong>Zero-Or-More</strong>. A string <em>s ∈ L </em>(<em>re</em><sub>1</sub>?) iff <em>s </em>is in zero-or-one matches of <em>re</em><sub>1 </sub>(i.e., <em>s ∈</em></li>

</ul>

<em>L </em>(#<em>|re</em><sub>1</sub>)).

<ul>

 <li><strong>Intersection</strong>. A string <em>s ∈ L </em>(<em>re</em><sub>1</sub>&amp;<em>re</em><sub>2</sub>) iff <em>s ∈ L </em>(<em>re</em><sub>1</sub>) and <em>s ∈ L </em>(<em>re</em><sub>2</sub>) (i.e., <em>s ∈ L </em>(<em>re</em><sub>1</sub>)<em>∩ L </em>(<em>re</em><sub>2</sub>)).</li>

 <li><strong>Complement</strong>. A string <em>s ∈L </em>(<em>∼ re</em><sub>1</sub>) iff <em>s ∉L </em>(<em>re</em><sub>1</sub>).</li>

</ul>

Note that !, #, &amp;, and <em>∼ </em>operators are not typically in regular expression constructs in programming languages (e.g., in JavaScript, Perl), though all others are almost always present.

You may complete the following parts in mostly any order. Matching is the most challenging part, but we suggest you get at least a few of simpler cases done first before working on parsing.

(a) <strong>Regular Expression Matcher: Continuations</strong>. For this exercise, we will implement a basic backtracking regular expression matcher.

We will write a regular expression matcher

<strong>def </strong>retest(re: RegExpr, s: String): Boolean

that given a regular expression re and a string s returns <strong>true </strong>if the string s belongs to the language described by the regular expression re and otherwise returns <strong>false</strong>. The implementation of retest is provided for you, which calls a helper function test that you provide. This function corresponds to a restricted implementation of the test method on RegExp objects in JavaScript. For simplicity, we consider only whole string matches, which would be equivalent to

/^<em>re</em>$/.test(<em>s</em>)

in JavaScript.

We will implement our regular expression matcher using continuations. In particular, we will implement a helper function:

<strong>def </strong>test(re: RegExpr, chars: List[Char])

(sc: List[Char] <strong>=&gt; </strong>Boolean): Boolean

This helper function will see if a <em>prefix </em>of chars matches the regular expression re. If there is a prefix match, then the success continuation sc is called with the remainder of chars that has yet to be matched. That is, the success continuation sc captures “what to do next if a prefix of chars successfully matches re.” If test discovers a failure to match, then it can “return <strong>false </strong>early.”

A continuation a special kind of callback in that its a callback for the higher-order function itself. In this example, test is a higher-order function that takes in a continuation sc that is callback for itself “in the future,” that is, in a recursive call. More precisely, a continuation captures an action to do on return. It accumulates an action that should be performed after the current downwards recursive sequence is complete.

The idea of continuations is an underlying concept in asynchronous programming, such as in client-server systems and interactive systems (e.g., mobile apps or any GUIbased app). For example, Node.js forces continuation-style programming because all I/O operations are asynchronous.

<strong>ExtraCredit</strong>. The RIntersect and RNeg cases will be considered extra credit. All other cases are part of this problem.

<strong>Hints</strong>.

<ul>

 <li>RStar is the most difficult case (that is not extra credit). Consider completing the other cases first. Implementing the RConcat case might help clarify how the success continuation is used.</li>

 <li>From an general algorithm level, our regular expression matcher and our recursive descent parser (in the next part). They are both backtracking search algorithms on an input string. The input string is treated like a stream of characters where we iteratively try to “consume” from the front of the stream according to some constraints (i.e., “Does it match ‘this’ part of the regular expression?” or “Does it match ‘this’ production?”). When the first try fails, we go on to try the next possibility and so forth.</li>

</ul>

(b) <strong>Regular Expression Parser: Recursive Descent Parsing</strong>

To specify, how the concrete syntax of regular expressions is transformed into abstract syntax. We resolve the ambiguity in the grammar given above by saying that all binary operators should be left associative and the precedence of the operators are ordered as follows (from highest precedence to lowest): {<em>∗</em>,<em>+</em>,?}, <em>∼</em>, (juxtaposition for concatenation), &amp;, and <em>|</em>. A set {…} means all of the operators are at the same precedence level.

In this part, we will implement one of the most basic parsing algorithms: recursive descent parsing. Recursive descent parsing is generic pattern for manually constructing parsers for simple languages. Parsing is an area rich with numerous more efficient algorithms, useful tools, and interesting techniques, as well as elegant theory. For more complex grammars, one often uses a type tool called a <em>parser generator</em>. Using such tools is generally a topic in a compilers course.

A recursive descent parser works top-down in the grammar and left-to-right in the input string. The basic pattern is to write a recursive function for each non-terminal in the grammar. Each parsing function tries to parse (and consume) characters from the input string by applying each production for that non-terminal in sequence. If applying a production fails, then it backtracks and tries the next one. Applying a production means either (1) consuming characters from the left of the string to match a terminal or (2) calling the function corresponding to a non-terminal to try to match that non-terminal. In the end, one ends up with a set of mutually recursive functions that parallels the structure of the grammar.

The first task for constructing a recursive descent parser is to refactor the grammar to eliminate ambiguity. But not any umambiguous grammar with do. One key requirement for recursive descent parsing is that the grammars must not contain left recursion. Otherwise, your parser will go into an infinite loop (why?).

<ol>

 <li>In your write-up, give a refactored version of the <em>re </em>grammar that eliminates ambiguity in BNF (not EBNF) using left recursion. Use the new non-terminal names from the EBNF grammar below (<em>union</em>, <em>intersect</em>, etc.).</li>

 <li>Explain briefly why a recursive descent parser following your grammar with left recursion would go into an infinite loop.</li>

</ol>

Without left recursion, obtaining left associativity requires a little bit more work. To get there, we consider an extension of the meta-language for describing grammars with the braces {<em>α</em>} to mean a sequence of 0-or-more of <em>α</em>. This notation is part of what’s known as EBNF (Extended Backus-Naur Form). We give a refactored version of the previous grammar that eliminates ambiguity.

<em>re </em>::<em>= union</em>

<em>union </em>::<em>= intersect </em>{ ‘<em>|</em>’ <em>intersect </em>}

<em>intersect </em>::<em>= concat </em>{ ‘&amp;’ <em>concat </em>}

<em>concat </em>::<em>= not </em>{ <em>not </em>} <em>not </em>::<em>= </em>‘ <em>∼ </em>’<em>not | star</em>

<em>star </em>::<em>= atom </em>{ ‘<em>∗</em>’ <em>| </em>‘<em>+</em>’ <em>| </em>‘?’ } <em>atom </em>::<em>= </em>‘!’ <em>| </em>‘#’ <em>| c | </em>‘.’ <em>| </em>‘(’<em>re</em>‘)’

The quotes ‘ ’ emphasize the symbols that are terminals in the object language (rather than meta-level symbols of the grammar notation). The 0-or-more sequence operator can be translated into BNF by using another non-terminal, which is what we need for our recursive descent parser. As an example, we can translate

<em>union </em>::<em>= intersect </em>{ ‘<em>|</em>’ <em>intersect </em>}

in EBNF to <em>union </em>::<em>= intersect unions unions </em>::<em>= ε| </em>‘<em>|</em>’ <em>intersect unions</em>

in BNF. We can now use this grammar to structure our recursive descent parser. Notice that <em>unions </em>looks a lot like a list. Now, we can enforce left associativity of <em>| </em>by constructing RUnion abstract syntax nodes as we “fold-left” across the <em>intersect </em>“elements.”

iii. In your write-up, give the full refactored grammar in BNF without left recursion and new non-terminals like <em>unions </em>for lists of symbols. You will need to introduce new terminals for <em>intersects </em>and so forth.

Scala includes a powerful library for constructing parsers. We will use a small bit of that library to handle input and parsing results. We will implement a Scala object called RegExprParser that derives from Parsers:

<strong>object </strong>RegExprParser <strong>extends </strong>Parsers {

<strong>type </strong>Elem = Char

<strong>def </strong>re(next: Input): ParseResult[RegExpr]

…

}

In this object, you should define your mutually recursive functions that define a recursive descent parser (one for each non-terminal). For example, the top-level function is re that takes a parameter of type Input and returns something of type ParseResult. These two types are inherited from Parsers. The relevant parts are as follows:

<strong>type </strong>Input = Reader[Elem] <strong>sealed abstract class </strong>ParseResult[T] { <strong>val </strong>next: Input

<strong>def </strong>map[U](f: T <strong>=&gt; </strong>U): ParseResult[U]

}

<strong>case class </strong>Success[T](result: T, next: Input) <strong>extends </strong>ParseResult[T] <strong>case class </strong>Failure(msg: String, next: Input) <strong>extends </strong>ParseResult[Nothing]

The ParseResult type is somewhat similar to the Option type. A successful parse is indicated by returning a Success that bundles the result (in our case a RegExpr) and the remaining input. To determine what RegExpr abstract syntax tree to construct on a successful parse, you should consult the specification in the original, unrefactored grammar for <em>re</em>. The Failure case class indicates a parse failure with an error message and the remaining input.

Scala’s parsing library is a combinator parsing library. A combinator means essentially a higher-order function. So a combinator parsing library is a set of higher-order functions in a library that one uses to construct parsers. The algorithm of a parser constructed using the parser combinators is very similar to the one that you implement in your recursive descent parser. What you might observe after you complete your recursive descent parser is that there is a lot of repeated boilerplate in your code

(and then just imagine how much repetition there would be in a larger language like JAVASCRIPTY!). What the parser combinators do is essentially factor out the boilerplate into a generic library that can be used, much like the higher-order methods in the collection classes.

After you have constructed your recursive descent parser in this exercise, you will be able to approach Scala’s combinator parsing library and write your future parsers with it!

A reference implementation of the regular expression parser built using Scala’s combinator parsing library is given in RegExprParser.scala. The code will probably not make much sense until you have written your recursive descent parser, but then, it will be interesting to compare your implementation to it. You can also use the reference parser implementation to work on the matcher before completing your parser.

(c) <strong>Regular Expression Literals in JavaScripty</strong>. Let’s extend our Lab 5 interpreter with regular expression literals and regular expression matching. We extend JAVASCRIPTY as follows:

expressions <em>e </em>::<em>= ···| </em>/ˆ<em>re</em>$/ values <em>v </em>::<em>= ···| </em>/ˆ<em>re</em>$/ types <em>τ </em>::<em>= ···|</em><strong>RegExp</strong>

to specify regular expression literals. We will treat regular expression literals as values and introduce a type for regular expressions <strong>RegExp</strong>. To represent regular expression literals, we use the following case classes:

<strong>case class </strong>RE(re: RegExpr) <strong>extends </strong>Expr <strong>case class </strong>TRegExpr <strong>extends </strong>Typ

To match JavaScript, we write the regular expression test operator as follows:

<em>e</em><sub>1</sub>.test(<em>e</em><sub>2</sub>)

where <em>e</em><sub>1 </sub>should have type <strong>RegExp </strong>and <em>e</em><sub>2 </sub>should have type <strong>string</strong>. We do not introduce a new abstract syntax node, as the above will already parse as

Call(GetField(<em>e</em><sub>1</sub>, “test”), List(<em>e</em><sub>2</sub>))

and special case matching for this in typeInfer and step before doing the usual actions for a Call node. The “do” rule in step will call your run-time function restep that you wrote in Scala (instead for example as a library in JAVASCRIPTY). One note is that ideally, we still want to permit fields named test in objects that store functions, so the above expression is only a regular expression test if <em>e</em><sub>1 </sub>is of type <strong>RegExp</strong>.

As an aside, this observation suggests that a potential translation to a distiguished “regular expression test” AST node from the above would have to be done during type analysis. We will not do this, as such a translation seems overkill for our later phases but such translations are important software architectural decisions in structuring an interpreter/compiler.

<ol>

 <li>In your write-up, give typing and small-step operational semantic rules for regular expression literals and regular expression tests based on the informal specification given above. Clearly and concisely explain how your rules enforce the constraints given above and any additional decisions you made. ii. <strong>Optional: </strong>Extend your Lab 5 interpreter to include regular expressions. The discussion above enables you to extend your Lab 5 interpreter so that you can have your own full JAVASCRIPTY interpreter!</li>

 <li><strong>Mini-Project</strong></li>

</ol>

As part of Lab 6, we will have a separate mini-project. For the mini-project, you will develop a video/screencast tutorial with your partner. The final deliverables for the mini-project are

<ol>

 <li>A short 5 minute presentation on your topic to be given in class.</li>

 <li>A 5-10 minute video/screencast on your topic. The in-class presentation should be treated as a practice run to get feedback before creating the video/screencast.</li>

</ol>

There are two choices of topic for your mini-project:

<ul>

 <li>Pick a software framework about which you would like to learn. Download, explore, and learn about the software framework before producing your video. Given the short timeframe, you may follow an existing tutorial about the framework as the content for your video. For example, you might say that you want to learn about Apache Spark <a href="https://spark.apache.org/">(</a><a href="https://spark.apache.org/">http://spark.apache.org/</a><a href="https://spark.apache.org/">)</a>, so you find a web-based tutorial as the basis for your video content (be sure to cite your sources!). However, in your video, you <strong>must connect something about the software framework to the course content</strong>. This requirement is typically easy to satisfy because just about any software framework will make repeated use of the core concepts of this course (e.g., functions are values, code as data, callbacks and higher-order functions, abstract data types). Your task is to identify instances of these concepts occurring in the software framework.</li>

 <li><strong>Or</strong>, pick a topic from the course about which you would like to review. Develop some new examples and/or exam-like questions on which to base your video tutorial on the subject. If you choose this route, you <strong>must create new content </strong>(e.g., examples, illustrations) for the basis of your video. For example, you might say that you want to review “encapsulating computation” and DoWith, so you develop some new examples to illustrate Option, List, and DoWith in a different light. Challenge yourself to think about explaining the concept in a different way than me.</li>

</ul>

By the Lab 6 deadline, you should have your content ready, but you have time to record and produce your video in the subsequent week. In your Lab 6 write-up, write 1–2 paragraphs describing your mini-project topic, what content you have gathered or created, and your plan for your video.

Be creative and have fun!