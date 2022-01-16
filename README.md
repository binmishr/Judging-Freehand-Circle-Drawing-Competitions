# Judging-Freehand-Circle-Drawing-Competitions

The details of the codeset and plots are included in the attached Microsoft Word Document (.docx) file in this repository. 
You need to view the file in "Read Mode" to see the contents properly after downloading the same.

Imager Package - A Brief Introduction
======================================

Imager is an image/video processing package for R, based on CImg, a C++ library by David Tschumperlé. CImg provides an easy-to-use and consistent API for image processing, which imager largely replicates. CImg supports images in up to four dimensions, which makes it suitable for applications like video processing/hyperspectral imaging/MRI.

Installing the package
======================
Imager is on CRAN:

    install.packages("imager")
should do the trick. You may also want to install ImageMagick and ffmpeg, see "External Dependencies" below.

OS X
======
Install XQuartz if you haven't already (it's required for the interactive functions). You'll need Xcode (OS X's development environment) to compile source packages. The FFTW library is needed, and the easiest way to install it is via Homebrew. Install Homebrew, then run:

    brew install fftw
Optionally, install libtiff for better support of TIFF files.

Windows
==========
Building R packages on Windows is a bit of a pain so you're probably better off with the binary package (which may not be up-to-date). If you need the latest version of imager, you'll have to,Install Rtools.Install additional libraries for Rtools. You want the package that's called "local tree". Put those libraries somewhere gcc can find them.

Linux
=======
To build under Linux make sure you have the headers for libX11 and libfftw3 (optionally, libtiff as well). On my Ubuntu system this seems to be enough:

    sudo apt-get install libfftw3-dev libx11-dev libtiff-dev
For other Linux distributions, you can query the SystemRequirements database to determine the relevant dependency package names for imager:

# install prerequisites
    install.packages("remotes")


# set platform; supported platforms are listed here: 
    target <- "linux-x86_64-arch-gcc"

# print package names as character vector
    tmp <- tempfile(pattern = "DESCRIPTION")
    sysreqs::(desc = tmp,
              platform = "linux-x86_64-arch-gcc",
        soft = FALSE)
    rm(tmp)

External dependencies
======================
OS X users need XQuartz. On its own imager supports JPEG, PNG, TIFF and BMP formats. If you need support for other file types install ImageMagick. To load and save videos you'll need ffmpeg, no file formats are supported natively.

Getting started
================
Here's a small demo that actually demonstrates an interesting property of colour perception:

    library(imager)
    library(purrr)
    parrots <- load.example("parrots")
    plot(parrots)
    #Define a function that converts to YUV, blurs a specific channel, and converts back
    bchan <- function(im,ind,sigma=5) { 
      im <- RGBtoYUV(im)
      channel(im,ind) <- isoblur(channel(im,ind),sigma); 
      YUVtoRGB(im)
    }
    #Run the function on all three channels and collect the results as a list
    blurred <- map_il(1:3,~ bchan(parrots,.))
    names(blurred) <- c("Luminance blur (Y)","Chrominance blur (U)","Chrominance blur (V)")
    plot(blurred)
We're much more sensitive to luminance edges than we are to colour edges.Documentation is available here. To get a list of all package functions, run:

    ls(pos = "package:imager")
    
Important warning on memory usage
===================================
All images are stored as standard R numeric vectors (i.e., double-precision), meaning that they take up a lot of memory. It's easy to underestimate how much storage you need for videos, because they take up so little space in a compressed format. Before you can work on it in R a video has to be fully decompressed and stored as double-precision floats. To get a sense of the size, consider a low-resolution (400x300), colour video lasting 120 sec. The video might take up a few MBs when compressed. To store it in memory, you'll need: (400x300) x (25x120) x 3 values, corresponding to (space)x(time)x(colour). In addition, each value costs 8 bytes of storage, for a grand total of 8GB of memory.
