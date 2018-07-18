<!--
.. title: How to run SMS-tools on Python 3 on Mac
.. slug: how-to-run-sms-tools-on-python-3-on-mac
.. date: 2015-01-04 18:20:09+01:00
.. tags: audio,software
.. category: audio
.. link: 
.. description: 
.. type: text
-->


{%img right http://i.bohumirzamecnik.cz/2015-01-04-how-to-run-sms-tools-on-python-3-on-mac/sms_tools_cover_t.png %}

[SMS-tools](https://github.com/MTG/sms-tools) (Spectral modeling synthesis) is a Python education library for audio signal analysis and modeling. It serves for practical excercises within the [Audio Signal Processing for Music Applications](https://www.coursera.org/course/audio) course at Coursera. It was written to run Ubuntu Linux and Python 2.7. Here we see how to run it on latest Python 3.4 on Mac OS X Yosemite.

<!-- TEASER_END -->

## Overview

What are SMS-tools good for? In short they provide models for music analysis - how to turn a music recording into a spectrogram and then a each frame into a sum of sinusoids, sum of harmonic sinusoids, sum of harmonic sinusoids and random noise, etc. Also it contains some very cool transformations including sound morphing. Besides the library of models and transformations there is some GUI that allows you to quickly experimentat with various parameters and data. Just open a wav file, set the params, look at the graphs and play the reconstructed audio. Also there's a lot of short example scripts.

If you look at the [Audio Signal Processing for Music Applications](https://www.coursera.org/course/audio) course at Coursera you can see the theory, practical details and more. I liked to attend the first session of this course which was held in Autumn/Winter 2014. Since I found some free time after the course ended I'm going to take it now and wait for the next session this year to get the certificate. Anyway the software is quite interesting for my own experiments.

In the README you can find the recommended environment is Python 2.7 and Ubuntu Linux. I don't use none of it and don't want to install a lot of GB just to run some little piece of Python code.

There must be a way to run it on Python 3.4 and Mac. Fortunately there is! It consists of two steps: install all the dependencies, port the code to Python 3. The good news is that the step 2 is already done for you. So in this article I'll just summarize both.

## Install dependencies

As for the package manager I use macports. For homebrew it should be quite similar (or try to [consult the fora](https://class.coursera.org/audio-001/forum/thread?thread_id=126#post-535)).

First we'll assume that you already have Python 3 installed (otherwise you wouldn't find this tutorial). If not `sudo port install python34` (or the latest).

It is recommended to keep Python packages for different purposes in virtual environments (or at least in one) and not to install into the system location to prevent conflicts. Python 3.4 contains out-of-the-box a nice tool called [pyvenv](https://docs.python.org/3/library/venv.html) which can solve exactly this problem. So we'll assume you have one virtual env and it is activated. Otherwise:

```
pyvenv my-audio-env
source my-audio-env/bin/activate
```

SMS-tools requires several Python packages and other dependencies. Some of them are available via pip, some of them via macports. Tkinter, SDL, XQuartz (eg. XQuartz-2.7.7.dmg) and Pygame.

```
sudo port install libsdl-framework libsdl_ttf-framework libsdl_image-framework libsdl_mixer-framework mercurial
pip install cython numpy scipy matplotlib ipython readline
pip install hg+http://bitbucket.org/pygame/pygame
```

Note that installing PyGame via a pip package doesn't work now and as alternative we install from a Mercurial repo.

XQuartz is an implementation of X11 UI server. It can be installed from a DMG from [xquartz.macosforge.org](http://xquartz.macosforge.org/). You should log out and log in again for some settings to take effect.

SMS-tools also use Tkinter GUI toolkit with is a bit tricky to install. I've describe its installation in a separate article [How to Install Tkinter With Python 3 on Mac](/blog/2014/install-tkinter-with-python-3-on-mac/). In summary you need to install py34-tkinter and make a symlink since the module in on the wrong path.

```
sudo port install py34-tkinter
```

## Install SMS-tools

Clone the fork SMS-tools and checkout the branch with Python 3 port:

```
git clone https://github.com/bzamecnik/sms-tools.git
git checkout -b python3
```

## Run

Make sure you have activated you pyvenv.

There are two GUIs, one for models, the other for transformations. You can run the GUIs like this. Yes, it really expects you to be inside that directory...

```
sms-tools $ cd software/models_interface
models_interface $ python models_GUI.py
models_interface $ cd ../transformations_interface
transformations_interface $ python transformations_GUI.py
```

{%img http://i.bohumirzamecnik.cz/2015-01-04-how-to-run-sms-tools-on-python-3-on-mac/sms_tools_gui_hps.png %}

## How was porting to Python 3?

First I converted the source code with `2to3`:

```
sms-tools $ 2to3 -w .
```

Then it was necessary to fix some more subtle problems.

The / operator now represents floating-point division, not integer one.
So to avoid indexing with floats it was sometimes necessary to convert the result to integer via `int()`.

Tkinter stuff needs to be imported in a little bit different way. This should work both in Python 2 and 3.

```
try:
	from tkinter import *
	from tkinter import messagebox, filedialog
except ImportError:
	from Tkinter import *
	import tkFileDialog as filedialog, tkMessageBox as messagebox
```

instead of


```
from Tkinter import *
import tkFileDialog, tkMessageBox
```

And that's about it. The result should be compatible with Python 3.4. Since I don't have Python 2.7 I can't test if it still work. Probably not. But anyway, Python 2 is history.

Thanks for reading and have fun with audio processing with SMS-tools.

Here are some nice screenshots:

DFT spectrum of a single frame:
{%img http://i.bohumirzamecnik.cz/2015-01-04-how-to-run-sms-tools-on-python-3-on-mac/01_dft_piano.png %}

STFT spectrogram:
{%img http://i.bohumirzamecnik.cz/2015-01-04-how-to-run-sms-tools-on-python-3-on-mac/02_stft_piano.png %}

Analysis with the sine model:
{%img http://i.bohumirzamecnik.cz/2015-01-04-how-to-run-sms-tools-on-python-3-on-mac/03_sine_model_analysis_bendir.png %}

Harmonic sines model:
{%img http://i.bohumirzamecnik.cz/2015-01-04-how-to-run-sms-tools-on-python-3-on-mac/04_harmonic_model_analysis_vignesh.png %}

Stochastic model:
{%img http://i.bohumirzamecnik.cz/2015-01-04-how-to-run-sms-tools-on-python-3-on-mac/05_stochastic_model_ocean.png %}

Sine plus residuals model:
{%img http://i.bohumirzamecnik.cz/2015-01-04-how-to-run-sms-tools-on-python-3-on-mac/06_spr_model_bendir.png %}

Stocharstic plus residuals model:
{%img http://i.bohumirzamecnik.cz/2015-01-04-how-to-run-sms-tools-on-python-3-on-mac/07_sps_model_bendir.png %}

Harmonic sines plus residuals model:
{%img http://i.bohumirzamecnik.cz/2015-01-04-how-to-run-sms-tools-on-python-3-on-mac/08_hpr_model_sax_phrase_short.png %}

Harmonic sines plus stochastic model:
{%img http://i.bohumirzamecnik.cz/2015-01-04-how-to-run-sms-tools-on-python-3-on-mac/08_hps_model_sax_phrase_short.png %}