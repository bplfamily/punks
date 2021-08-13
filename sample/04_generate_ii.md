# 10 000 More Punks - Inside the Punk Art Machinery (Continued) - How To Generate Punks, Algorithmically - Paint by Numbers - A New Punk Series

> 9 rare CryptoPunks from the LavraLabs [24×24 pixel] series to
> star in our 21st Century Evening Sale this May [13th] in New York.
>
> All mint numbers under 1k and yes there is a rare Alien!
> Punks ![](i/punk-0002.png)[#2](https://www.larvalabs.com/cryptopunks/details/2),
> ![](i/punk-0532.png)[#532](https://www.larvalabs.com/cryptopunks/details/532),
> ![](i/punk-0058.png)[#58](https://www.larvalabs.com/cryptopunks/details/58),
> ![](i/punk-0030.png)[#30](https://www.larvalabs.com/cryptopunks/details/30),
> ![](i/punk-0635.png)[#635](https://www.larvalabs.com/cryptopunks/details/635),
> ![](i/punk-0602.png)[#602](https://www.larvalabs.com/cryptopunks/details/602),
> ![](i/punk-0768.png)[#768](https://www.larvalabs.com/cryptopunks/details/768),
> ![](i/punk-0603.png)[#603](https://www.larvalabs.com/cryptopunks/details/603),
> ![](i/punk-0757.png)[#757](https://www.larvalabs.com/cryptopunks/details/757).
> [Estimate: $7,000,000-9,000,0000.]
>
> -- [Christie's [- The World's Leading Auction House], April 2021](https://twitter.com/ChristiesInc/status/1380236081472364550)


The bad news - the formula for the original 10 000 algorithmically generated 24×24 pixel CryptoPunks by LavraLabs is a secret
(and not included in the published open source code).


The good news  -  emirongrr has put together a simple
pixel art generator to get your started with your
own punk series.


Let's convert the [`pixel art generator`](https://github.com/emirongrr/pixelArt_Generator) machinery.
Let's start with all the parts of a punk:


``` ruby
PARTS = {
  face:     { },
  hair:     { offset: [7, 1] },
  glasses:  { offset: [6, 7] },
  beard:    { offset: [11, 19] },
}
```


A punk is composed of four parts, that is,

- Face
- Hair
- Glasses
- Beard

The face - the first part - is always required
and the rest is optional.

For every part there is a matching directory (e.g. `/face`, `/hair`, `/glasses`, etc.) and
for every variant there is an image with an index number starting at 1 (e.g. `/face/face1.png`, `face/face2.png`, etc.)
Find a cached copy of all images in the `i/parts_ii` directory -
looking something like:

```
parts_ii/
  ├───face/
  │       face1.png
  │       face2.png
  │       face3.png
  ├───hair/
  │       hair1.png
  │       hair2.png
  │       hair3.png
  ├───glasses/
  │       glasses1.png
  └───beard/
          beard1.png
          beard2.png
```

Or an all-together-now sneak preview:

![](i/parts_ii/face/face1.png)
![](i/parts_ii/face/face2.png)
![](i/parts_ii/face/face3.png)

![](i/parts_ii/hair/hair1.png)
![](i/parts_ii/hair/hair2.png)
![](i/parts_ii/hair/hair3.png)

![](i/parts_ii/glasses/glasses1.png)

![](i/parts_ii/beard/beard1.png)
![](i/parts_ii/beard/beard2.png)



Note: Yes, you can add new variants.
Only make sure that the image size for the parts
matches the following formats - only the face is in the 100% full-size 24×24 format:

- face => 24×24
- hair => 10×8
- glasses => 12×6
- beard => 3×3

The offset  (see in `PARTS` above e.g. `offset: [7, 1]`)
tells the starting x,y-coordinates in pixel where the part
will get pasted into the full-size 24×24 punk image
e.g. for hair it's x=7, y=1,
for glasses it's x=6, y=7 and so on.



Let's code the artist known as  `generate_punk` - a method
that returns a (ready-to-save) punk image
from the attribute parts coded as numbers
starting at 1. If the code is 0 than
the (optional) part gets skipped.



``` ruby
codes = [1,1,0,0]        # face (1), hair (1), glasses (x), beard (x)
punk = generate_punk( codes )
punk.save( "./punk-1100.png" )
```


And here's the magic paint by number art machinery:

``` ruby
require 'pixelart'     ## helper library for pixel art images (in .png)

def generate_punk( codes )
  punk = Pixelart::Image.new( 24, 24 )

  PARTS.each_with_index do |(key,part),i|
    code  = codes[i]
    if code != 0    ## if code 0 - let's you skip optional part

      ## compose parts on top (from face to beard)
      path = "./i/parts_ii/#{key}/#{key}#{code}.png"
      img  = Pixelart::Image.read( path )
      x, y = part[:offset] || [0, 0]
      punk.compose!( img, x, y )
    end
  end

  punk
end
```




Let's try for real:

``` ruby
codes = [1,1,0,0]        # face (1), hair (1), glasses (x), beard (x)
punk = generate_punk( codes )
punk.save( "./punk-1100.png" )
```

and let's add a 3x zoom factor:

``` ruby
punk3x = punk.zoom( 3 )
punk3x.save( "./punk-1100x3.png" )
```

resulting in:

![](i/gen_ii/punk-1100.png)
![](i/gen_ii/punk-1100x3.png)



How does the machinery work?
The algo generates an empty 24×24 pixel image / canvas
and than adds - or is that composes -
one part after the other on top
all the way from face to beard.
That's all the magic.


Let's generate another punk.

``` ruby
codes = [2,3,0,1]    # face (2), hair (3), glasses (x), beard (1)
punk = generate_punk( codes )
punk.save( "./punk-#{codes.join}.png" )
punk.zoom( 3 ).save( "./punk-#{codes.join}x3.png" )
```

resulting in:

![](i/gen_ii/punk-2301.png)
![](i/gen_ii/punk-2301x3.png)



And let's generate another punk.

``` ruby
codes = [3,0,1,2]       # face (3), hair (x), glasses (1), beard (2)
punk = generate_punk( codes )
punk.save( "./punk-#{codes.join}.png" )
punk.zoom( 3 ).save( "./punk-#{codes.join}x3.png" )
```

resulting in:

![](i/gen_ii/punk-3012.png)
![](i/gen_ii/punk-3012x3.png)



Now generate your own punk series, algorithmically! Yes, you can!

