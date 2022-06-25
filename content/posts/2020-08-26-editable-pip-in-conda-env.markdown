---
categories:
  - computational-chemistry
  - troubleshooting
comments: true
date: "2020-08-26T11:18:00Z"
subtitle: When your pip development environment can not recognize the Conda environment
tags: 
  - conda
  - open-babel
  - pip
  - python-environment
title: Installing Open Babel with Pip
---

So yesterday I spent at least 3 hours to deal with Python package development in Conda environment. So I wrote this post as a self-reminder, since the steps are somewhat long. And also for those of you who is facing the same problem as I do.

### Why do I need to use pip when I can use Conda to install Open Babel

If you're an experienced Python package developer you should have known the following trick:

`pip install -e .`

For those of you who do not know, this command is for installing your package in editable mode, which means every change you made to your package will immediately reflected to your environment without having to reinstall your package. Amazing huh?

This trick is very useful when I was developing [my package][hippos] and I was satisfied, until... I made the change to setup.py in order to stage my package to conda-forge so my package can be installed using conda. And then I just figured it out when there is some minor bug in my package recently, and debugging would be much easier if I could see the result every time I patch the code.

But alas, for some reason `pip install -e .` just doesn't work anymore. Well, the first reason is one of my package dependency (Open Babel) can only be installed with Conda or package manager (apt, yum, etc.), and since I added package dependency to `setup.py` pip will try to install Open Babel which doesn't work. The second reason was, when I run `pip install -e . --no-deps` with the assumption that my package will use Open Babel from Conda environment, but that doesn't work because [package installed with pip in editable mode can not recognize the conda environment][pip-conda-issue].

My first approach is by googling the workaround for pip editable mode in Conda environment, but the answers I found were too complicated and require me to create new Conda environment everytime I want to use the development environment via `pip install -e .` . And then the other alternative is using `conda develop` command, which is an alternative for `pip install -e .`. But too bad that `conda develop` has been abandoned and can not serve its purpose since what I really need is the [entrypoint][entrypoint] so that every time I update the code I can check my package by typing `hippos` or `hippos-genref` command.

### setup.py come to rescue

And then finally, I use my last resort. That is... by installing Open Babel to my pip environment (inside my Conda environment) from the source. First of all I follow [the build instruction for Open Babel][open-babel-install]. After making sure the requirement satisfied (especially SWIG for SWIG binding)

```bash
tar -zxf openbabel-openbabel-3-1-1.tar.gz
cd openbabel-openbabel-3-1-1
mkdir ob-build
cd $_
cmake -DRUN_SWIG=ON -DPYTHON_BINDINGS=ON -DCMAKE_INSTALL_PREFIX=../openbabel-install ..
make -j4 install
```

And then using the [Open Babel pip source file][open-babel-pip] to install Open Babel Python binding to my pip environment with the following command:

```bash
tar -zxf openbabel-3.1.1.1.tar.gz
cd openbabel-3.1.1.1
python setup.py build_ext -I ~/radifar.pro/openbabel/openbabel-openbabel-3-1-1/openbabel-install/include/openbabel3/ -L ~/radifar.pro/openbabel/openbabel-openbabel-3-1-1/openbabel-install/lib/
python setup.py install
```

Note here that the -I and -L option is to specify the Open Babel Include and Library directory where you install your Open Babel.

Then check if Open Babel is installed in pip environment inside Conda environment using `conda env export`:

```
...
  - xorg-xproto=7.0.31=h14c3975_1007
  - xz=5.2.5=h516909a_1
  - zlib=1.2.11=h516909a_1006
  - pip:
    - attrs==19.3.0
    - coverage==5.2.1
    - importlib-metadata==1.7.0
    - more-itertools==8.4.0
    - openbabel==3.1.0
    - packaging==20.4
    - pluggy==0.13.1
    - py==1.9.0
    - pyparsing==2.4.7
    - pytest==5.4.3
    - pytest-cov==2.10.0
    - six==1.15.0
    - wcwidth==0.2.5
    - zipp==3.1.0
```

Then check if Open Babel can be imported from my Conda environment:

```
Python 3.7.8 | packaged by conda-forge | (default, Jul 23 2020, 03:54:19) 
[GCC 7.5.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> from openbabel import openbabel
>>> 
```

Great! Now Open Babel is running perfectly under pip in Conda environment. OK, so that's all folks. Hope this post can help solve your problem. And if you have any question feel free to ask in the comment section below.

[hippos]: https://github.com/radifar/PyPLIF-HIPPOS/
[pip-conda-issue]: https://github.com/conda/conda/issues/5861
[entrypoint]: https://dev.to/demianbrecht/entry-points-in-python-34i3
[open-babel-install]: https://dev.to/demianbrecht/entry-points-in-python-34i3
[open-babel-pip]: https://pypi.org/project/openbabel/#files