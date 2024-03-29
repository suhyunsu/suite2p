# suite2p <img src="suite2p/logo/logo_unshaded.png" width="250" title="sweet two pea" alt="sweet two pea" align="right" vspace = "50">

Pipeline for processing two-photon calcium imaging data.  
Copyright (C) 2018  Howard Hughes Medical Institute Janelia Research Campus  

suite2p includes the following modules:

* Registration
* Cell detection
* Spike detection
* Visualization GUI

There will be a workshop at Janelia on suite2p and kilosort2 Aug 19-23, see details [here](https://www.janelia.org/you-janelia/conferences/learning-to-use-suite2p-and-kilosort2). Room and meals will be provided.

This code was written by Carsen Stringer and Marius Pachitariu.  
For support, please open an [issue](https://github.com/MouseLand/suite2p/issues).
The reference paper is [here](https://www.biorxiv.org/content/early/2017/07/20/061507).  
The deconvolution algorithm is based on [this paper](https://journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.1005423), with settings based on [this paper](http://www.jneurosci.org/content/early/2018/08/06/JNEUROSCI.3339-17.2018).

**More in depth documentation is available on the [wiki](https://github.com/MouseLand/suite2p/wiki).**

See this **twitter [thread]** for GUI demonstrations.

The matlab version is available [here](https://github.com/cortex-lab/Suite2P). Note that the algorithm is older and will not work as well on non-circular ROIs.

## Installation

Install an [Anaconda](https://www.anaconda.com/download/) distribution of Python -- Choose **Python 3.x** and your operating system. Note you might need to use an anaconda prompt if you did not add anaconda to the path.

1. Download the `environment.yml` file from the repository
2. Open an anaconda prompt / command prompt with `conda` for **python 3** in the path
3. Run `conda env create -f environment.yml`
4. To activate this new environment, run `conda activate suite2p`
5. You should see `(suite2p)` on the left side of the terminal line. Now run `python -m suite2p` and you're all set.

If you have an older `suite2p` environment you can remove it with `conda env remove -n suite2p` before creating a new one.

Note you will always have to run **conda activate suite2p** before you run suite2p. Conda ensures mkl_fft and numba run correctly and quickly on your machine. If you want to run jupyter notebooks in this environment, then also `conda install jupyter`.

To upgrade suite2p (package [here](https://pypi.org/project/suite2p/)), run the following in the environment:
~~~~
pip install suite2p --upgrade
~~~~

**Common issues**

If when running `python -m suite2p`, you receive the error: `No module named PyQt5.sip`, then try uninstalling and reinstalling pyqt5
~~~~
pip uninstall pyqt5 pyqt5-tools
pip install pyqt5 pyqt5-tools pyqt5.sip
~~~~

If when running `python -m suite2p`, you receive an error associated with **matplotlib**, try upgrading it:
~~~~
pip install matplotlib --upgrade
~~~~

If you are on Yosemite Mac OS, PyQt doesn't work, and you won't be able to install suite2p. More recent versions of Mac OS are fine.

The software has been heavily tested on Windows 10 and Ubuntu 18.04, and less well tested on Mac OS. Please post an issue if you have installation problems. The registration step runs faster on Ubuntu than Windows, so if you have a choice we recommend using the Ubuntu OS.

(+ there's more info on the [install wiki](https://github.com/MouseLand/suite2p/wiki/Installation))

## Examples

An example dataset is provided [here](https://drive.google.com/open?id=1PCJy265NHRWYXUz7CRhbJHtd6B-zJs8f). It's a single-plane, single-channel recording.

## Getting started

The quickest way to start is to open the GUI from a command line terminal. You might need to open an anaconda prompt if you did not add anaconda to the path. Make sure to run this from a directory in which you have **WRITE** access (suite2p saves a couple temporary files in your current directory):
~~~~
python -m suite2p
~~~~
Then:
1. File -> Run suite2p (or ctrl+r)
2. Setup a configuration
    - -> Add directory which contains tiffs to data_path (can be multiple folders, but add them one at a time)
    - -> OR choose an h5 file which has a key with the data, data shape should be time x pixels x pixels (you can type in the key name for the data after you choose the file)
    - -> Add save_path ((otherwise the data directory is used as save path))
    - -> Add fast_disk (this is where the binary file of registered data will be created, choose an SSD for this path) ((otherwise the save path is used as the fast disk path))
    - Set some parameters (see full list below). At the minimum:
		~~~~
		nplanes, nchannels, tau, fs
		~~~~
3. Press run and wait. Messages should start appearing in the embedded command line.
4. When the run is finished, the results will open in the GUI window and there you can visualize and refine the results (see below).

For more information on input file formatting, see this wiki [page](https://github.com/MouseLand/suite2p/wiki/Input-format-and-supported-file-types).

For a description of all the settings and their defaults, see this wiki [page](https://github.com/MouseLand/suite2p/wiki/Settings-(ops.npy)). Also, you can mouse over the settings in the run window to see a short description of each of them.

### Using the GUI

![multiselect](gui_images/multiselect.gif)

suite2p output goes to a folder called "suite2p" inside your save_path, which by default is the same as the data_path. If you ran suite2p in the GUI, it loads the results automatically. Otherwise, load the results with File -> Load results.

The GUI serves two main functions:

1. Checking the quality of the data and results.
	* there are currently several views such as the enhanced mean image, the ROI masks, the correlation map, the correlation among cells, and the ROI+neuropil traces
	* by selecting multiple cells (with "Draw selection" or ctrl+left-click), you can view the activity of multiple ROIs simultaneously in the lower plot
	* there are also population-level visualizations, such as [rastermap](https://github.com/MouseLand/rastermap)
2. Classify ROIs into cell / not cell (left and right views respectively)
	* the default classifier included should work well in a variety of scenarios.
	* a user-classifier can be learnt from manual curation, thus adapting to the statistics of your own data.
	* the GUI automatically saves which ROIs are good in "iscell.npy". The second column contains the probability that the ROI is a cell based on the currently loaded classifier.

Main GUI controls (works in all views):

1. Pan  = Left-Click  + drag  
2. Zoom = (Scroll wheel) OR (Right-Click + drag)
3. Full view = Double left-click OR escape key
4. Swap cell = Right-click on the cell
5. Select multiple cells = (Ctrl + left-click) AND/OR ("Draw selection" button)

You can add your manual curation to a pre-built classifier by clicking "Add current data to classifier". Or you can make a brand-new classifier from a list of "iscell.npy" files that you've manually curated. The default classifier in the GUI is initialized as the suite2p classifier, but you can overwrite it by adding to it, or loading a different classifier and saving it as the default. The default classifier is used in the pipeline to produce the initial "iscell.npy" file.

There is more information on using the GUI on the [wiki](https://github.com/MouseLand/suite2p/wiki/Using-the-GUI)

## Other ways to call Suite2p

1. From the command line:
~~~~
python -m suite2p --ops <path to ops.npy> --db <path to db.npy>
~~~~

2. From Python/Jupyter
~~~~python
from suite2p.run_s2p import run_s2p
ops1 = run_s2p(ops, db)
~~~~

See our example jupyter notebook [here](jupyter/run_pipeline_tiffs_or_batch.ipynb). It also explains how to batch-run suite2p.

## Outputs

~~~~
F.npy: array of fluorescence traces (ROIs by timepoints)  
Fneu.npy: array of neuropil fluorescence traces (ROIs by timepoints)  
spks.npy: array of deconvolved traces (ROIs by timepoints)  
stat.npy: array of statistics computed for each cell (ROIs by 1)  
ops.npy: options and intermediate outputs
iscell.npy: specifies whether an ROI is a cell, first column is 0/1, and second column is probability that the ROI is a cell based on the default classifier
~~~~

See the [output wiki page](https://github.com/MouseLand/suite2p/wiki/Outputs) for more info.

## Option defaults

~~~~python
 ops = {
        # file paths
        'look_one_level_down': False, # whether to look in all subfolders when searching for tiffs
        'fast_disk': [], # used to store temporary binary file, defaults to save_path0
        'delete_bin': False, # whether to delete binary file after processing
        'mesoscan': False, # for reading in scanimage mesoscope files
        'h5py': [], # take h5py as input (deactivates data_path)
        'h5py_key': 'data', #key in h5py where data array is stored
        'save_path0': [], # stores results, defaults to first item in data_path
        'subfolders': [],
        # main settings
        'nplanes' : 1, # each tiff has these many planes in sequence
        'nchannels' : 1, # each tiff has these many channels per plane
        'functional_chan' : 1, # this channel is used to extract functional ROIs (1-based)
        'tau':  1., # this is the main parameter for deconvolution
        'fs': 10.,  # sampling rate (PER PLANE - e.g. if you have 12 planes then this should be around 2.5)
        'force_sktiff': False, # whether or not to use scikit-image for tiff reading
        # output settings
        'preclassify': 0.5, # apply classifier before signal extraction with probability 0.5 (turn off with value 0)
        'save_mat': False, # whether to save output as matlab files
        'combined': True, # combine multiple planes into a single result /single canvas for GUI
        'aspect': 1.0, # um/pixels in X / um/pixels in Y (for correct aspect ratio in GUI)
        # bidirectional phase offset
        'do_bidiphase': False,
        'bidiphase': 0,
        # registration settings
        'do_registration': 1, # whether to register data (2 forces re-registration)
        'keep_movie_raw': False,
        'nimg_init': 300, # subsampled frames for finding reference image
        'batch_size': 500, # number of frames per batch
        'maxregshift': 0.1, # max allowed registration shift, as a fraction of frame max(width and height)
        'align_by_chan' : 1, # when multi-channel, you can align by non-functional channel (1-based)
        'reg_tif': False, # whether to save registered tiffs
        'reg_tif_chan2': False, # whether to save channel 2 registered tiffs
        'subpixel' : 10, # precision of subpixel registration (1/subpixel steps)
        'smooth_sigma': 1.15, # ~1 good for 2P recordings, recommend >5 for 1P recordings
        'th_badframes': 1.0, # this parameter determines which frames to exclude when determining cropping - set it smaller to exclude more frames
        'pad_fft': False,
        # non rigid registration settings
        'nonrigid': True, # whether to use nonrigid registration
        'block_size': [128, 128], # block size to register (** keep this a multiple of 2 **)
        'snr_thresh': 1.2, # if any nonrigid block is below this threshold, it gets smoothed until above this threshold. 1.0 results in no smoothing
        'maxregshiftNR': 5, # maximum pixel shift allowed for nonrigid, relative to rigid
        # 1P settings
        '1Preg': False, # whether to perform high-pass filtering and tapering
        'spatial_hp': 50, # window for spatial high-pass filtering before registration
        'pre_smooth': 2, # whether to smooth before high-pass filtering before registration
        'spatial_taper': 50, # how much to ignore on edges (important for vignetted windows, for FFT padding do not set BELOW 3*ops['smooth_sigma'])
        # cell detection settings
        'roidetect': True, # whether or not to run ROI extraction
        'spatial_scale': 0, # 0: multi-scale; 1: 6 pixels, 2: 12 pixels, 3: 24 pixels, 4: 48 pixels
        'connected': True, # whether or not to keep ROIs fully connected (set to 0 for dendrites)
        'nbinned': 5000, # max number of binned frames for cell detection
        'max_iterations': 20, # maximum number of iterations to do cell detection
        'threshold_scaling': 5., # adjust the automatically determined threshold by this scalar multiplier
        'max_overlap': 0.75, # cells with more overlap than this get removed during triage, before refinement
        'high_pass': 100, # running mean subtraction with window of size 'high_pass' (use low values for 1P)
        # ROI extraction parameters
        'inner_neuropil_radius': 2, # number of pixels to keep between ROI and neuropil donut
        'min_neuropil_pixels': 350, # minimum number of pixels in the neuropil
        'allow_overlap': False, # pixels that are overlapping are thrown out (False) or added to both ROIs (True)
        # channel 2 detection settings (stat[n]['chan2'], stat[n]['not_chan2'])
        'chan2_thres': 0.65, # minimum for detection of brightness on channel 2
        # deconvolution settings
        'baseline': 'maximin', # baselining mode (can also choose 'prctile')
        'win_baseline': 60., # window for maximin
        'sig_baseline': 10., # smoothing constant for gaussian filter
        'prctile_baseline': 8.,# optional (whether to use a percentile baseline)
        'neucoeff': .7,  # neuropil coefficient
        'xrange': np.array([0, 0]),
        'yrange': np.array([0, 0]),
      }
~~~~

## Dependencies
suite2p relies on the following excellent packages (which are automatically installed with conda/pip if missing):
- [rastermap](https://github.com/MouseLand/rastermap)
- [pyqtgraph](http://pyqtgraph.org/)
- [PyQt5](http://pyqt.sourceforge.net/Docs/PyQt5/)
- [numpy](http://www.numpy.org/) (>=1.16.0)
- [numba](http://numba.pydata.org/numba-doc/latest/user/5minguide.html)
- [mkl_fft](https://anaconda.org/conda-forge/mkl_fft)
- [scanimage-tiff-reader](https://vidriotech.gitlab.io/scanimagetiffreader-python/)
- [scipy](https://www.scipy.org/)
- [h5py](https://www.h5py.org/)
- [scikit-image](https://scikit-image.org/)
- [scikit-learn](http://scikit-learn.org/stable/)
- [scanimage-tiff-reader](http://scanimage.gitlab.io/ScanImageTiffReaderDocs/)
- [natsort](https://natsort.readthedocs.io/en/master/)
- [matplotlib](https://matplotlib.org/) (not for plotting (only using hsv_to_rgb and colormap function), should not conflict with PyQt5)

### Logo
Logo was designed by Shelby Stringer and [Chris Czaja](http://chrisczaja.com/).
