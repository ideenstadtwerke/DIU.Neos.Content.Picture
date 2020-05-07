# Responsive Images in Neos

This package delivers the basic image content element for responsive Neos websites.
It's heavily depending on the Sitegeist Kaleidoskop package. See the packages Readme here for more information
about possible settings: 
https://github.com/sitegeist/Sitegeist.Kaleidoscope

## Using this package
By default this package creates a picture tag with fallback image so that also IE11 is supported.
It also creates several renditions of an image as the default type of the image, like jpg, gif ... and also a webp format.
Make sure that your system/server is correctly configured in order to get .webp images out of the box.

## Adapt image size to your grid
In order to have an image fit perfectly to your grid settings do the following:

1) Make sure you add this package to your site packages composer.json as a required dependency
2) Create a fusion file to override the Picture settings, e.g. if you use our DIU.Grid package:

**Two column example**
```
prototype(DIU.Grid:Content.Structure.Grid.Column.Two) {
    prototype(DIU.Neos.Content.Picture:Picture) {
        renderer {
            imageSource.withAsset.renderer = Sitegeist.Kaleidoscope:AssetImageSource {
                width = 540
            }

            sources >
            sources = Neos.Fusion:RawArray {
                webp = Neos.Fusion:RawArray {
                    imageSource = ${imageSource.setFormat('webp')}
                    srcset = '345w, 330w, 450w, 510w, 540w, 690w, 660w, 900w, 1020w, 1080w, 1035w, 990w, 1350w, 1530w, 1620w'
                    sizes = '(max-width: 375px) 345px, (max-width: 767px) 510px,(max-width: 991px) 330px,(max-width: 1199px) 450px, 540px'
                    type = 'image/webp'
                }
                default = Neos.Fusion:RawArray {
                    srcset = '345w, 330w, 450w, 510w, 540w, 690w, 660w, 900w, 1020w, 1080w, 1035w, 990w, 1350w, 1530w, 1620w'
                    sizes = '(max-width: 375px) 345px, (max-width: 767px) 510px,(max-width: 991px) 330px,(max-width: 1199px) 450px, 540px'
                }
            }
        }
    }
}
```  

## How does it work
###Step one
First you tell the browser on which break point the image will have which width.
```
sizes = '(max-width: 375px) 345px, (max-width: 767px) 510px,(max-width: 991px) 330px,(max-width: 1199px) 450px, 540px'
```
These are just the column width at the breakpoints with one additional on mobile, so that we don't load the biggest 
necessary one for 767px.
Now you know which width the image can have in a two column (50%/50%) grid.
One thing, you have to use `max-width` NOT min-width! The last value without the max-width will be then the image for 
every bigger screen. ( So this is different from CSS where you go usually with min-with up )

### Step two
Second we have screens with several dpi values. "Old" regular screens have a value of 1x, others like HiRes screens for 
computer or mobile phones will have a twice as high density "2x" or even "3x". There for we need an image which has the
doubled or three times the size of the column width. This makes the next line, where we have 1x, 2x, 3x of the sizes values:
```
srcset = '345w, 330w, 450w, 510w, 540w, 690w, 660w, 900w, 1020w, 1080w, 1035w, 990w, 1350w, 1530w, 1620w'
```
If the browser doesn't find the exact matching image it will take the next bigger one. This is important to know so that
you provide an efficient set of image widths. Just keep in mind, that every additional value in srcset will increase the
amount of disk space necessary on the server! So you should find a good balance here.  

### Step three
Third one, test your images. We recommand to use Firefox to test responsive images as Firefox will load the different
images when changing the size, whereas Chrome will load once the biggest and won't show you that he will load the smaller
image when you shrink the with of the browser window
