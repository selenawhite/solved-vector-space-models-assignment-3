Download Link: https://assignmentchef.com/product/solved-vector-space-models-assignment-3
<br>
In this assignment you will implement many of the things you learned in <a href="https://web.stanford.edu/~jurafsky/slp3/15.pdf">Chapter 15 of the textbook</a>. If you haven’t read it yet, now would be a good time to do that. We’ll wait. Done? Great, let’s move on.

We will provide a corpus of Shakespeare plays, which you will use to create a term-document matrix and a term-context matrix. You’ll implement a selection of the weighting methods and similarity metrics defined in the textbook. Ultimately, your goal is to use the resulting vectors to measure how similar Shakespeare plays are to each other, and to find words that are used in a similar fashion. All (or almost all) of the code you write will be direct implementations of concepts and equations described in <a href="https://web.stanford.edu/~jurafsky/slp3/15.pdf">Chapter 15</a>.

<em>All difficulties are easy when they are known.</em>

<h1 id="term-document-matrix">Term-Document Matrix</h1>

You will write code to compile a term-document matrix for Shakespeare’s plays, following the description in section 15.1.1 in textbook.

<blockquote>

 In a <em>term-document matrix</em>, each row represents a word in the vocabulary and each column represents a document from some collection. The figure below shows a small selection from a term-document matrix showing the occurrence of four words in four plays by Shakespeare. Each cell in this matrix represents the number of times a particular word (defined by the row) occurs in a particular document (defined by the column). Thus <em>clown</em> appeared 117 times in *Twelfth Night

</blockquote>

<table>

 <thead>

  <tr>

   <th></th>

   <th>As You Like It</th>

   <th>Twelfth Night</th>

   <th>Julias Caesar</th>

   <th>Henry V</th>

  </tr>

 </thead>

 <tbody>

  <tr>

   <td><strong>battle</strong></td>

   <td>1</td>

   <td>1</td>

   <td>8</td>

   <td>15</td>

  </tr>

  <tr>

   <td><strong>soldier</strong></td>

   <td>2</td>

   <td>2</td>

   <td>12</td>

   <td>36</td>

  </tr>

  <tr>

   <td><strong>fool</strong></td>

   <td>37</td>

   <td>58</td>

   <td>1</td>

   <td>5</td>

  </tr>

  <tr>

   <td><strong>crown</strong></td>

   <td>5</td>

   <td>117</td>

   <td>0</td>

   <td>0</td>

  </tr>

 </tbody>

</table>

The dimensions of your term-document matrix will be the number of documents <span id="MathJax-Element-1-Frame" class="MathJax" style="box-sizing: border-box; display: inline; font-style: normal; font-weight: normal; line-height: normal; font-size: 14px; text-indent: 0px; text-align: left; text-transform: none; letter-spacing: normal; word-spacing: normal; word-wrap: normal; white-space: nowrap; float: none; direction: ltr; max-width: none; max-height: none; min-width: 0px; min-height: 0px; border: 0px; padding: 0px; margin: 0px; position: relative;" tabindex="0" role="presentation" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><mi>D</mi></math>"><span id="MathJax-Span-1" class="math"><span id="MathJax-Span-2" class="mrow"><span id="MathJax-Span-3" class="mi">D</span></span></span><span class="MJX_Assistive_MathML" role="presentation">D</span></span> (in this case, the number of Shakespeare’s plays that we give you in the corpus by the number of unique word types <span id="MathJax-Element-2-Frame" class="MathJax" style="box-sizing: border-box; display: inline; font-style: normal; font-weight: normal; line-height: normal; font-size: 14px; text-indent: 0px; text-align: left; text-transform: none; letter-spacing: normal; word-spacing: normal; word-wrap: normal; white-space: nowrap; float: none; direction: ltr; max-width: none; max-height: none; min-width: 0px; min-height: 0px; border: 0px; padding: 0px; margin: 0px; position: relative;" tabindex="0" role="presentation" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow><mi>V</mi><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow></math>"><span id="MathJax-Span-4" class="math"><span id="MathJax-Span-5" class="mrow"><span id="MathJax-Span-6" class="texatom"><span id="MathJax-Span-7" class="mrow"><span id="MathJax-Span-8" class="mo">|</span></span></span><span id="MathJax-Span-9" class="mi">V</span><span id="MathJax-Span-10" class="texatom"><span id="MathJax-Span-11" class="mrow"><span id="MathJax-Span-12" class="mo">|</span></span></span></span></span><span class="MJX_Assistive_MathML" role="presentation">|V|</span></span> in that collection. The columns represent the documents, and the rows represent the words, and each cell represents the frequency of that word in that document.

In your code you will write a function to <code class="highlighter-rouge">create_term_document_matrix</code>. This will let you be the hit of your next dinner party by being able to answer trivia questions like <em>how many words did Shakespeare use?</em>, which may give us a hint to the answer to <em>[How many words did Shakespeare know?]</em> The table will also tell you how many words Shakespeare used only once. Did you know that there’s a technical term for that? In corpus linguistics they are called <a href="https://en.wikipedia.org/wiki/Hapax_legomenon"><em>hapax legomena</em></a>, but I prefer the term <em>singleton</em>, because I don’t like snooty Greek or Latin terms.

<h2 id="comparing-plays">Comparing plays</h2>

The term-document matrix will also let us do cool things like figure out which plays are most similar to each other, by comparing the column vectors. We could even look for outliers to see if some plays are so dissimilar from the rest of the canon that <a href="https://en.wikipedia.org/wiki/Shakespeare_authorship_question">maybe they weren’t authored by Shakespeare after all</a>.

Let’s begin by considering the column representing each play. Each column is a <span id="MathJax-Element-3-Frame" class="MathJax" style="box-sizing: border-box; display: inline; font-style: normal; font-weight: normal; line-height: normal; font-size: 14px; text-indent: 0px; text-align: left; text-transform: none; letter-spacing: normal; word-spacing: normal; word-wrap: normal; white-space: nowrap; float: none; direction: ltr; max-width: none; max-height: none; min-width: 0px; min-height: 0px; border: 0px; padding: 0px; margin: 0px; position: relative;" tabindex="0" role="presentation" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow><mi>V</mi><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow></math>"><span id="MathJax-Span-13" class="math"><span id="MathJax-Span-14" class="mrow"><span id="MathJax-Span-15" class="texatom"><span id="MathJax-Span-16" class="mrow"><span id="MathJax-Span-17" class="mo">|</span></span></span><span id="MathJax-Span-18" class="mi">V</span><span id="MathJax-Span-19" class="texatom"><span id="MathJax-Span-20" class="mrow"><span id="MathJax-Span-21" class="mo">|</span></span></span></span></span><span class="MJX_Assistive_MathML" role="presentation">|V|</span></span>-dimensional vector. Let’s use some math to define the similarity of these vectors. By far the most common similarity metric is the cosine of the angle between the vectors. The cosine similarity metric is defined in Section 15.3 of the textbook.

<blockquote>

 The cosine, like most measures for vector similarity used in NLP, is based on the dot product operator from linear algebra, also called the inner product:

</blockquote>

<blockquote>

 dot-product(<span id="MathJax-Element-4-Frame" class="MathJax" style="box-sizing: border-box; display: inline; font-style: normal; font-weight: normal; line-height: normal; font-size: 17.5px; text-indent: 0px; text-align: left; text-transform: none; letter-spacing: normal; word-spacing: normal; word-wrap: normal; white-space: nowrap; float: none; direction: ltr; max-width: none; max-height: none; min-width: 0px; min-height: 0px; border: 0px; padding: 0px; margin: 0px; position: relative;" tabindex="0" role="presentation" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mover><mi>v</mi><mo stretchy=&quot;false&quot;>&amp;#x2192;</mo></mover></mrow><mo>,</mo><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mover><mi>w</mi><mo stretchy=&quot;false&quot;>&amp;#x2192;</mo></mover></mrow><mo stretchy=&quot;false&quot;>)</mo><mo>=</mo><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mover><mi>v</mi><mo stretchy=&quot;false&quot;>&amp;#x2192;</mo></mover></mrow><mo>&amp;#x22C5;</mo><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mover><mi>w</mi><mo stretchy=&quot;false&quot;>&amp;#x2192;</mo></mover></mrow><mo>=</mo><munderover><mo>&amp;#x2211;</mo><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mi>i</mi><mo>=</mo><mn>1</mn></mrow><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mi>N</mi></mrow></munderover><mrow class=&quot;MJX-TeXAtom-ORD&quot;><msub><mi>v</mi><mi>i</mi></msub><msub><mi>w</mi><mi>i</mi></msub></mrow><mo>=</mo><msub><mi>v</mi><mn>1</mn></msub><msub><mi>w</mi><mn>1</mn></msub><mo>+</mo><msub><mi>v</mi><mn>2</mn></msub><msub><mi>w</mi><mn>2</mn></msub><mo>+</mo><mo>&amp;#x2026;</mo><mo>+</mo><msub><mi>v</mi><mi>N</mi></msub><msub><mi>w</mi><mi>N</mi></msub></math>"><span id="MathJax-Span-22" class="math"><span id="MathJax-Span-23" class="mrow"><span id="MathJax-Span-24" class="texatom"><span id="MathJax-Span-25" class="mrow"><span id="MathJax-Span-26" class="munderover"><span id="MathJax-Span-27" class="mi">v</span><span id="MathJax-Span-28" class="mo">⃗ </span></span></span></span><span id="MathJax-Span-29" class="mo">,</span><span id="MathJax-Span-30" class="texatom"><span id="MathJax-Span-31" class="mrow"><span id="MathJax-Span-32" class="munderover"><span id="MathJax-Span-33" class="mi">w</span><span id="MathJax-Span-34" class="mo">⃗ </span></span></span></span><span id="MathJax-Span-35" class="mo">)</span><span id="MathJax-Span-36" class="mo">=</span><span id="MathJax-Span-37" class="texatom"><span id="MathJax-Span-38" class="mrow"><span id="MathJax-Span-39" class="munderover"><span id="MathJax-Span-40" class="mi">v</span><span id="MathJax-Span-41" class="mo">⃗ </span></span></span></span><span id="MathJax-Span-42" class="mo">⋅</span><span id="MathJax-Span-43" class="texatom"><span id="MathJax-Span-44" class="mrow"><span id="MathJax-Span-45" class="munderover"><span id="MathJax-Span-46" class="mi">w</span><span id="MathJax-Span-47" class="mo">⃗ </span></span></span></span><span id="MathJax-Span-48" class="mo">=</span><span id="MathJax-Span-49" class="munderover"><span id="MathJax-Span-50" class="mo">∑</span><span id="MathJax-Span-51" class="texatom"><span id="MathJax-Span-52" class="mrow"><span id="MathJax-Span-53" class="mi">N</span></span></span><span id="MathJax-Span-54" class="texatom"><span id="MathJax-Span-55" class="mrow"><span id="MathJax-Span-56" class="mi">i</span><span id="MathJax-Span-57" class="mo">=</span><span id="MathJax-Span-58" class="mn">1</span></span></span></span><span id="MathJax-Span-59" class="texatom"><span id="MathJax-Span-60" class="mrow"><span id="MathJax-Span-61" class="msubsup"><span id="MathJax-Span-62" class="mi">v</span><span id="MathJax-Span-63" class="mi">i</span></span><span id="MathJax-Span-64" class="msubsup"><span id="MathJax-Span-65" class="mi">w</span><span id="MathJax-Span-66" class="mi">i</span></span></span></span><span id="MathJax-Span-67" class="mo">=</span><span id="MathJax-Span-68" class="msubsup"><span id="MathJax-Span-69" class="mi">v</span><span id="MathJax-Span-70" class="mn">1</span></span><span id="MathJax-Span-71" class="msubsup"><span id="MathJax-Span-72" class="mi">w</span><span id="MathJax-Span-73" class="mn">1</span></span><span id="MathJax-Span-74" class="mo">+</span><span id="MathJax-Span-75" class="msubsup"><span id="MathJax-Span-76" class="mi">v</span><span id="MathJax-Span-77" class="mn">2</span></span><span id="MathJax-Span-78" class="msubsup"><span id="MathJax-Span-79" class="mi">w</span><span id="MathJax-Span-80" class="mn">2</span></span><span id="MathJax-Span-81" class="mo">+</span><span id="MathJax-Span-82" class="mo">…</span><span id="MathJax-Span-83" class="mo">+</span><span id="MathJax-Span-84" class="msubsup"><span id="MathJax-Span-85" class="mi">v</span><span id="MathJax-Span-86" class="mi">N</span></span><span id="MathJax-Span-87" class="msubsup"><span id="MathJax-Span-88" class="mi">w</span><span id="MathJax-Span-89" class="mi">N</span></span></span></span><span class="MJX_Assistive_MathML" role="presentation">v→,w→)=v→⋅w→=∑i=1Nviwi=v1w1+v2w2+…+vNwN</span></span>

</blockquote>

<blockquote>

 The dot product acts as a similarity metric because it will tend to be high just when the two vectors have large values in the same dimensions. Alternatively, vectors that have zeros in different dimensions (orthogonal vectors) will have a dot product of 0, representing their strong dissimilarity.

</blockquote>

<blockquote>

 This raw dot-product, however, has a problem as a similarity metric: it favors long vectors. The vector length is defined as

</blockquote>

<blockquote>

 <span id="MathJax-Element-5-Frame" class="MathJax" style="box-sizing: border-box; display: inline; font-style: normal; font-weight: normal; line-height: normal; font-size: 17.5px; text-indent: 0px; text-align: left; text-transform: none; letter-spacing: normal; word-spacing: normal; word-wrap: normal; white-space: nowrap; float: none; direction: ltr; max-width: none; max-height: none; min-width: 0px; min-height: 0px; border: 0px; padding: 0px; margin: 0px; position: relative;" tabindex="0" role="presentation" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mover><mi>v</mi><mo stretchy=&quot;false&quot;>&amp;#x2192;</mo></mover></mrow><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow><mo>=</mo><msqrt><munderover><mo>&amp;#x2211;</mo><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mi>i</mi><mo>=</mo><mn>1</mn></mrow><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mi>N</mi></mrow></munderover><mrow class=&quot;MJX-TeXAtom-ORD&quot;><msubsup><mi>v</mi><mi>i</mi><mn>2</mn></msubsup></mrow></msqrt></math>"><span id="MathJax-Span-90" class="math"><span id="MathJax-Span-91" class="mrow"><span id="MathJax-Span-92" class="texatom"><span id="MathJax-Span-93" class="mrow"><span id="MathJax-Span-94" class="mo">|</span></span></span><span id="MathJax-Span-95" class="texatom"><span id="MathJax-Span-96" class="mrow"><span id="MathJax-Span-97" class="munderover"><span id="MathJax-Span-98" class="mi">v</span><span id="MathJax-Span-99" class="mo">⃗ </span></span></span></span><span id="MathJax-Span-100" class="texatom"><span id="MathJax-Span-101" class="mrow"><span id="MathJax-Span-102" class="mo">|</span></span></span><span id="MathJax-Span-103" class="mo">=</span><span id="MathJax-Span-104" class="msqrt"><span id="MathJax-Span-105" class="mrow"><span id="MathJax-Span-106" class="munderover"><span id="MathJax-Span-107" class="mo">∑</span><span id="MathJax-Span-108" class="texatom"><span id="MathJax-Span-109" class="mrow"><span id="MathJax-Span-110" class="mi">N</span></span></span><span id="MathJax-Span-111" class="texatom"><span id="MathJax-Span-112" class="mrow"><span id="MathJax-Span-113" class="mi">i</span><span id="MathJax-Span-114" class="mo">=</span><span id="MathJax-Span-115" class="mn">1</span></span></span></span><span id="MathJax-Span-116" class="texatom"><span id="MathJax-Span-117" class="mrow"><span id="MathJax-Span-118" class="msubsup"><span id="MathJax-Span-119" class="mi">v</span><span id="MathJax-Span-120" class="mn">2</span><span id="MathJax-Span-121" class="mi">i</span></span></span></span></span>−−−−−−√</span></span></span><span class="MJX_Assistive_MathML" role="presentation">|v→|=∑i=1Nvi2</span></span>

</blockquote>

<blockquote>

 The dot product is higher if a vector is longer, with higher values in each dimension. More frequent words have longer vectors, since they tend to co-occur with more words and have higher co-occurrence values with each of them. The raw dot product thus will be higher for frequent words. But this is a problem; we would like a similarity metric that tells us how similar two words are regardless of their frequency.

</blockquote>

<blockquote>

 The simplest way to modify the dot product to normalize for the vector length is to divide the dot product by the lengths of each of the two vectors. This normalized dot product turns out to be the same as the cosine of the angle between the two vectors, following from the definition of the dot product between two vectors <span id="MathJax-Element-6-Frame" class="MathJax" style="box-sizing: border-box; display: inline; font-style: normal; font-weight: normal; line-height: normal; font-size: 17.5px; text-indent: 0px; text-align: left; text-transform: none; letter-spacing: normal; word-spacing: normal; word-wrap: normal; white-space: nowrap; float: none; direction: ltr; max-width: none; max-height: none; min-width: 0px; min-height: 0px; border: 0px; padding: 0px; margin: 0px; position: relative;" tabindex="0" role="presentation" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mover><mi>v</mi><mo stretchy=&quot;false&quot;>&amp;#x2192;</mo></mover></mrow></math>"><span id="MathJax-Span-122" class="math"><span id="MathJax-Span-123" class="mrow"><span id="MathJax-Span-124" class="texatom"><span id="MathJax-Span-125" class="mrow"><span id="MathJax-Span-126" class="munderover"><span id="MathJax-Span-127" class="mi">v</span><span id="MathJax-Span-128" class="mo">⃗ </span></span></span></span></span></span><span class="MJX_Assistive_MathML" role="presentation">v→</span></span> and <span id="MathJax-Element-7-Frame" class="MathJax" style="box-sizing: border-box; display: inline; font-style: normal; font-weight: normal; line-height: normal; font-size: 17.5px; text-indent: 0px; text-align: left; text-transform: none; letter-spacing: normal; word-spacing: normal; word-wrap: normal; white-space: nowrap; float: none; direction: ltr; max-width: none; max-height: none; min-width: 0px; min-height: 0px; border: 0px; padding: 0px; margin: 0px; position: relative;" tabindex="0" role="presentation" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mover><mi>w</mi><mo stretchy=&quot;false&quot;>&amp;#x2192;</mo></mover></mrow></math>"><span id="MathJax-Span-129" class="math"><span id="MathJax-Span-130" class="mrow"><span id="MathJax-Span-131" class="texatom"><span id="MathJax-Span-132" class="mrow"><span id="MathJax-Span-133" class="munderover"><span id="MathJax-Span-134" class="mi">w</span><span id="MathJax-Span-135" class="mo">⃗ </span></span></span></span></span></span><span class="MJX_Assistive_MathML" role="presentation">w→</span></span> as:

</blockquote>

<blockquote>

 <span id="MathJax-Element-8-Frame" class="MathJax" style="box-sizing: border-box; display: inline; font-style: normal; font-weight: normal; line-height: normal; font-size: 17.5px; text-indent: 0px; text-align: left; text-transform: none; letter-spacing: normal; word-spacing: normal; word-wrap: normal; white-space: nowrap; float: none; direction: ltr; max-width: none; max-height: none; min-width: 0px; min-height: 0px; border: 0px; padding: 0px; margin: 0px; position: relative;" tabindex="0" role="presentation" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mover><mi>v</mi><mo stretchy=&quot;false&quot;>&amp;#x2192;</mo></mover></mrow><mo>&amp;#x22C5;</mo><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mover><mi>w</mi><mo stretchy=&quot;false&quot;>&amp;#x2192;</mo></mover></mrow><mo>=</mo><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mover><mi>v</mi><mo stretchy=&quot;false&quot;>&amp;#x2192;</mo></mover></mrow><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mover><mi>w</mi><mo stretchy=&quot;false&quot;>&amp;#x2192;</mo></mover></mrow><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow><mi>c</mi><mi>o</mi><mi>s</mi><mi mathvariant=&quot;normal&quot;>&amp;#x0398;</mi></math>"><span id="MathJax-Span-136" class="math"><span id="MathJax-Span-137" class="mrow"><span id="MathJax-Span-138" class="texatom"><span id="MathJax-Span-139" class="mrow"><span id="MathJax-Span-140" class="munderover"><span id="MathJax-Span-141" class="mi">v</span><span id="MathJax-Span-142" class="mo">⃗ </span></span></span></span><span id="MathJax-Span-143" class="mo">⋅</span><span id="MathJax-Span-144" class="texatom"><span id="MathJax-Span-145" class="mrow"><span id="MathJax-Span-146" class="munderover"><span id="MathJax-Span-147" class="mi">w</span><span id="MathJax-Span-148" class="mo">⃗ </span></span></span></span><span id="MathJax-Span-149" class="mo">=</span><span id="MathJax-Span-150" class="texatom"><span id="MathJax-Span-151" class="mrow"><span id="MathJax-Span-152" class="mo">|</span></span></span><span id="MathJax-Span-153" class="texatom"><span id="MathJax-Span-154" class="mrow"><span id="MathJax-Span-155" class="munderover"><span id="MathJax-Span-156" class="mi">v</span><span id="MathJax-Span-157" class="mo">⃗ </span></span></span></span><span id="MathJax-Span-158" class="texatom"><span id="MathJax-Span-159" class="mrow"><span id="MathJax-Span-160" class="mo">|</span></span></span><span id="MathJax-Span-161" class="texatom"><span id="MathJax-Span-162" class="mrow"><span id="MathJax-Span-163" class="mo">|</span></span></span><span id="MathJax-Span-164" class="texatom"><span id="MathJax-Span-165" class="mrow"><span id="MathJax-Span-166" class="munderover"><span id="MathJax-Span-167" class="mi">w</span><span id="MathJax-Span-168" class="mo">⃗ </span></span></span></span><span id="MathJax-Span-169" class="texatom"><span id="MathJax-Span-170" class="mrow"><span id="MathJax-Span-171" class="mo">|</span></span></span><span id="MathJax-Span-172" class="mi">c</span><span id="MathJax-Span-173" class="mi">o</span><span id="MathJax-Span-174" class="mi">s</span><span id="MathJax-Span-175" class="mi">Θ</span></span></span><span class="MJX_Assistive_MathML" role="presentation">v→⋅w→=|v→||w→|cosΘ</span></span>

</blockquote>

<blockquote>

 <span id="MathJax-Element-9-Frame" class="MathJax" style="box-sizing: border-box; display: inline; font-style: normal; font-weight: normal; line-height: normal; font-size: 17.5px; text-indent: 0px; text-align: left; text-transform: none; letter-spacing: normal; word-spacing: normal; word-wrap: normal; white-space: nowrap; float: none; direction: ltr; max-width: none; max-height: none; min-width: 0px; min-height: 0px; border: 0px; padding: 0px; margin: 0px; position: relative;" tabindex="0" role="presentation" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><mfrac><mrow><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mover><mi>v</mi><mo stretchy=&quot;false&quot;>&amp;#x2192;</mo></mover></mrow><mo>&amp;#x22C5;</mo><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mover><mi>w</mi><mo stretchy=&quot;false&quot;>&amp;#x2192;</mo></mover></mrow></mrow><mrow><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mover><mi>v</mi><mo stretchy=&quot;false&quot;>&amp;#x2192;</mo></mover></mrow><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mover><mi>w</mi><mo stretchy=&quot;false&quot;>&amp;#x2192;</mo></mover></mrow><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow></mrow></mfrac><mo>=</mo><mi>c</mi><mi>o</mi><mi>s</mi><mi mathvariant=&quot;normal&quot;>&amp;#x0398;</mi></math>"><span id="MathJax-Span-176" class="math"><span id="MathJax-Span-177" class="mrow"><span id="MathJax-Span-178" class="mfrac"><span id="MathJax-Span-179" class="mrow"><span id="MathJax-Span-180" class="texatom"><span id="MathJax-Span-181" class="mrow"><span id="MathJax-Span-182" class="munderover"><span id="MathJax-Span-183" class="mi">v</span><span id="MathJax-Span-184" class="mo">⃗ </span></span></span></span><span id="MathJax-Span-185" class="mo">⋅</span><span id="MathJax-Span-186" class="texatom"><span id="MathJax-Span-187" class="mrow"><span id="MathJax-Span-188" class="munderover"><span id="MathJax-Span-189" class="mi">w</span><span id="MathJax-Span-190" class="mo">⃗ </span></span></span></span></span><span id="MathJax-Span-191" class="mrow"><span id="MathJax-Span-192" class="texatom"><span id="MathJax-Span-193" class="mrow"><span id="MathJax-Span-194" class="mo">|</span></span></span><span id="MathJax-Span-195" class="texatom"><span id="MathJax-Span-196" class="mrow"><span id="MathJax-Span-197" class="munderover"><span id="MathJax-Span-198" class="mi">v</span><span id="MathJax-Span-199" class="mo">⃗ </span></span></span></span><span id="MathJax-Span-200" class="texatom"><span id="MathJax-Span-201" class="mrow"><span id="MathJax-Span-202" class="mo">|</span></span></span><span id="MathJax-Span-203" class="texatom"><span id="MathJax-Span-204" class="mrow"><span id="MathJax-Span-205" class="mo">|</span></span></span><span id="MathJax-Span-206" class="texatom"><span id="MathJax-Span-207" class="mrow"><span id="MathJax-Span-208" class="munderover"><span id="MathJax-Span-209" class="mi">w</span><span id="MathJax-Span-210" class="mo">⃗ </span></span></span></span><span id="MathJax-Span-211" class="texatom"><span id="MathJax-Span-212" class="mrow"><span id="MathJax-Span-213" class="mo">|</span></span></span></span></span><span id="MathJax-Span-214" class="mo">=</span><span id="MathJax-Span-215" class="mi">c</span><span id="MathJax-Span-216" class="mi">o</span><span id="MathJax-Span-217" class="mi">s</span><span id="MathJax-Span-218" class="mi">Θ</span></span></span><span class="MJX_Assistive_MathML" role="presentation">v→⋅w→|v→||w→|=cosΘ</span></span>

</blockquote>

<blockquote>

 The cosine similarity metric between two vectors <span id="MathJax-Element-10-Frame" class="MathJax" style="box-sizing: border-box; display: inline; font-style: normal; font-weight: normal; line-height: normal; font-size: 17.5px; text-indent: 0px; text-align: left; text-transform: none; letter-spacing: normal; word-spacing: normal; word-wrap: normal; white-space: nowrap; float: none; direction: ltr; max-width: none; max-height: none; min-width: 0px; min-height: 0px; border: 0px; padding: 0px; margin: 0px; position: relative;" tabindex="0" role="presentation" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mover><mi>v</mi><mo stretchy=&quot;false&quot;>&amp;#x2192;</mo></mover></mrow></math>"><span id="MathJax-Span-219" class="math"><span id="MathJax-Span-220" class="mrow"><span id="MathJax-Span-221" class="texatom"><span id="MathJax-Span-222" class="mrow"><span id="MathJax-Span-223" class="munderover"><span id="MathJax-Span-224" class="mi">v</span><span id="MathJax-Span-225" class="mo">⃗ </span></span></span></span></span></span><span class="MJX_Assistive_MathML" role="presentation">v→</span></span> and <span id="MathJax-Element-11-Frame" class="MathJax" style="box-sizing: border-box; display: inline; font-style: normal; font-weight: normal; line-height: normal; font-size: 17.5px; text-indent: 0px; text-align: left; text-transform: none; letter-spacing: normal; word-spacing: normal; word-wrap: normal; white-space: nowrap; float: none; direction: ltr; max-width: none; max-height: none; min-width: 0px; min-height: 0px; border: 0px; padding: 0px; margin: 0px; position: relative;" tabindex="0" role="presentation" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mover><mi>w</mi><mo stretchy=&quot;false&quot;>&amp;#x2192;</mo></mover></mrow></math>"><span id="MathJax-Span-226" class="math"><span id="MathJax-Span-227" class="mrow"><span id="MathJax-Span-228" class="texatom"><span id="MathJax-Span-229" class="mrow"><span id="MathJax-Span-230" class="munderover"><span id="MathJax-Span-231" class="mi">w</span><span id="MathJax-Span-232" class="mo">⃗ </span></span></span></span></span></span><span class="MJX_Assistive_MathML" role="presentation">w→</span></span> thus can be computed

</blockquote>

<blockquote>

 <span id="MathJax-Element-12-Frame" class="MathJax" style="box-sizing: border-box; display: inline; font-style: normal; font-weight: normal; line-height: normal; font-size: 17.5px; text-indent: 0px; text-align: left; text-transform: none; letter-spacing: normal; word-spacing: normal; word-wrap: normal; white-space: nowrap; float: none; direction: ltr; max-width: none; max-height: none; min-width: 0px; min-height: 0px; border: 0px; padding: 0px; margin: 0px; position: relative;" tabindex="0" role="presentation" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><mi>c</mi><mi>o</mi><mi>s</mi><mi>i</mi><mi>n</mi><mi>e</mi><mo stretchy=&quot;false&quot;>(</mo><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mover><mi>v</mi><mo stretchy=&quot;false&quot;>&amp;#x2192;</mo></mover></mrow><mo>,</mo><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mover><mi>w</mi><mo stretchy=&quot;false&quot;>&amp;#x2192;</mo></mover></mrow><mo stretchy=&quot;false&quot;>)</mo><mo>=</mo><mfrac><mrow><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mover><mi>v</mi><mo stretchy=&quot;false&quot;>&amp;#x2192;</mo></mover></mrow><mo>&amp;#x22C5;</mo><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mover><mi>w</mi><mo stretchy=&quot;false&quot;>&amp;#x2192;</mo></mover></mrow></mrow><mrow><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mover><mi>v</mi><mo stretchy=&quot;false&quot;>&amp;#x2192;</mo></mover></mrow><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mover><mi>w</mi><mo stretchy=&quot;false&quot;>&amp;#x2192;</mo></mover></mrow><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow></mrow></mfrac><mo>=</mo><mfrac><mrow><munderover><mo>&amp;#x2211;</mo><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mi>i</mi><mo>=</mo><mn>1</mn></mrow><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mi>N</mi></mrow></munderover><mrow class=&quot;MJX-TeXAtom-ORD&quot;><msub><mi>v</mi><mi>i</mi></msub><msub><mi>w</mi><mi>i</mi></msub></mrow></mrow><mrow><msqrt><munderover><mo>&amp;#x2211;</mo><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mi>i</mi><mo>=</mo><mn>1</mn></mrow><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mi>N</mi></mrow></munderover><mrow class=&quot;MJX-TeXAtom-ORD&quot;><msubsup><mi>v</mi><mi>i</mi><mn>2</mn></msubsup></mrow></msqrt><msqrt><munderover><mo>&amp;#x2211;</mo><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mi>i</mi><mo>=</mo><mn>1</mn></mrow><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mi>N</mi></mrow></munderover><mrow class=&quot;MJX-TeXAtom-ORD&quot;><msubsup><mi>w</mi><mi>i</mi><mn>2</mn></msubsup></mrow></msqrt></mrow></mfrac></math>"><span id="MathJax-Span-233" class="math"><span id="MathJax-Span-234" class="mrow"><span id="MathJax-Span-235" class="mi">c</span><span id="MathJax-Span-236" class="mi">o</span><span id="MathJax-Span-237" class="mi">s</span><span id="MathJax-Span-238" class="mi">i</span><span id="MathJax-Span-239" class="mi">n</span><span id="MathJax-Span-240" class="mi">e</span><span id="MathJax-Span-241" class="mo">(</span><span id="MathJax-Span-242" class="texatom"><span id="MathJax-Span-243" class="mrow"><span id="MathJax-Span-244" class="munderover"><span id="MathJax-Span-245" class="mi">v</span><span id="MathJax-Span-246" class="mo">⃗ </span></span></span></span><span id="MathJax-Span-247" class="mo">,</span><span id="MathJax-Span-248" class="texatom"><span id="MathJax-Span-249" class="mrow"><span id="MathJax-Span-250" class="munderover"><span id="MathJax-Span-251" class="mi">w</span><span id="MathJax-Span-252" class="mo">⃗ </span></span></span></span><span id="MathJax-Span-253" class="mo">)</span><span id="MathJax-Span-254" class="mo">=</span><span id="MathJax-Span-255" class="mfrac"><span id="MathJax-Span-256" class="mrow"><span id="MathJax-Span-257" class="texatom"><span id="MathJax-Span-258" class="mrow"><span id="MathJax-Span-259" class="munderover"><span id="MathJax-Span-260" class="mi">v</span><span id="MathJax-Span-261" class="mo">⃗ </span></span></span></span><span id="MathJax-Span-262" class="mo">⋅</span><span id="MathJax-Span-263" class="texatom"><span id="MathJax-Span-264" class="mrow"><span id="MathJax-Span-265" class="munderover"><span id="MathJax-Span-266" class="mi">w</span><span id="MathJax-Span-267" class="mo">⃗ </span></span></span></span></span><span id="MathJax-Span-268" class="mrow"><span id="MathJax-Span-269" class="texatom"><span id="MathJax-Span-270" class="mrow"><span id="MathJax-Span-271" class="mo">|</span></span></span><span id="MathJax-Span-272" class="texatom"><span id="MathJax-Span-273" class="mrow"><span id="MathJax-Span-274" class="munderover"><span id="MathJax-Span-275" class="mi">v</span><span id="MathJax-Span-276" class="mo">⃗ </span></span></span></span><span id="MathJax-Span-277" class="texatom"><span id="MathJax-Span-278" class="mrow"><span id="MathJax-Span-279" class="mo">|</span></span></span><span id="MathJax-Span-280" class="texatom"><span id="MathJax-Span-281" class="mrow"><span id="MathJax-Span-282" class="mo">|</span></span></span><span id="MathJax-Span-283" class="texatom"><span id="MathJax-Span-284" class="mrow"><span id="MathJax-Span-285" class="munderover"><span id="MathJax-Span-286" class="mi">w</span><span id="MathJax-Span-287" class="mo">⃗ </span></span></span></span><span id="MathJax-Span-288" class="texatom"><span id="MathJax-Span-289" class="mrow"><span id="MathJax-Span-290" class="mo">|</span></span></span></span></span><span id="MathJax-Span-291" class="mo">=</span><span id="MathJax-Span-292" class="mfrac"><span id="MathJax-Span-293" class="mrow"><span id="MathJax-Span-294" class="munderover"><span id="MathJax-Span-295" class="mo">∑</span><span id="MathJax-Span-296" class="texatom"><span id="MathJax-Span-297" class="mrow"><span id="MathJax-Span-298" class="mi">N</span></span></span><span id="MathJax-Span-299" class="texatom"><span id="MathJax-Span-300" class="mrow"><span id="MathJax-Span-301" class="mi">i</span><span id="MathJax-Span-302" class="mo">=</span><span id="MathJax-Span-303" class="mn">1</span></span></span></span><span id="MathJax-Span-304" class="texatom"><span id="MathJax-Span-305" class="mrow"><span id="MathJax-Span-306" class="msubsup"><span id="MathJax-Span-307" class="mi">v</span><span id="MathJax-Span-308" class="mi">i</span></span><span id="MathJax-Span-309" class="msubsup"><span id="MathJax-Span-310" class="mi">w</span><span id="MathJax-Span-311" class="mi">i</span></span></span></span></span><span id="MathJax-Span-312" class="mrow"><span id="MathJax-Span-313" class="msqrt"><span id="MathJax-Span-314" class="mrow"><span id="MathJax-Span-315" class="munderover"><span id="MathJax-Span-316" class="mo">∑</span><span id="MathJax-Span-317" class="texatom"><span id="MathJax-Span-318" class="mrow"><span id="MathJax-Span-319" class="mi">N</span></span></span><span id="MathJax-Span-320" class="texatom"><span id="MathJax-Span-321" class="mrow"><span id="MathJax-Span-322" class="mi">i</span><span id="MathJax-Span-323" class="mo">=</span><span id="MathJax-Span-324" class="mn">1</span></span></span></span><span id="MathJax-Span-325" class="texatom"><span id="MathJax-Span-326" class="mrow"><span id="MathJax-Span-327" class="msubsup"><span id="MathJax-Span-328" class="mi">v</span><span id="MathJax-Span-329" class="mn">2</span><span id="MathJax-Span-330" class="mi">i</span></span></span></span></span>√</span><span id="MathJax-Span-331" class="msqrt"><span id="MathJax-Span-332" class="mrow"><span id="MathJax-Span-333" class="munderover"><span id="MathJax-Span-334" class="mo">∑</span><span id="MathJax-Span-335" class="texatom"><span id="MathJax-Span-336" class="mrow"><span id="MathJax-Span-337" class="mi">N</span></span></span><span id="MathJax-Span-338" class="texatom"><span id="MathJax-Span-339" class="mrow"><span id="MathJax-Span-340" class="mi">i</span><span id="MathJax-Span-341" class="mo">=</span><span id="MathJax-Span-342" class="mn">1</span></span></span></span><span id="MathJax-Span-343" class="texatom"><span id="MathJax-Span-344" class="mrow"><span id="MathJax-Span-345" class="msubsup"><span id="MathJax-Span-346" class="mi">w</span><span id="MathJax-Span-347" class="mn">2</span><span id="MathJax-Span-348" class="mi">i</span></span></span></span></span>√</span></span></span></span></span><span class="MJX_Assistive_MathML" role="presentation">cosine(v→,w→)=v→⋅w→|v→||w→|=∑i=1Nviwi∑i=1Nvi2∑i=1Nwi2</span></span>

</blockquote>

The cosine value ranges from 1 for vectors pointing in the same direction, through 0 for vectors that are orthogonal, to -1 for vectors pointing in opposite directions. Since our term-document matrix contains raw frequency counts, it is non-negative, so the cosine for its vectors will range from 0 to 1. 1 means that the vectors are identical, 0 means that they are totally dissimilar.

Please implement <code class="highlighter-rouge">compute_cosine_similarity</code>, and for each play in the corpus, score how similar each other play is to it. Which plays are the closet to each other in vector space (ignoring self similarity)? Which plays are the most distant from each other?

<h2 id="how-do-i-know-if-my-rankings-are-good">How do I know if my rankings are good?</h2>

First, read all of the plays. Then perform at least three of them. Now that you are a true thespian, you should have a good intuition for the central themes in the plays. Alternately, take a look at <a href="https://en.wikipedia.org/wiki/Shakespeare%27s_plays#Canonical_plays">this grouping of Shakespeare’s plays into Tragedies, Comedies and Histories</a>. Do plays that are thematically similar to the one that you’re ranking appear among its most similar plays, according to cosine similarity? Another clue that you’re doing the right thing is if a play has a cosine of 1 with itself. If that’s not the case, then you’ve messed something up. Another good hint, is that there are a ton of plays about Henry. They’ll probably be similar to each other.

<h1 id="measuring-word-similarity">Measuring word similarity</h1>

Next, we’re going to see how we can represent words as vectors in vector space. This will give us a way of representing some aspects of the <em>meaning</em> of words, by measuring the similarity of their vectors.

In our term-document matrix, the rows are word vectors. Instead of a <span id="MathJax-Element-13-Frame" class="MathJax" style="box-sizing: border-box; display: inline; font-style: normal; font-weight: normal; line-height: normal; font-size: 14px; text-indent: 0px; text-align: left; text-transform: none; letter-spacing: normal; word-spacing: normal; word-wrap: normal; white-space: nowrap; float: none; direction: ltr; max-width: none; max-height: none; min-width: 0px; min-height: 0px; border: 0px; padding: 0px; margin: 0px; position: relative;" tabindex="0" role="presentation" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow><mi>V</mi><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow></math>"><span id="MathJax-Span-349" class="math"><span id="MathJax-Span-350" class="mrow"><span id="MathJax-Span-351" class="texatom"><span id="MathJax-Span-352" class="mrow"><span id="MathJax-Span-353" class="mo">|</span></span></span><span id="MathJax-Span-354" class="mi">V</span><span id="MathJax-Span-355" class="texatom"><span id="MathJax-Span-356" class="mrow"><span id="MathJax-Span-357" class="mo">|</span></span></span></span></span><span class="MJX_Assistive_MathML" role="presentation">|V|</span></span>-dimensional vector, these row vectors only have <span id="MathJax-Element-14-Frame" class="MathJax" style="box-sizing: border-box; display: inline; font-style: normal; font-weight: normal; line-height: normal; font-size: 14px; text-indent: 0px; text-align: left; text-transform: none; letter-spacing: normal; word-spacing: normal; word-wrap: normal; white-space: nowrap; float: none; direction: ltr; max-width: none; max-height: none; min-width: 0px; min-height: 0px; border: 0px; padding: 0px; margin: 0px; position: relative;" tabindex="0" role="presentation" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><mi>D</mi></math>"><span id="MathJax-Span-358" class="math"><span id="MathJax-Span-359" class="mrow"><span id="MathJax-Span-360" class="mi">D</span></span></span><span class="MJX_Assistive_MathML" role="presentation">D</span></span> dimensions. Do you think that’s enough to represent the meaning of words? Try it out. In the same way that you computed the similarity of the plays, you can compute the similarity of the words in the matrix. Pick some words and compute 10 words with the highest cosine similarity between their row vector representations. Are those 10 words good synonyms?

<h2 id="term-context-matrix">Term-Context Matrix</h2>

Instead of using a term-document matrix, a more common way of computing word similarity is by constructing a term-context matrix (also called a word-word matrix), where columns are labeled by words rather than documents. The dimensionality of this kind of a matrix is <span id="MathJax-Element-15-Frame" class="MathJax" style="box-sizing: border-box; display: inline; font-style: normal; font-weight: normal; line-height: normal; font-size: 14px; text-indent: 0px; text-align: left; text-transform: none; letter-spacing: normal; word-spacing: normal; word-wrap: normal; white-space: nowrap; float: none; direction: ltr; max-width: none; max-height: none; min-width: 0px; min-height: 0px; border: 0px; padding: 0px; margin: 0px; position: relative;" tabindex="0" role="presentation" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow><mi>V</mi><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow></math>"><span id="MathJax-Span-361" class="math"><span id="MathJax-Span-362" class="mrow"><span id="MathJax-Span-363" class="texatom"><span id="MathJax-Span-364" class="mrow"><span id="MathJax-Span-365" class="mo">|</span></span></span><span id="MathJax-Span-366" class="mi">V</span><span id="MathJax-Span-367" class="texatom"><span id="MathJax-Span-368" class="mrow"><span id="MathJax-Span-369" class="mo">|</span></span></span></span></span><span class="MJX_Assistive_MathML" role="presentation">|V|</span></span> by <span id="MathJax-Element-16-Frame" class="MathJax" style="box-sizing: border-box; display: inline; font-style: normal; font-weight: normal; line-height: normal; font-size: 14px; text-indent: 0px; text-align: left; text-transform: none; letter-spacing: normal; word-spacing: normal; word-wrap: normal; white-space: nowrap; float: none; direction: ltr; max-width: none; max-height: none; min-width: 0px; min-height: 0px; border: 0px; padding: 0px; margin: 0px; position: relative;" tabindex="0" role="presentation" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow><mi>V</mi><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow></math>"><span id="MathJax-Span-370" class="math"><span id="MathJax-Span-371" class="mrow"><span id="MathJax-Span-372" class="texatom"><span id="MathJax-Span-373" class="mrow"><span id="MathJax-Span-374" class="mo">|</span></span></span><span id="MathJax-Span-375" class="mi">V</span><span id="MathJax-Span-376" class="texatom"><span id="MathJax-Span-377" class="mrow"><span id="MathJax-Span-378" class="mo">|</span></span></span></span></span><span class="MJX_Assistive_MathML" role="presentation">|V|</span></span>. Each cell represents how often the word in the row (the target word) co-occurs with the word in the column (the context) in a training corpus.

For this part of the assignment, you should write the <code class="highlighter-rouge">create_term_context_matrix</code> function. This function specifies the size word window around the target word that you will use to gather its contexts. For instance, if you set that variable to be 4, then you will use 4 words to the left of the target word, and 4 words to its right for the context. In this case, the cell represents the number of times in Shakespeare’s plays the column word occurs in +/-4 word window around the row word.

You can now re-compute the most similar words for your test words using the row vectors in your term-context matrix instead of your term-document matrix. What is the dimensionality of your word vectors now? Do the most similar words make more sense than before?

<h1 id="weighting-terms">Weighting terms</h1>

Your term-context matrix contains the raw frequency of the co-occurrence of two words in each cell. Raw frequency turns out not to be the best way of measuring the association between words. There are several methods for weighting words so that we get better results. You should implement two weighting schemes:

<ul>

 <li>Positive pointwise mutual information (PPMI)</li>

 <li>Term frequency inverse document frequency (tf-idf)</li>

</ul>

These are defined in Section 15.2 of the textbook.

<em>Warning, calculating PPMI for your whole <span id="MathJax-Element-17-Frame" class="MathJax" style="box-sizing: border-box; display: inline; font-style: normal; font-weight: normal; line-height: normal; font-size: 14px; text-indent: 0px; text-align: left; text-transform: none; letter-spacing: normal; word-spacing: normal; word-wrap: normal; white-space: nowrap; float: none; direction: ltr; max-width: none; max-height: none; min-width: 0px; min-height: 0px; border: 0px; padding: 0px; margin: 0px; position: relative;" tabindex="0" role="presentation" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow><mi>V</mi><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow></math>"><span id="MathJax-Span-379" class="math"><span id="MathJax-Span-380" class="mrow"><span id="MathJax-Span-381" class="texatom"><span id="MathJax-Span-382" class="mrow"><span id="MathJax-Span-383" class="mo">|</span></span></span><span id="MathJax-Span-384" class="mi">V</span><span id="MathJax-Span-385" class="texatom"><span id="MathJax-Span-386" class="mrow"><span id="MathJax-Span-387" class="mo">|</span></span></span></span></span><span class="MJX_Assistive_MathML" role="presentation">|V|</span></span>-by-<span id="MathJax-Element-18-Frame" class="MathJax" style="box-sizing: border-box; display: inline; font-style: normal; font-weight: normal; line-height: normal; font-size: 14px; text-indent: 0px; text-align: left; text-transform: none; letter-spacing: normal; word-spacing: normal; word-wrap: normal; white-space: nowrap; float: none; direction: ltr; max-width: none; max-height: none; min-width: 0px; min-height: 0px; border: 0px; padding: 0px; margin: 0px; position: relative;" tabindex="0" role="presentation" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow><mi>V</mi><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo stretchy=&quot;false&quot;>|</mo></mrow></math>"><span id="MathJax-Span-388" class="math"><span id="MathJax-Span-389" class="mrow"><span id="MathJax-Span-390" class="texatom"><span id="MathJax-Span-391" class="mrow"><span id="MathJax-Span-392" class="mo">|</span></span></span><span id="MathJax-Span-393" class="mi">V</span><span id="MathJax-Span-394" class="texatom"><span id="MathJax-Span-395" class="mrow"><span id="MathJax-Span-396" class="mo">|</span></span></span></span></span><span class="MJX_Assistive_MathML" role="presentation">|V|</span></span> matrix might be slow. Our intrepid TA’s implementation for PPMI takes about 10 minutes to compute all values. She always writes perfectly optimized code on her first try. You may improve performance by using matrix operations a la MATLAB.</em>

<h1 id="weighting-terms-1">Weighting terms</h1>

There are several ways of computing the similarity between two vectors. In addition to writing a function to compute cosine similarity, you should also write functions to <code class="highlighter-rouge">compute_jaccard_similarity</code> and <code class="highlighter-rouge">compute_dice_similarity</code>. Check out section 15.3.1. of the textbook for the defintions of the Jaccard and Dice measures.

<h1 id="your-tasks">Your Tasks</h1>

All of the following are function stubs in the python code. You just need to fill them out.

Create matrices:

<ul>

 <li>fill out <code class="highlighter-rouge">create_term_document_matrix</code></li>

 <li>fill out <code class="highlighter-rouge">create_term_context_matrix</code></li>

 <li>fill out <code class="highlighter-rouge">create_PPMI_matrix</code></li>

 <li>fill out <code class="highlighter-rouge">compute_tf_idf_matrix</code></li>

</ul>

Compute similarities:

<ul>

 <li>fill out <code class="highlighter-rouge">compute_cosine_similarity</code></li>

 <li>fill out <code class="highlighter-rouge">compute_jaccard_similarity</code></li>

 <li>fill out <code class="highlighter-rouge">compute_dice_similarity</code></li>

</ul>

Do some ranking:

<ul>

 <li>fill out <code class="highlighter-rouge">rank_plays</code></li>

 <li>fill out <code class="highlighter-rouge">rank_words</code></li>

</ul>

<h1 id="report">Report</h1>

In the ranking tasks, play with different vector representations and different similarity functions. Does one combination appear to work better than another? Do any interesting patterns emerge? Include this discussion in your writeup.

Some patterns you could touch upon:

<ul>

 <li>The fourth column of <code class="highlighter-rouge">will_play_text.csv</code> contains the name of the character who spoke each line. Using the methods described above, which characters are most similar? Least similar?</li>

 <li>Shakespeare’s plays are traditionally classified into <a href="https://en.wikipedia.org/wiki/Shakespeare%27s_plays">comedies, histories, and tragedies</a>. Can you use these vector representations to cluster the plays?</li>

 <li>Do the vector representations of <a href="https://en.wikipedia.org/wiki/Category:Female_Shakespearean_characters">female characters</a> differ distinguishably from <a href="https://en.wikipedia.org/wiki/Category:Male_Shakespearean_characters">male ones</a>?</li>

</ul>

<h1 id="extra-credit">Extra credit</h1>

Quantifying the goodness of one vector space representation over another can be very difficult to do. It might ultimately require testing how the different vector representations change the performance when used in a downstream task like question answering. A common way of quantifying the goodness of word vectors is to use them to compare the similarity of words with human similarity judgments, and then calculate the correlation of the two rankings.

If you would like extra credit on this assignment, you can quantify the goodness of each of the different vector space models that you produced (for instance by varying the size of the context window, picking PPMI or tf-idf, and selecting among cosine, Jaccard, and Dice). You can calculate their scores on the <a href="https://www.cl.cam.ac.uk/~fh295/simlex.html">SimLex999 data set</a>, and compute their correlation with human judgments using <a href="https://en.wikipedia.org/wiki/Kendall_rank_correlation_coefficient">Kendall’s Tau</a>.

Add a section to your writeup explaining what experiments you ran, and which setting had the highest correlation with human judgments.

<h1 id="more-optional-fun-extra-credit-options">More Optional Fun Extra Credit options</h1>

So you’ve built some machinery that can measure similarity between words and documents. We gave you a Shakespeare corpus, but you can use any body of text you like. For example, check out <a href="https://www.gutenberg.org/">Project Gutenberg</a> for public domain texts. The sky’s the limit on what you can do, but here are some ideas:

<ul>

 <li><em>Term-Character Matrix</em>. Our data set.</li>

 <li><em>Novel recommender system</em>. Maybe you enjoyed reading <em>Sense and Sensibility</em> and <em>War and Peace</em>. Can you suggest some similar novels? Or maybe you need some variety in your consumption. Find novels that are really different.</li>

 <li><em>Other languages</em>. Do these techniques work in other languages? Project Gutenberg has texts in a variety of languages. Maybe you could use this to measure language similarity?</li>

 <li><em>Modernizing Shakespeare</em>. When I read Shakespeare in high school, I had the dickens of a time trying to understand all the weird words in the play. Some people have re-written Shakespeare’s plays into contemporary English. An <a href="https://cocoxu.github.io/">awesome NLP researcher</a> has <a href="https://github.com/cocoxu/Shakespeare">compiled that data</a>. Use her data and your vector space models to find contemporary words that mean similar things to the Shakespearean English.</li>

</ul>

<h2 id="deliverables">Deliverables</h2>

<h2 id="recommended-readings">Recommended readings</h2>

<table>

 <tbody>

  <tr>

   <td><a href="https://web.stanford.edu/~jurafsky/slp3/15.pdf">Vector Semantics.</a> Dan Jurafsky and James H. Martin. Speech and Language Processing (3rd edition draft) .</td>

  </tr>

  <tr>

   <td><a href="https://www.jair.org/media/2934/live-2934-4846-jair.pdf">From Frequency to Meaning: Vector Space Models of Semantics.</a> Peter D. Turney and Patrick Pantel. Journal of Artificial Intelligence Research 2010.<a class="label label-success" href="http://computational-linguistics-class.org/assignment3.html#from-frequency-to-meaning-abstract" data-toggle="modal">Abstract</a> <a class="label label-default" href="http://computational-linguistics-class.org/assignment3.html#from-frequency-to-meaning-bibtex" data-toggle="modal">BibTex</a></td>

  </tr>

  <tr>

   <td><a href="http://www.aclweb.org/anthology/C12-1177">Paraphrasing for Style.</a> Wei Xu, Alan Ritter, Bill Dolan, Ralph Grisman, and Colin Cherry. Coling 2012. <a class="label label-success" href="http://computational-linguistics-class.org/assignment3.html#paraphrasing-for-style-abstract" data-toggle="modal">Abstract</a> <a class="label label-default" href="http://computational-linguistics-class.org/assignment3.html#paraphrasing-for-style-bibtex" data-toggle="modal">BibTex</a></td>

  </tr>

  <tr>

   <td><a href="http://www.aclweb.org/anthology/D15-1036">Evaluation methods for unsupervised word embeddings.</a> Tobias Schnabel, Igor Labutov, David Mimno, Thorsten Joachims. EMNLP 2015. <a class="label label-success" href="http://computational-linguistics-class.org/assignment3.html#evaluation-methods-for-word-embeddings-abstract" data-toggle="modal">Abstract</a><a class="label label-default" href="http://computational-linguistics-class.org/assignment3.html#evaluation-methods-for-word-embeddings-bibtex" data-toggle="modal">BibTex</a></td>

  </tr>

  <tr>

   <td><a href="http://www.aclweb.org/anthology/P14-5004">Community Evaluation and Exchange of Word Vectors at wordvectors.org.</a> Manaal Faruqui and Chris Dyer. ACL demos 2014. <a class="label label-success" href="http://computational-linguistics-class.org/assignment3.html#wordvectors-abstract" data-toggle="modal">Abstract</a> <a class="label label-default" href="http://computational-linguistics-class.org/assignment3.html#wordvectors-bibtex" data-toggle="modal">BibTex</a></td>

  </tr>

 </tbody>

</table>

5/5 - (3 votes)

Here are the materials that you should download for this assignment:

<ul>

 <li><a href="http://computational-linguistics-class.org/downloads/hw3/main.py">Skeleton python code</a></li>

 <li><a href="http://computational-linguistics-class.org/downloads/hw3/will_play_text.csv">Data – csv of the complete works of Shakespeare</a></li>

 <li><a href="http://computational-linguistics-class.org/downloads/hw3/vocab.txt">Data – vocab the complete works of Shakespeare</a></li>

 <li><a href="http://computational-linguistics-class.org/downloads/hw3/play_names.txt">Data – list of all plays in dataset</a></li>

</ul>

Here are the deliverables that you will need to submit:

<ul>

 <li>writeup.pdf</li>

 <li>code (.zip). It should be written in Python 3.</li>