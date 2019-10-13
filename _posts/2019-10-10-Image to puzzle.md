---
title: "Convert an Image to block puzzle"
date: 2019-10-12
tags: [Python, Image, PyGame]
excerpt: "Python script that turns your images into puzzle"
---

<img src="{{ site.url }}{{ site.baseurl }}/images/puzzle/record.gif" alt="Image puzzle gif">

Hi this post explains how you can convert your images into a block puzzle using Python. Lets breakdown the main task into subtasks:

1 Initialise game environment
2 Image loader
3 Divide image into blocks
4 Write mechanism to switch blocks on mouse click.

To dive directly into code you can follow this [link to github](https://github.com/bhanga-manish/Image-Puzzle).


### 1. Initialise game environment

The first step is to initialise the canvas for the game to function properly using PyGame library makes it easy to set up things quickly. PyGame library is highly popular library to create games in Python.

```python
pygame.init()
os.environ['SDL_VIDEO_CENTERED'] = '1'
pygame.display.set_caption("Slide Puzzle")
screen = pygame.display.set_mode((800,600))
fpsclock = pygame.time.Clock()
program = SlidePuzzle((3,3), 150, 5)
```

We can set some important variables something like this.To make pygame screen to appear in the middle set SDL_VIDEO_CENTERED to 1. Set appropriate name to the title of pygame. Initialise frame rate for smoother animations. Lastly we have initialised object of class SlidePuzzle with parameters as (3,3) is the grid size 3 rows and 3 columns 9 tiles, 150 is the size of each tile in pixels and 5 is the border size between the tiles in pixels. Feel free to change these settings according each desire. Let's implement SlidePuzzle class.

```python
class SlidePuzzle:
    def __init__(self, gs, ts, ms):
        self.gs, self.ts, self.ms = gs, ts, ms
        self.tiles_len = (gs[0]*gs[1]) - 1
        self.tiles = [(x,y) for x in range(gs[0]) for y in range(gs[1])]
        self.tilesOG = [(x,y) for x in range(gs[0]) for y in range(gs[1])]    
        self.tilespos = {(x,y):(x*(ts+ms)+ms,y*(ts+ms)+ms) for y in range(gs[1]) for x in range(gs[0])}    
        self.font = pygame.font.Font(None, 120)    
        w,h = gs[0]*(ts+ms)+ms, gs[1]*(ts+ms)+ms
```

Self.tiles and self.tilesOG are same array but later self.tiles is randomly shuffled for puzzle to be solved and self.tilesOG is kept to compare whether puzzled is solved or not. Self.tilespos is list that contains where to cut image for each block. W&h are the height and width of pygame screen.

### 2. Image loader




```python
root = Tk()
root.filename =  filedialog.askopenfilename(title = "Select file",filetypes = (("jpeg files","*.jpg"),("all files","*.*")))
pic = pygame.image.load(root.filename)
pic = pygame.transform.scale(pic, (w,h))
root.destroy()
```

Using Tkinter’s filedialog function gives a GUI interface to select the image for the user.

### 3. Divide image into blocks

```python
self.images = []
for i in range(self.tiles_len):
    x,y = self.tilespos[self.tiles[i]]
    image = pic.subsurface(x,y,ts,ts)
    self.images += [image]

self.temp = self.tiles[:-1]
shuffle(self.temp)
self.temp.insert(len(self.temp), self.tiles[-1])
self.tiles = self.temp
```

For the number of tiles divide image into block using subsurface module. self.temp = self.tiles[:-1] this line sets an empty block which provides room to move tiles. shuffle(self.temp) randomly shuffle the order of blocks in the list.



### 4. Write mechanism to switch blocks on mouse click.

```python
def switch(self, tile):
        """
        # Switch to adjacent opentile
        """
        n = self.tiles.index(tile)
        self.tiles[n], self.opentile = self.opentile, self.tiles[n]
        if self.tiles == self.tilesOG:
            print("COMPLETE")

def is_grid(self, tile):
        """
        # Check mouse click is inside the grid
        """
        return tile[0] >= 0 and tile[0] < self.gs[0] and tile[1] >= 0 and tile[1] < self.gs[1]

def adjacent(self):
        x,y = self.opentile;
        return (x-1, y), (x+1,y), (x,y-1), (x,y+1)

def update(self, dt):
        """
        # Find the tile mouse is on
        # Switch as long as open tile is adjacent
        """

        mouse = pygame.mouse.get_pressed()
        mpos = pygame.mouse.get_pos()

        # Convert mouse position relative to tile position and check in grid
        if mouse[0]:
            tile = mpos[0]//self.ts, mpos[1]//self.ts

            if self.is_grid(tile):
                if tile in self.adjacent():
                    self.switch(tile)

def draw(self, screen):
        for i in range(self.tiles_len):
            x,y = self.tilespos[self.tiles[i]]
            screen.blit(self.images[i], (x,y))            

````

On the mouse click first check if the click is inside the grid and then check if adjacent tile is the open tile using opentile property if both of the conditions are satisfied switch the tile with the adjacent open tile. After switching check if the order of tiles is same as tilesOG that means puzzled is solved.

For full code click [here](https://github.com/bhanga-manish/Image-Puzzle).
