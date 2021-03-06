.. _specidentify:

************
specidentify
************


Name
====

specidentify-- Calculate the wavelength solution for ARC images

Usage
=====

specidentify images linelist outfile (automethod) (guesstype) (guessfile)
(function) (order) (rstep) (mdiff) (sigma) (niter) (rstart)
(inter) (clobber) (logfile) (verbose)

Parameters
==========


*images*
    String. List of FITS images to prepare. Data can be provided as a
    comma-separated list, or a string with a wildcard
    (e.g. 'images=S20061210*.fits'), or a foreign file containing an ascii
    list of image filenames. For the ascii list option, the filename
    containing the list must be provided preceded by a '@' character,
    e.g. 'images=@listoffiles.lis'. The list can contain data files from
    multiple SALT instruments.

*linelist*
    String.  File or list of files with ARC lines for the observed
    lamp.  The format should be a two-column file with wavelength and
    relative flux.

*outfile*
    String. An ascii file for the resulting wavelength solution to be
    stored in.  The outfile will have the identification for the spectrograph
    configuration in the header as comments and then a table with the
    wavelength solutions.  The format of the solutions will be a row number
    and then the coefficients for different solutions

*(guesstype)*
    String. The type of initial guess for the wavelength solution.  Currently, the
    only initial guess method is 'rss' which uses the model for the RSS spectrograph
    as the initial guess.

*(guessfile)*
    String. File with the initial guess.  Currently not used.

*(automethod)*
    String. SPECIDENTIFY supports three different methods for automatic
    line identification.  These methods include 'Zeropoint', 'Mactchlines',
    and 'MatchZero'.  'Zeropoint' shifts the solution only by its zeropoint
    to match lines.   'Matchlines' tries to match lines by their position and
    flux.   'MatchZero' combines these two methods to iteratively match the lines.

*(function)*
    String.  Functional form for the fit to the model.  It is only needed if calytpe='rss'.

*(order)*
    Int.  Order for the fit to the model.  It is only needed if caltype='rss'.

*(rstep)*
    Int.  When calculating the solution, the number of rows to step between
    calculating each solution.

*(rstart)*
    String.  The first row to calculate the solution.  The input is a string.  If the user
    specifies 'middleline', the middle row will be used to calculate the first solution.
    If the user specifies an integer as a string, then this row will be used.

*(mdiff)*
    Int.  mdiff is the maximum difference in pixels that an arc line and an observed feature
    in the image can be in order to be matched.

*(thresh)*
    Real.  Threshold for detecting features in the image.

*(niter)*
    Int.  Number of times to iterate on detecting features and calculating the wavlength solution.

*(smooth)*
    Int.  Smooth the spectrum with a Gaussian kernel before plotting

*(inter)*
    Bool.  If 'yes', saltspec will run in interactive mode.

*(startext)*
    Int.  startextf is the extension to start on.  The default is zero, but it is useful especially
    for multi-object slit masks.

*(clobber)*
    Hidden boolean. If set to 'yes' files contained within the outpath
    directory will be overwritten by newly created files of the same
    name.

*(logfile)*
    String. Name of an ascii file for storing log and error messages
    from the tool. The file may be new, or messages can also be appended to a
    pre-existing file.

*(verbose)*
    Hidden Boolean. If verbose=n, log messages will be suppressed.

Description
===========


SPECIDENTIFY identies Arc lines in a spectra and calculates the
wavlength solution.  The task can run interactively where the user can
confirm the identification of the lines or it can be run automatically
where the task will identify the lines without any assistance from the
user.

The input for the task is an RSS Arc image that has been processed by
the SALTRED package.  The input data should be mosaicked as well as
having any other corrections already applied to the data.  In
addition, the user supplies a file with the wavelengths of lines that
appear in the spectrum of the lamp.  For best results, the user should
supply a linelist that includes the line wavelength and the relative
line flux.

At this current time, the user supplies an initial guess for the data.
There are currently only two options for the initial guess: 'rss' and
'file'.  If 'rss' is selected, the initial guess is based on the
instrument configuration.  The SPECIDENTIFY task will use the RSS
model from the PySpectrogrph package and based on the grating, set-up,
and configuration of RSS, it will determine an initial guess for the
wavelength solution.  If guess method is 'file', then SPECIDENTIFY
will read in guessfile and determine the wavelength solution based on
the previously calculated wavelength solutions.

The user should also set the function and order for the wavelength
solution.  The different types include polynomial, legendre,
chebyshev, and model.  If model is chosen, it will fit the free
spectrograph model parameters that included the center of the chip
with respect to the central ray, the focus of the instrument, and the
index of refraction.  It will model the index of refraction as a
polynomial with an order given by order.

Once the initial wavelength solution is calculated, the dispersion of
that solution is used to create an artificial spectrum from the line
list.  This artificial spectrum will be compared to the real sprectum,
and by convolving the line list with the predicted dispersion, we can
use a single line list for all data to account for blended lines in
the observed spectrum.

If inter is set to no, the task will then use one of three methods set
in automethod for automatically determine the lines.  The first
method, 'Zeropoint', determine the zeropoint shift of the wavelength
solution through cross-correlating the arc lines.  'Matchlines' works
by iteratively trying to identify lines by being near the solution and
then progressively improving the solution and finding more lines.
'MatchZero' combines the two and does a zeropoint fit, repeated by
matching the lines, and then iterating up to niter times or until the
solution has stabilized.

Using section, the user can specify the first line to begin fitting
the data.  If the user specifies 'middlerow', then it will
automatically select the row corresponding to the optical axis of the
system or the middle row the data section supplied.  Otherwise, if the
user supplies an integer, it will begin at that line.  By setting
nrows, the user can specify the number of rows that are averaged
together before identifying Arc lines in those rows.  By setting
rstep, the user can specify the number of steps to take between
finding the solution for different rows, ie. if rstep=10, then the
next row fit will be 10 rows away.


If inter is set to 'yes', the user can interactively identify the
lines.  A GUI interface will appear with three tabs on it.  The first
tab is the full 2D image and the line extracted will be highlighted by
two green lines drawn over the image.  On this tab, the user can
select different lines to be selected, change nrow or rstep, or run
the tool automatically to determine the wavelength solution.  Once the
user has determined the wavelength solution using the other tabs, they
can automatically determine the wavelength solution for the rest of
the image using automethod by selecting 'Auto-Identify' button.

On the next tab, the extracted spectrum along that row is presented.
On this screen the user can use different methods to calculate the
solution.  At any point, the user can see the solution by typing 'P'.
By hitting 'z', a zeropoint shift to solution will be determined
through cross correlating.  By selecting 'b', it will automatically
identify line features.  Lines are required to be within mdiff pixels
of observed features in order to be identified with them.  The user
can manually add lines by entering in the value for the pixel and
wavelength position and then hitting update.  In addition, the user
can also update the function, order, and automethod from this tab.

On the third tab, the user can view the residual in the fit.  The
residual is plotted as a function of pixel position and the user can
delete points by selecting a point and pressing 'd'.  The mean and rms
of the fit are also supplied on this tab.  The points can also be
rejected automatically by iterating over the fit and the user can
update the niter and thresh parameters used for that rejection on this
tab.

At anytime, the user can bring up the interactive help function by
typing '?'.  If the user does that, it brings up all of the key
bindings that are available to the user that include::

    ? - Print this file     q - Quit the program
    c - centroid on line    x - print the current position
    a - Display spectrum    l - display features
    b - identify features
    p - print features      P - print solution
    z - zeropoint fit       f - fit solution
    r - redraw spectrum     R - reset values
    d - delete feature      u - undelete feature

Once complete, the task will write out to a file specified by solfile.
In ths file, the paramters of the observations will be listed as
comments.  For each row the solution was calculated on, one line will
be printed to this file with the first column being the line, and then
the next columns will be the coefficients of the solution starting
with zeroth order.  The task will append this values to this file if
this file already exists.

EXAMPLES
1. To calibrate science FITS files with specrectify using solutions
based on an arc lamp::

    --> specidentify images='pmbxpP*.fits' linelist='Ne.dat' outfile='dbsol'
    guesstype='rss' automethod='Matchlines' logfile='salt.log'

Time and disk requirements
==========================

Individual unbinned raw full-frame RSS files can be 112MB in size. It is
recommended to use workstations with a minimum of 512MB RAM. On a
linux machine with 2.8 Ghz processor and 2 Gb of RAM, one 2051x2051 image
in 0.15 sec.

Bugs and limitations
====================

No other guess types are currently accepted.  If the user wants to change
the function form or order and apply it to all data, they will have to
restart the task.


Send feedback and bug reports to salthelp@saao.ac.za

See also
========

 :ref:`specidentify`