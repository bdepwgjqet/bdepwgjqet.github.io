ML tools

- python tools (windows) : http://www.lfd.uci.edu/~gohlke/pythonlibs/ 

- numpy : http://www.numpy.org/ 

- SciPy

- scikit-learn : http://scikit-learn.org/stable/index.html 

- matplotlib

Supervised learning

- Generalized Linear Modules

 - Ordinary Least Squares (最小二乘)

  - min(sum(squares(Xw-y))), w=inverse(X)Y

  - sklearn.linear_model.LinearRegression()

 - Ridge Regression (岭回归)
 
  - min(sum(squares(Xw-y)+squares(Rw)))

  - w=inverse(transport(X)*X+transport(R)*R)*transport(X)Y

