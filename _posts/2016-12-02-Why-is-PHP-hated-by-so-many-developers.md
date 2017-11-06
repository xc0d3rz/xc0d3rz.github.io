---
layout:     post
title:      "Why is PHP hated by so many developers ?"	
date:       2016-12-2 12:00:00
author:     "Anders Kaseorg"
category: web-development
---
<p>There are many good reasons to hate PHP, and they run way deeper than some inconsistently named functions with misordered parameters. Learning PHP will actually transform you into a <strong>worse programmer</strong>, and writing in PHP will leave behind a mess that’s a <strong>costly nightmare to maintain</strong> and clean up after. Here are my top four reasons.</p>

<ol>
<li><p><strong>PHP encourages an insecure programming style</strong>  <br>
By design its very syntax encourages you to splice unescaped values directly into database queries and HTML output without thinking twice, leading to <a href="https://en.wikipedia.org/wiki/SQL_injection">SQL injection</a> and <a href="https://en.wikipedia.org/wiki/Cross-site_scripting">cross-site scripting</a> vulnerabilities. <br>
To write any PHP code that isn’t riddled with these security holes, you have to actively work against this by carefully sprinkling your code with calls to functions with long names like <code>htmlspecialchars()</code> [<a href="http://php.net/manual/en/function.htmlspecialchars.php">1</a>] and <code>mysql_real_escape_string()</code> [<a href="http://php.net/manual/en/function.mysql-real-escape-string.php">2</a>] (that’s right, make sure you use the <code>real</code> one!).  <br>
If you want any protection from cross-site request forgery attacks, you need to go implement it yourself. <br>
<br>
Which of these would you rather write ? <br>
<br>
(a) <code>&lt;input type="text" name="user" value="&lt;?php echo $_REQUEST[user]"&gt;</code> <br>
<br>
(b) <code>&lt;input type="text" name="user" value="&lt;?php echo htmlspecialchars($_REQUEST[user])"&gt;</code> <br>
<br>
(a) <code>&lt;?php $result = mysql_query("SELECT * FROM users WHERE user='$_REQUEST[user]' AND pass='$_REQUEST[pass]'"); ?&gt;</code> <br>
<br>
(b) <code>&lt;?php $result = mysql_query("SELECT * FROM users WHERE user='" . mysql_real_escape_string($_REQUEST[user]) . "' AND pass='" .  mysql_real_escape_string($_REQUEST[pass]) . "'"); ?&gt;</code> <br>
<br>
If you ever wrote version (a) anywhere in your application, then your users’ accounts <a href="http://www.acunetix.com/blog/news/barracuda-networks-breached/">have been compromised</a>. <br>
<br>
This is all basic stuff that any decent <a href="https://en.wikipedia.org/wiki/Web_framework">web framework</a> for real programming languages will take care of for you automatically, but most PHP developers are too lazy to do it by hand (or don’t even understand that they need to), and even the best ones slip up occasionally. That’s why web applications written in PHP consistently have terrible <a href="http://cve.mitre.org/cgi-bin/cvekey.cgi?keyword=wordpress">security</a> <a href="http://cve.mitre.org/cgi-bin/cvekey.cgi?keyword=mediawiki">track</a> <a href="http://cve.mitre.org/cgi-bin/cvekey.cgi?keyword=drupal">records</a>.</p></li>
<li><p>The language is a <strong>minefield of hidden security problems</strong> <br>
Even something as simple as <br>
<br></p>

<pre class="prettyprint"><code class="language-php hljs "><span class="hljs-preprocessor">&lt;?php</span>
<span class="hljs-keyword">if</span> (strcmp(<span class="hljs-variable">$_POST</span>[<span class="hljs-string">'password'</span>], <span class="hljs-string">'sekret'</span>) == <span class="hljs-number">0</span>) {
<span class="hljs-keyword">echo</span> <span class="hljs-string">"Welcome, authorized user!\n"</span>;
} <span class="hljs-keyword">else</span> {
<span class="hljs-keyword">echo</span> <span class="hljs-string">"Go away, imposter.\n"</span>;
}
<span class="hljs-preprocessor">?&gt;</span></code></pre>

<p>is vulnerable to attack:</p>

<pre class="prettyprint"><code class="language-bash hljs ">$ curl <span class="hljs-operator">-d</span> password=sekret http://andersk.scripts.mit.edu/strcmp.php
Welcome, authorized user!
$ curl <span class="hljs-operator">-d</span> password=wrong http://andersk.scripts.mit.edu/strcmp.php
Go away, imposter.
$ curl <span class="hljs-operator">-d</span> password[]=wrong http://andersk.scripts.mit.edu/strcmp.php
Welcome, authorized user!</code></pre>

<p>(This example was not artificially constructed to demonstrate a potential security problem; it came from a real-world website breakin! This vulnerability was silently introduced by a <a href="http://php.net/strcmp#102677">change to <code>strcmp</code> in PHP 5.3 and later</a> that wasn’t even mentioned in the <a href="http://php.net/releases/5_3_0.php">release announcement</a> or the <a href="http://php.net/migration53">migration guide</a>.)</p></li>
<li><p>The <strong>documentation is full of harmful advice</strong>  <br>
Even the sample code from the <a href="http://www.php.net/manual/en/faq.html.php#faq.html.encoding">official FAQ</a> on how to avoid cross-site scripting attacks is vulnerable to cross-site scripting attacks! I couldn’t make this up if I tried:</p>

<pre class="prettyprint"><code class="language-php hljs "><span class="hljs-preprocessor">&lt;?php</span>
<span class="hljs-keyword">echo</span> <span class="hljs-string">"&lt;input type='hidden' value='"</span> . htmlspecialchars(<span class="hljs-variable">$data</span>) . <span class="hljs-string">"' /&gt;\n"</span>;
<span class="hljs-preprocessor">?&gt;</span></code></pre>

<p>(<code>htmlspecialchars</code>[<a href="http://php.net/manual/en/function.htmlspecialchars.php">1</a>] does not escape single quotes!)</p></li>
<li><p>PHP references are brain-damagingly wrong <br>
I mean that literally—I know from personal experience that <strong>PHP references will damage your brain</strong> in ways that will make it harder for you to learn how references and pointers work in real programming languages.</p></li>
</ol>

<p>Update, two years later: It’s telling that the world’s largest PHP shop, Facebook, has invested millions of dollars of engineering effort into developing a new language to replace PHP and <a href="https://code.facebook.com/posts/264544830379293/hack-a-new-programming-language-for-hhvm/">migrating nearly their entire codebase</a> away from PHP. I’m only amazed that it didn’t happen sooner.</p>

<p>For hundreds of other reasons, see</p>

<ul>
<li><p><a href="http://me.veekun.com/blog/2012/04/09/php-a-fractal-of-bad-design/">PHP: a fractal of bad design</a></p>

<blockquote>
  <p>PHP is not merely awkward to use, or ill-suited for what I want, or suboptimal, or against my religion. … Virtually every feature in PHP is broken somehow. The language, the framework, the ecosystem, are all just <strong>bad</strong>. And I can’t even point out any single damning thing, because the damage is so systemic. … But I’ve got to get this out of my system. So here goes, one last try.</p>
</blockquote></li>
<li><p><a href="http://phpsadness.com/">PHP Sadness</a></p>

<blockquote>
  <p>These are things in PHP which make me sad. They are real, objective issues which I have personally encountered in my normal day-to-day activites.</p>
</blockquote></li>
<li><p><a href="http://www.codinghorror.com/blog/2012/06/the-php-singularity.html">The PHP Singularity</a></p>

<blockquote>
  <p>What’s depressing is not that PHP is horribly designed. Does anyone even dispute that PHP is the worst designed mainstream “language” to blight our craft in decades? What’s truly depressing is that <strong>so little has changed</strong>.</p>
</blockquote></li>
</ul>

<p>(* The PHP documentation, starting <a href="http://svn.php.net/viewvc?view=revision&amp;revision=324878">a year after I first wrote this answer</a>, finally discourages new use of the original mysql extension in favor of the newer mysqli and PDO_MySQL extensions, which provide slightly better solutions to the SQL injection problem, if you’re lucky enough to have a PHP installation that has them enabled and a code base that uses them.)</p>

<p><a href="https://www.quora.com/Why-is-PHP-hated-by-so-many-developers">Post source</a></p>
