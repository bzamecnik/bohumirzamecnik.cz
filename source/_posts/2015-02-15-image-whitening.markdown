---
layout: post
title: "Image whitening - clean B&W documents from nasty photos"
date: 2015-02-15 19:29:32 +0100
comments: true
categories: [image processing]
---

{% img http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/whiteboard/IMG_3129_orig_and_whitened_row_t.jpg %}

Turn a poor smartphone-camera photo of a document or whiteboard into a nice black & white or color "scan" that can be printed or converted to PDF. It works for text / line drawings / sheet music. The method is described here along with a Photoshop tutorial (and a Photoshop action for download) and a handful of examples.

<!--more-->

## The problem

As you can see in the image above, we have taken a photo of a whiteboard at some irregular ambient day light. In order to print it or embed it into web page we want pure white background and nice black text or lines. The image should not be binarized (ie. only black and white pixel), the anti-aliasing of the lines should be preserved.

### Thresholding - bad

The trivial method is [thresholding](http://en.wikipedia.org/wiki/Thresholding_%28image_processing%29) - all pixels darker than some value will be black, and the rest white. It works kind of ok for scans with regular lighting but fails miserably for our photos. The problem is the threshold is global for the whole image and cannot cope with gradients in background. Also scanners are usually not available at hand and certainly cannot scan whiteboards. The other problem with thresholding is that we lose color information and anti-aliasing.

[![](http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/whiteboard/IMG_3129_denoised_threshold_150_t.jpg)](http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/whiteboard/IMG_3129_denoised_threshold_150.jpg)

Well, yes, there are some adaptive thresholding methods out there which try to find optimal threshold value for small image block. Yet, they still produce binarized image and lose colors. We need something better.

## The solution

I've came across this problem many time through the years, tried to solve it many ways, including spectral filtering, without much success. Yesterday I needed to prepare some illustration images for my one of my [blog articles](http://www.visualmusictheory.com/tone-circle.html) and make an animated GIF. I only had my new whiteboard and iphone.

Suddenly, the light bulb in my head lit up:

> Use the median filter!

What does the [median filter](http://en.wikipedia.org/wiki/Median_filter) do. Simply said, for each pixel it finds the most common value from its neighborhood, a [median](http://en.wikipedia.org/wiki/Median). From the image processing lecture I can remember it is good for removing salt and pepper noise, ie. lone black or white pixels. And what are the lines and text? A kind of salt and pepper!

The median filter with wide enough radius of neighborhood just keeps the background and removes the text. Then we can just remove this background, eg. divide the original image by the median-filtered one. A similar (yet not really the same) procedure is called *whitening* in the image-processing field.

	I_whitened = I / median2d(I, radius)

### How to do it in Photoshop

The easiest tool to experiment with this idea is probably Photoshop (although it might not be the best for batch processing and certainly it is not free).

So here the procedure:

- open the image
- duplicate the layer twice
	- name the top layer "median" (not necessary, just for clarity)
	- name the second layer from top "background"
- apply the median filter to the "median" layer: Filter -> Noise -> Median
	- fiddle with the radius so that the background is quite smooth
	- start eg. with 100px for a 6Mpx photo
- set the "median" layer blending mode to "divide"
- merge down the "median" and "background" layers
- voilà!

#### Tips

Try to fiddle with the radius. Use the least radius that doesn't leave too much dark blobs.

In case there are larger areas filled with black or color the whitening doesn't work well. See the "Document with color diagrams" example below. But there's a work around. We need to remove the big spots from the median layer. The *Content-aware fill* feature is our savior:

- just select the remaining blobs
- Edit -> Fill (SHIFT+F5) -> Content Aware
- and select the "divide" blending mode again

Denoise before whitening. A great denoising tool is in Lightroom. Something is probably in Photoshop as well.

Adjust Levels after whitening - do white and black clipping. This leaves the background perfectly white and makes the lines/text more contrasty.

{% img http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/photoshop/levels.png 268 %}

#### Photoshop Action

It is too much hand work. As a bonus I have made a Photoshop action and exported it so that you can just plug it in your installation!

- [median_whitening.atn](http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/photoshop_actions/median_whitening.atn)

## Examples

### Document with color diagrams

Original (bad gradients from a table lamp):

[{% img http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/text_with_color_diagrams/IMG_3274_orig_t.jpg 400 %}](http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/text_with_color_diagrams/IMG_3274_orig.jpg)

Thresholded at 100 (bad):

[{% img http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/text_with_color_diagrams/IMG_3274_threshold_100_t.jpg 400 %}](http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/text_with_color_diagrams/IMG_3274_threshold_100.jpg)

Median with 100 px radius (notice the color blobs):

[{% img http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/text_with_color_diagrams/IMG_3274_median_100px_t.jpg 400 %}](http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/text_with_color_diagrams/IMG_3274_median_100px.jpg)

Whitened (artifacts from color blobs):

[{% img http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/text_with_color_diagrams/IMG_3274_whitented_t.jpg 400 %}](http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/text_with_color_diagrams/IMG_3274_whitented.jpg)

Median with 100 px radius + content-aware fill:

[{% img http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/text_with_color_diagrams/IMG_3274_median_100px_content_aware_fill_t.jpg 400 %}](http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/text_with_color_diagrams/IMG_3274_median_100px_content_aware_fill.jpg)

Whitened (artifacts are gone):

[{% img http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/text_with_color_diagrams/IMG_3274_whitented_content_aware_fill_t.jpg 400 %}](http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/text_with_color_diagrams/IMG_3274_whitented_content_aware_fill.jpg)

### Sheet music

Original (shadow from my hand):

[{% img http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/sheet_music/something_latin_original_photo_t.jpg 400 %}](http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/sheet_music/something_latin_original_photo.jpg)

Thresholded at 128 (bad):

[{% img http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/sheet_music/something_latin_threshold_128_t.jpg 400 %}](http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/sheet_music/something_latin_threshold_128.jpg)

Median with 100 px radius:

[{% img http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/sheet_music/something_latin_median_50px_t.jpg 400 %}](http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/sheet_music/something_latin_median_50px.jpg)

Original divided by the median:

[{% img http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/sheet_music/something_latin_whitened_divide_t.jpg 400 %}](http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/sheet_music/something_latin_whitened_divide.jpg)

### Simple whiteboard

Original, denoised (in Lightroom):

[{% img http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/whiteboard_simple/IMG_3262_denoised_t.jpg 400 %}](http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/whiteboard_simple/IMG_3262_denoised.jpg)

Thresholded at 80 (the light blue is gone - bad):

[{% img http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/whiteboard_simple/IMG_3262-threshold_80_t.jpg 400 %}](http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/whiteboard_simple/IMG_3262-threshold_80.jpg)

Median with 100 px radius:

[{% img http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/whiteboard_simple/IMG_3262_median_100px_t.jpg 400 %}](http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/whiteboard_simple/IMG_3262_median_100px.jpg)

Whitened:

[{% img http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/whiteboard_simple/IMG_3262_whitened_t.jpg 400 %}](http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/whiteboard_simple/IMG_3262_whitened.jpg)

Whitened + levels:

[{% img http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/whiteboard_simple/IMG_3262_whitened_levels_t.jpg 400 %}](http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/whiteboard_simple/IMG_3262_whitened_levels.jpg)

### Whiteboard

Original (day light + some reflections):

[{% img http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/whiteboard/IMG_3129_orig_t.jpg 400 %}](http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/whiteboard/IMG_3129_orig.jpg)

Denoised in Lightroom (click for detail view):

[{% img http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/whiteboard/IMG_3129_denoised_t.jpg 400 %}](http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/whiteboard/IMG_3129_denoised.jpg)

Denoised + thresholded at 150 (bad):

[{% img http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/whiteboard/IMG_3129_denoised_threshold_150_t.jpg 400 %}](http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/whiteboard/IMG_3129_denoised_threshold_150.jpg)

Median with 100 px radius:

[{% img http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/whiteboard/IMG_3129_median_100px_t.jpg 400 %}](http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/whiteboard/IMG_3129_median_100px.jpg)

Whitened:

[{% img http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/whiteboard/IMG_3129_whitened_color_t.jpg 400 %}](http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/whiteboard/IMG_3129_whitened_color.jpg)

Whitened + desaturated + levels:

[{% img http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/whiteboard/IMG_3129_whitened_bw_levels_t.jpg 400 %}](http://i.bohumirzamecnik.cz/2015-02-15-image-whitening/whiteboard/IMG_3129_whitened_bw_levels.jpg)

## Conclusion / TL;DR

In this article I've presented a very simple, yet effective method for converting photographed or scanned documents or whiteboards to a nice printable form with pure white backround and crispy dark text / lines.

The principle is using median filter with a wide radius to remove the text and then divide the original image by the filtered one.

We have seen a procedure in Photoshop and the action available for download.

This method is most suitable for smaller text and lines. It has problems with larger dark or colored blobs. In such case we can manually apply content-aware fill to the remaing blobs in median layer to overcome the problem.

It is good to use denoising before and adjust level after the filter.

### Further directions

Since Photoshop is not free and most importantly is it not that great for true batch processing I'd like to implement this method in Python. Hope the code will be available soon.

I also hope you found this article useful. If so feel free to share it and let me know. Also you can follow me at twitter: [@bzamecnik](https://twitter.com/bzamecnik).

Enjoy!
