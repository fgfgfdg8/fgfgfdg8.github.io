---
# the default layout is 'page'
icon: fas fa-info-circle
order: 4
---

<!-- > Add Markdown syntax content to file `_tabs/about.md`{: .filepath } and it will show up on this page.
{: .prompt-tip } -->

# 关于我
我是**吴丰艺**，现在是[东南大学](https://www.seu.edu.cn)[网络空间安全学院](https://cyber.seu.edu.cn/)的一名网络空间安全专业硕士研究生，导师是[**陈浩**](https://haochen.org/)教授。  
我先前于[大连海事大学](https://www.dlmu.edu.cn/)[船舶电气工程学院](https://cbdq.dlmu.edu.cn/)物联网工程专业获得工学学士学位。

# 研究方向
现在我的主要研究方向致力于将模糊测试（Fuzzing）与LLM Agent结合，以挖掘实际代码仓库中的软件漏洞。
- Fuzzing
- LLM Agent
- LLM Agent for Fuzzing
- Automatic Crash Analysis

# Publications
> To be continued

# Trophy
> To be continued


# 参考学习资料 (from Prof. Chen)
## How to Read a Paper
- [How to Read a Paper](https://svr-sk818-web.cl.cam.ac.uk/keshav/papers/07/paper-reading.pdf)

## Technical writing
- [The Mayfield Handbook of Technical & Scientific Writing](https://www.mit.edu/course/21/21.guide/toc.htm)
- [Style: Lessons in Clarity and Grace](https://en.wikipedia.org/wiki/Style:_Lessons_in_Clarity_and_Grace)
- Presentations: Chapter 5 of [Beamer User Guide](https://tug.ctan.org/macros/latex/contrib/beamer/doc/beameruserguide.pdf) 

## Tools
- [LaTeX](https://en.wikibooks.org/wiki/LaTeX)
- [Git](https://git-scm.com/book/en/v2)
- [Pro Git中文版](https://www.progit.cn/)
- [Visual Studio Code](https://code.visualstudio.com/docs/)
- [Ubuntu](https://help.ubuntu.com/)

## Languages
- [Bash](https://en.wikipedia.org/wiki/Bash_(Unix_shell))
- [Python](https://docs.python.org/3/tutorial/index.html)
- [Rust](https://doc.rust-lang.org/book/)

## Textbooks
- [Introduction to Algorithms (Fourth Edition)](https://mitpress.mit.edu/9780262046305/introduction-to-algorithms/) [(Download)](https://dl.ebooksworld.ir/books/Introduction.to.Algorithms.4th.Leiserson.Stein.Rivest.Cormen.MIT.Press.9780262046305.EBooksWorld.ir.pdf)
- [Operating Systems: Principles and Practice](https://ospp.cs.washington.edu/) [(Download)](https://www.kea.nu/files/textbooks/ospp/)
- [The Elements of Statistical Learning: Data Mining, Inference, and Prediction (Second Edition)](https://hastie.su.domains/ElemStatLearn/)
- [Computer Security](https://textbook.cs161.org/)

## Advice on LaTeX (from Prof. Chen)

- Use modern implementations of LaTeX to take advantage of Unicode and other useful features.
    - Use [LuaLaTeX](https://www.luatex.org/) instead of LaTeX or pdfLaTeX
    - Use [BibLaTeX](https://www.overleaf.com/learn/latex/Bibliography_management_with_biblatex) instead of BibTex
- Use correct [typefaces](https://physics.nist.gov/cuu/pdf/typefaces.pdf). Particularly, *italics* should be used for variables but not for descriptive terms. [More...](https://en.wikibooks.org/wiki/LaTeX/Mathematics#Adding_text_to_equations)
    - Wrong: $t_{max}$
    - Right: $t_\text{max}$
- Do not manually add separators in large numbers. `\usepackage{siunitx}`. Then, wrap large numbers in `\num{}`.
    - Bad: 12,345
    - Good: `\num{12345}`
- Do not manually type reference names, such as Table, Figure, Theorem. Instead, `\usepackage{hyperref}`, and then `\autoref{fig:xxx}`, `\autoref{tbl:xxx}`, `\autoref{thm:xxx}`
    - Not recommended: In Figure~\ref{fig:overview}
    - Good: In \autoref{fig:overview}

## Advice on writing (from Prof. Chen)

### Contents
- Your paper should be easily comprehensible by its reviewers. They are far less familiar with your work than you. They may not be an expert on the topic and may not be able to afford much time on your paper.
- The introduction should convey curiosity or excitement (new problem, new solution, improved solution, impressive results, or high impact), the design novelty, substantiality, and correctness, and the evaluation relevancy and comprehensiveness.
- Conciseness: Remove every word that contributes no meaning, such as `kind of`.
- Use proper tenses: [1](https://www.unlv.edu/sites/default/files/page_files/27/GradCollege-VerbTenseScientificManuscripts.pdf) [2](https://berks.psu.edu/sites/berks/files/campus/VerbTense_Handout.pdf) [3](https://www.nature.com/scitable/topicpage/effective-writing-13815989/)

### Grammar
- Section titles should have consistent [capitalization](https://www.grammarly.com/blog/title-case-sentence-case/). I prefer sentence case.
- Avoid passive voice unless strongly justifiable. Passive voice is ambiguous because it has no subject unless followed by "by...".
    - Bad: LLM was applied to fuzzing. (Who applied it? The authors or someone else?)
    - Good: We applied LLM to fuzzing.
- Avoid [nominalization](https://en.wikipedia.org/wiki/Nominalization).
    - Bad: He made a proposal to use Rust.
    - Good: He proposed to use Rust.
- Avoid "There is/are".
    - Bad: There are many developers of Rust.
    - Good: Many developers use Rust.
    - Good: Rust has many developers.
- "Which" vs "that": Use "which" in a nonrestrictive clause and "that" in a restrictive clause. [More ...](https://www.grammarly.com/blog/which-vs-that/)
    - Wrong: Rust that is safe is popular. (This is wrong because there is only one Rust.)
    - Right: Rust, which is safe, is popular.
- Distinguish [coordinating conjunction vs conjunctive adverbs](https://www.iup.edu/writingcenter/writing-resources/grammar/common-problems-with-however-therefore-and-similar-words.html).
    - Wrong: C is dangerous, Rust is safe. (Cannot join two sentences by a comma)
    - Right: C is dangerous, but Rust is safe.
    - Wrong: C is dangerous, however Rust is safe.
    - Right: C is dangerous; however, Rust is safe.
- "Fewer" modifies countable nouns whereas "less" uncountable nouns.
    - Wrong: ten items or less
    - Right: ten items or fewer
    - Wrong: fewer feedback
    - Right: less feedback
- Use articles (`a`, `an`, `the`) properly.
    - A singular countable noun must be preceded by an article.
        - Wrong: I wrote Rust program.
        - Right: I wrote a Rust program.
        - Right: I wrote Rust programs.
    - `The` must have a reference that is unique either by fact or in the context.
        - Right: the first Rust programmer (unique by fact)
        - Right: Our team has a Rust and a C++ programmer. The Rust programmer produces the fastest, most robust code. (unique in the context)
        - Wrong: Our team has two Rust and two C++ programmers. The Rust programer is more productive than the C++ programer.
- Distinguish between [compare with and compare to](https://www.noslangues-ourlanguages.gc.ca/en/writing-tips-plus/compare-to-compare-with)
    - Right: Rust is safer compared with C.
    - Right: Some people compare Rust to a panacea for memory safety problems.

# 友情链接
[Yunlongs](https://yunlongs.cn/)  
[spinpx](https://spinpx.com/)