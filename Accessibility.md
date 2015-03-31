# Accessibility #

According to the CHI2014 website, in the future it is expected that papers _must_ be accessible: http://chi2014.acm.org/authors/guide-to-an-accessible-submission  The currently suggested approaches to making a PDF accessible rely on Microsoft Word + Adobe Acrobat Pro, both being proprietary software.  This page is thus aimed at discussing ideas on how to achieve this goal using just LaTeX.  In the following I comment what has worked for me (Linux Ubuntu 13.10, TeX Live 2013); though it should work under any modern LaTeX installation, too.

# Details #

B. Schalitz wrote an `accessibility` package in 2007 (http://www.babs.gmxhome.de/da_ergeb.htm) that can be downloaded at http://www.babs.gmxhome.de/download/da_pdftex/accessibility.sty  This package is not definite but might get the job done. So far, it meets the 5 steps indicated at the CHI2014 website.

1. [Add alternative text to all figures](http://chi2014.acm.org/authors/add-alternative-text-to-all-figures)

The `accessibility` package provides the `\alt{}` command to do so. Usage example:
```
\begin{figure}
  \alt{A description of the sample.img file below.}
  \includegraphics{sample.img}
  \alt{A description of the sample.pic file below.}
  \includegraphics{sample.pic}
  \caption{The figure caption}
  \label{fig:sample}  
\end{figure}
```

This command can be used in equations, too. Example:
```
\begin{equation}
  \alt{Definition of Euclidean distance in n-dimensional space.} 
  d(p,q) = \sqrt{\sum_{i=1}^n (p_i - q_i)^2}
\end{equation}
```

2. [Mark table headers](http://chi2014.acm.org/authors/mark-table-headers)

The `accessibility` package automatically tags all table cells, and provides the `\thead{}` command to mark table headers. Usage example:
```
\begin{table}
  \begin{tabular}{cc}
    \hline
    \thead{Key} & \thead{Value} \\
    \hline
    A & foo \\
    B & bar \\
    \hline
  \end{tabular}
  \caption{Table caption}
  \label{tbl:sample}
\end{table}
```

3. [Generate a tagged PDF](http://chi2014.acm.org/authors/generate-a-tagged-pdf)

This guideline should have been provided in the first place. Just load the `accessibility` package with the `tagged` option:
```
\usepackage[tagged]{accessibility}
```
Be sure that this package is loaded at the very last, as it rewrites many LaTeX macros.

4. [Verify the default language](http://chi2014.acm.org/authors/verify-the-default-language)

The default language can be set with the `hyperref` package:
```
\usepackage[pdflang={en-US}]{hyperref}
```

Note: Acrobat Reader always shows the `Document properties -> Advanced -> Language` field blank while Acrobat Pro correctly displays it. See https://groups.google.com/forum/#!topic/comp.text.tex/hE8rWpYjW1U

5. [Set the tab order of all pages to "Use Document Structure"](http://chi2014.acm.org/authors/set-the-tab-order)

According to WCAG 2.0 "the reading order of a PDF document is the tag order of document elements". Therefore, in principle it is automatically handled by the `accessibility` package. Nevertheless it may be the case that the order has to be corrected, for which I'm not yet aware of an open source tool to do so.

# Issues #

The `accessibility` package seems to be a good approach to making an accessible PDF with the current `sigchi.cls` template. However, I found a number of inconsistencies:
  * You must set `\pagenumbering{arabic}` and then set `\pagestyle{empty}` right after `\begin{document}`. Otherwise `pdflatex` will not compile the document.
  * `\subparagraph` is defined in `sigchi.cls` but not in `accessibility.sty`. This throws a compiler warning.
  * `\theindex` and `\@idxitem` are defined in `accessibility.sty` but not in `sigchi.cls`. This also throws a compiler warning.
  * When setting a subfloat caption (using `\subfloat[caption text]{}` from the `subfig` package), floating elements are typesetted as block elements. A workaround for this is using a tabular environment instead, but it is not very elegant. Perhaps there is a captioning package elsewhere that does not clash with `accessibility.sty`.
  * The `tabular` environment is augmented with an extra row at the bottom. You can save vertical space either using `\renewcommand{\arraystretch}{0.5}` or putting a negative vspace (e.g. `\vspace{-1em}`) after `\end{tabular}`.

You might want to suppress warnings while compiling by using the `interaction=nonstopmode` flag in `pdflatex`:
```
pdflatex -interaction=nonstopmode paper.tex
```

On another line, `accessibility.sty` sets `\pdfcompresslevel=0` so the resulting PDF is uncompressed. Thus, in order to produce a smaller PDF file, just put this in your document preamble, before `\begin{document}`:
```
\pdfcompresslevel=9
\pdfobjcompresslevel=3
```


# Ideas #

  * Update the accessibility package and host it at CTAN, so that everyone can download a common version.
  * Rewrite the `sigchi.cls` template so that it can support these accessibility guidelines "natively" (plus, the template is a bit outdated so it is an opportunity to make it simpler).
  * Use the LaTeX built-in `article` class and use a custom STY file instead with all required stuff built-in.
  * Do nothing while compiling the PDF and post-process it using open source tools, if available.
  * More?

# Updates #

Andy Clifton has performed some changes to the original `accessibility` package. It seems that some issues has been fixed, though I didn't test his reworked `accessibility_meta` package yet. Suggestion: [Download the modified accessibility package](https://github.com/AndyClifton/AccessibleMetaClass/blob/master/accessibility_meta.sty) and load it on the preamble as indicated before:
```
\usepackage[tagged]{accessibility_meta}
```

# End Notes #

Some useful readings:
  * http://www.babs.gmxhome.de/download/da_pdftex/dok_pdf.pdf (in German)
  * http://www.babs.gmxhome.de/download/da_aa/aa_pdf.pdf (in German)
  * http://www.tug.org/TUGboat/Articles/tb23-1/moore.pdf
  * http://www.tug.org/TUGboat/tb30-2/tb95moore.pdf
  * http://www.w3.org/WAI/GL/WCAG20-TECHS/pdf.html
  * http://alistapart.com/article/pdf_accessibility

The extended abstracts template will be discussed soon.