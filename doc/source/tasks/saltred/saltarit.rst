.. _saltarith:

*********
saltarith
*********


Name
====

saltarith -- image arithmetic

Usage
=====

saltarith operand1 op operand2 result outpref (divzero) (clobber)  (logfile) (verbose)

Parameters
==========


*operand1, operand2*
    Lists of images and constants to be used as operands. operand1 must be a
    SALT-style fits image. Data can be provided as a comma-separated
    list, or a string with a wildcard (e.g. 'images=S20061210*.fits'), or
    a foreign file containing an ascii list of image filenames. For ascii
    list option, the filename containing the list must be provided
    preceded by a '@' character, e.g. 'images=@listoffiles.lis'.
    

*op*
    Operator to be applied to the operands. This can be any python operator.

*result*
    List of resultant images. This should be the same format as operand1.

*outpref*
    String. If the outpref string is non-zero in length and contains
    characters other than a blank space, it will override any value of the
    result argument. Output file names will use the name list provided
    in the operand1 argument, but adding a prefix to the basename of
    each  output file defined by outpref. An absolute or relative directory
    path can be included in the prefix, e.g. 'outpref=/Volumes/data/p'.

*divzero = 0*
    Replacement value for division by zero.

*clobber*
    Hidden boolean. If set to 'yes' files contained within the outpath
    directory will be overwritten by newly created files of the same
    name.

*logfile*
    String. Name of an ascii file for storing log and error messages
    written by the task. The file may be new, or messages can also be
    appended to a pre-existing file.

*verbose*
    Boolean. If verbose=n, log messages will be suppressed.

Description
===========

SALTARITH is the SALT analog of IMARITH that has been designed to handle SALT's multi-extension files in a smart way. Unlike IMARITH, SALTARITH will to try
to handle any python operator you give it.


Examples
========

1. To multiply an group of images by 10::

    --> saltarith operand1=image*.fits op='*' operand2=10 outpref='m10'
    result='' clobber='yes' logfile='salt.log' verbose='yes'

2. Square the values in an image::

    --> saltarith operand1=image.fits op='**' operand2=2 outpref='sq'
    result='' clobber='yes' logfile='salt.log' verbose='yes'

Bugs and limitations
====================

SALTARITH will make no attempt to stop you from doing something stupid. It
is up to you to make sure you operands make sense. SALTARITH does what you tell
it to do, not what you want it to do. The actual arithmetic is evaluated with python's
eval() command, so there is potential for misuse, accidental or otherwise. This will not
be a problem if you stick to using SALTARITH just like IMARITH.


Send feedback and bug reports to eigenbrot@astro.wisc.edu

See also
========

 :ref:`imarith`