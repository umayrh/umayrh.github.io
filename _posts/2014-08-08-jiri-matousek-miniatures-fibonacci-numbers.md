---
title: "Fibonacci Numbers"
date: "2014-08-08"
---

Two notes from [Jiřì Matoušek](http://kam.mff.cuni.cz/~matousek/)'s book _Thirty-three Miniatures: Mathematical and Algorithmic Applications of Linear Algebra_ \[[1](http://www.ams.org/bookstore?fn=20&arg1=stmlseries&ikey=STML-53),[2](http://kam.mff.cuni.cz/~matousek/la-ams.html)\].

**Fibonacci numbers in O(lg_n_) steps**

1.1 Matrix formulation for recursive calculation

\[latex\]\\left(\\begin{array}{c}F\_{n+2}\\\\ F\_{n+1}\\end{array}\\right) = M\\left(\\begin{array}{c}F\_{n+1}\\\\ F\_{n}\\end{array}\\right)\\\\for\\ M\\ =\\left(\\begin{array}{cc}1 & 1 \\\\ 1 & 0\\end{array}\\right)\\\\ \\therefore \\left(\\begin{array}{c}F\_{n+1}\\\\ F\_{n}\\end{array}\\right) = M^{n}\\left(\\begin{array}{c}1\\\\ 0\\end{array}\\right)\[/latex\]

1.2 At most log2n multiplications needed size z <= log2n

\[latex\]n = 2^a + 2^b +\\ ...+ 2^z\\ for\\ a < b <\\ ...,\\ M^n=M^{2^a}M^{2^b}...M^{2^z}\[/latex\]

This can be extended to any recursive formula.

**Fibonacci formula**

2.1 Given the formula

\[latex\]F\_{n+2} = F\_{n+1} + F\_{n}\[/latex\]

2.2 If we start with the ansatz for either of the two sequences, un and vn, that compose Fn

\[latex\]u\_n = \\tau^{n} \\\\ \\therefore \\tau^{n+2} = \\tau^{n+1} + \\tau^{n} \\\\ \\Rightarrow \\tau^{2} = \\tau + 1\[/latex\]

2.3 This yields two distinct roots

\[latex\]\\tau = (1 \\pm \\sqrt{5}) / 2\[/latex\]

2.4 The two roots individually form two sequences, **u** and **v**, that are linearly independent. Thus Fibonacci numbers can be written in terms of these basis vectors.

\[latex\]\\mathbf{F}=\\alpha \\mathbf{u}+\\beta \\mathbf{v}\[/latex\]

2.5 The values of α and β can be evaluation by solving the linear systems, and eventually

\[latex\]F\_n = \\frac{1}{\\sqrt{5}}\\left\\{(\\frac{1+\\sqrt{5}}{2})^{n}-(\\frac{1-\\sqrt{5}}{2})^n\\right\\}\[/latex\]
