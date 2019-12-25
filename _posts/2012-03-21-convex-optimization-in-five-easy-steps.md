---
title: "Convex optimization in five easy steps"
date: "2012-03-21"
---

1\. Install [NumPy](http://numpy.scipy.org/ "NumPy")

sudo apt-get install python-numpy

2\. Install [SciPy](http://www.scipy.org "SciPy")

sudo apt-get install python-scipy

3\. Install [CVXOPT](http://abel.ee.ucla.edu/cvxopt/ "CVXOPT")

sudo apt-get install python-cvxopt

4\. Install [OpenOpt](http://openopt.org/Welcome "OpenOpt")

wget http://openopt.org/images/3/33/OpenOpt.zip
unzip OpenOpt.zip
cd OpenOpt
sudo python setup.py install --prefix=/usr/local/

5\. Run your second-order conic optimization program ([source](http://trac.openopt.org/openopt/browser/PythonPackages/OpenOpt/openopt/examples/socp_1.py "source"))

[![](http://umayrh.files.wordpress.com/2012/03/socp.png?w=300 "SOCP")](http://umayrh.files.wordpress.com/2012/03/socp.png)

\[sourcecode language="python"\] """ OpenOpt SOCP example for the problem http://openopt.org/images/2/28/SOCP.png """

from numpy import \* from openopt import SOCP

f = array(\[-2, 1, 5\]) C0 = mat('-13 3 5; -12 12 -6') d0 = \[-3, -2\] q0 = array(\[-12, -6, 5\]) s0 = -12

C1 = mat('-3 6 2; 1 9 2; -1 -19 3') d1 = \[0, 3, -42\] q1 = array(\[-3, 6, -10\]) s1 = 27

\# you could add lb <= x <= ub, Ax <= b, Aeq x = beq constraints # via p = SOCP(f, ..., A=A, b=b, Aeq=Aeq, beq=beq,lb=lb, ub=ub) p = SOCP(f,  C=\[C0, C1\],  d=\[d0, d1\], q=\[q0, q1\], s=\[s0, s1\]) r = p.solve('cvxopt\_socp')

x\_opt, f\_opt = r.xf,  r.ff

\# f\_opt: -38.346368 x\_opt: \[-5.01428121 -5.76680444 -8.52162517\] print(' f\_opt: %f    x\_opt: %s' % (f\_opt, x\_opt)) \[/sourcecode\]

python socp.py

------------------------- OpenOpt 0.38 -------------------------
solver: cvxopt\_socp   problem: unnamed    type: SOCP   goal: minimum
     pcost       dcost       gap    pres   dres   k/t
 0:  4.9969e+00 -1.7285e+01  6e+01  3e-01  4e+00  1e+00
 1: -1.6732e+00 -7.0431e+00  1e+01  7e-02  1e+00  6e-01
 2: -1.6221e+01 -3.5417e+01  2e+02  3e-01  5e+00  7e+00
 3: -2.1832e+01 -2.2849e+01  3e+01  4e-02  6e-01  2e+00
 4: -3.5265e+01 -3.5594e+01  1e+01  1e-02  2e-01  9e-01
 5: -3.8303e+01 -3.8314e+01  3e-01  4e-04  6e-03  2e-02
 6: -3.8342e+01 -3.8342e+01  1e-02  1e-05  2e-04  7e-04
 7: -3.8346e+01 -3.8346e+01  9e-04  1e-06  2e-05  7e-05
 8: -3.8346e+01 -3.8346e+01  4e-05  6e-08  9e-07  4e-06
 9: -3.8346e+01 -3.8346e+01  2e-06  3e-09  4e-08  2e-07
10: -3.8346e+01 -3.8346e+01  3e-07  4e-10  6e-09  3e-08
Optimal solution found.
istop: 1000 (optimal)
Solver:   Time Elapsed = 0.01     CPU Time Elapsed = 0.01
objFunValue: -38.346368
 f\_opt: -38.346368    x\_opt: \[-5.01469912 -5.76690749 -8.52177183\]
