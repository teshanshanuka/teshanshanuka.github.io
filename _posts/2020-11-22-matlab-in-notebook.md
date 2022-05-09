---
layout: post
title:  "Matlab in Jupyter notebook"
date:   2020-11-22 01:01:01 +0530
categories: matlab jupyter notebookpython
---

*I wanted to do matlab in Jupyter notebook because their live scripts suck*

---

# Overview

Matlab is one of the worst languages ever despite of having many invaluable features [[1]][1] [[2]][2].

Previously I have had used Matlab about 5 years ago and recently needed it again. MathWorks has tried to incorporate a 
Jupyter notebook like feature called live scripts into Matlab. While Jupyter notebook being really useful for 
interactive coding, I had a hard time adjusting to the Matlab live scripts. So here’s how I setup Matlab with Jupyter 
notebook (After having Matlab and [Jupyter notebook][3] installed obviously)

# Setting up the Matlab kernel

1. Go to you Matlab installation directory/extern/engines/python

   `cd /usr/local/MATLAB/R2020b/extern/engines/python`

2. Run the `setup.py` (You might need sudo depending on the python installation you have)

   `python3 setup.py install`

3. Install the matlab kernel (Might need sudo)

   `python3 -m pip install matlab-kernelCollecting matlab-kernel`

These are the basic steps but [here’s a more detailed guide][4] from MathWorks if needed. Now you can start jupyter 
notebook as usual and you’ll see a Matlab option in open new notebook drop down.

<img src="/assets/images/2020-11-22-01.png" alt="drop down"/>

# Matlab notebooks

You can straight away start writing Matlab code in the Matlab notebook just like you have used it with python. Here’s 
a few tips I figured out while using the notebook


## Plotting

Inline plots are displayed by default just like in python matplotlib plotting. However, if you need to change the plot 
properties, you’ll have to use some Jupyter magic commands like this.

1. Popup plot

   `%plot native`

2. Switch back to inline plot

   `%plot inline`

3. Plot size
   
   `%plot --size 600,200 (width, height)`

<img src="/assets/images/2020-11-22-02.png" alt="plot"/>

## Matlab toolboxes and simulink

They work just like how they work in the Matlab software. i.e. Type simulink and shift+enter -> you’ll get the 
**simulink** GUI.

## Python code in Matlab notebooks

You can have python (or any other language which you have support for Jupyter notebook) code cells inside the Matlab 
notebook. Just use the magic `%%python` at the start of the cell.

<img src="/assets/images/2020-11-22-03.png" alt="py in matlab notebook"/>


# Matlab cells in Python notebooks

This is not as straightforward as having python cells in a Matlab notebook. Here’s how you do it [[source]][5]]

1. First you have to register IPython magics
   
   `import metakernel; metakernel.register_ipython_magics()`

2. Then start the Matlab kernel

   `%kernel matlab_kernel.kernel MatlabKernel`

Now you can have Matlab code in cells starting with the magic `%%kx`

<img src="/assets/images/2020-11-22-04.png" alt="matlab in py notebook"/>

# Further reading

[This course][6] has several tutorials with examples on Matlab in Jupyter.

[Here is][7] the Matlab kernel git.

Please feel free to comment any other useful features I have missed. Cheers!

[1]: https://neuroplausible.com/matlab
[2]: https://www.rath.org/matlab-is-a-terrible-programming-language.html
[3]: https://jupyter.org/install
[4]: https://www.mathworks.com/help/matlab/matlab_external/install-the-matlab-engine-for-python.html
[5]: https://github.com/Calysto/matlab_kernel/issues/60
[6]: https://am111.readthedocs.io/en/latest/index.html
[7]: https://github.com/Calysto/matlab_kernel
