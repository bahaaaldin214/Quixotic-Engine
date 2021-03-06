<h1>Getting started</h1>

<h2>Importing engine</h2>
		
Install [quixotic.min.js](https://raw.githubusercontent.com/bahaaaldin214/Quixotic-Engine/master/quixotic.min.js)

##Some Examples [](https://bahaaaldin214.github.io/Quixotic-Engine/examples)

In a module js file, import `QuixoticEngine` which is exported as default

<h2>Initialize engine</h2>

```js

const game = {
  
  create: {
    
    display: {
      
      canvas: document.querySelector("#canvas")
    },
    
    homeURL: "/js/quixotic"
  },
  
  style: {
    
    backgroundColor: "#111122"
  },
  
  world: {},
  
  engine: {
    
    frameRate: 1000/60,
    
    update: function(deltaTime, engine){},
    
    render: function(display){}
    
  },
  
  setup: function(engine, display, controller, world){}
  
};
```
<h3>game.create</h3>

`game.create` takes care of creating the game, it requirs 2 values:

1: display(Object), the display object requirs a canvas key, which value should be a canvas element.
 
<h4>other keys:</h4>

| key        | type   | value                                                  | default |
| ---------- |: ----  | :----------------------------------------------------- | :------ |
| width      | Number | A number for the canvas width .                        | min of width and height |
| height     | Number | A number for the canvas height.                        | min of width and height |
| zAxis      | Number | A number for where the camera z coordinates will be.   | 6 |


2: homeURL(String), a url to where the index.js of the engine is located.

<h3>game.style</h3>

`game.style` takes care of styling the canvas, style object must be initialized but may be empty.

#### other keys:

| key             | type           | value                                                 |
| :-------------- | :------------  |  :--------------------------------------------------- |
| stroke          | Object         | See table below                                       |
| backgroundColor | Quixotic.color | A quixotic color for the stroke                       |
| backgroundImage | String         | Link to image location                                |


##### stroke

| key   | type           | value                           | required |
| ------| :------------  | :---------------------------- | :------: |
| width | Number         | Stroke width                    |  true    |
| color | Quixotic.color | A quixotic color for the stroke |  true    |

<h3>game.engine</h3>

`game.engine` is where the fun stuff are, it requirs 3 values:

1: frameRate(Number), the game frameRate. The game will update as fast as it can, but will only render under the frameRate.
 

2: Update(Function), the upate function. This function will get called as fast as possible. Use this to update non-engine related stuff as you wish. All `Quixotic.gameEntities` will get updated automatically.
 
3: Render(Function), the render function. This function will get called as fast as possible under the frameRate. Use this to render non-engine related stuff as you wish. All `Quixotic.gameEntities` will get rendered automatically.

<h4>Update Passed arguments</h4>

| argument  | type   | value                       |
| ----------| :----  | :------------------------   |
| deltaTime | Number | time passed in meli-seconds |
| engine    | Engine | The engine instance         |

<h4>Render Passed arguments</h4>

| argument  | type    | value                               |
| ----------|: -----  | :---------------------------------  |
| display   | Display | The Display instance used by engine |

<h3>game.setup</h3>

`game.setup` is where you setup your game, 4 arguments are being passed down to this function:

<h4>Setup Passed arguments</h4>

engine, display, controller, world

| argument     | type       | value                                  |
| -------------|: ----  :   | :-----------------------------         |
| engine       | Engine     | The engine instance                    |
| display      | Display    | The Display instance used by engine    |
| controller   | Controller | The Controller instance used by engine |
| world        | Object     | An Object containing the created world |

<h2>starting engine:</h2>

Now that we created the initial object, now we actually create the engine.

```js

Quixotic.create(game.create)
  .then(engine => {
    
    engine.setup({
        style: game.style,
        
        world: game.world,
        
        engine:game.engine,
        
        setup: game.setup,
        
      });
  });
```

<h1>Creating objects</h1>

There is no game with no objects, so lets create a player.

```js

game.world.objects = [
  {
  	name: "player",
  }
];
```

Now technically the player object have been created, but it's invisible. Lets fill it with a color.

<h2>Extending Entity Class</h2>

You might wanna extend the Entity class for 2 reasons, collision detection or having custom methods.

```js

import {Entity} from "/js/quixotic/index.js"

class Player extends Entity {
  constructor(){
    super();
    //Must always call super
  }
}

game.world = {
  
  classes: {
    Player
  },

  objects: [
    {
    	name: "player",
      class: "Player"
    }
  ],
};

```
<h2>Coloring objects</h2>

We color objects in our setup function.

```js
game.setup = function(engine, display, controller, world){
  
  world.objects.player.fill("#ff00000")
  
};
```

<h2>Attaching images to  objects</h2>

We attach images to objects also in the setup function.

```js
game.setup = function(engine, display, controller, world){
  
  //world.objects.player.fill("#ff00000")
  
  const marioTexture = display.createTexture("mario.png");
  
  world.objects.player.attachImage(marioTexture);
  
};
```

You can use the same texture for as many objects as you like, you can also create copies of the texture for different transformations.

```js

import {Texture} from "/quixotic/index.js"

//...

game.setup = function(engine, display, controller, world){
  
  
  const marioTexture = display.createTexture("mario.png");
  
  const textureCopy = Texture.from(marioTexture)
  world.objects.player.attachImage(textureCopy);
};
```

<h2>Creating multiple objects</h2>

Say you want to create 500 trees, creating each one manually is gonna be a living hell. You can simply set the `array` key to true.

```js

const treeObjects = {
	name: "trees",
	
	array: true,
	amount: 10,
};
```

<h2>Positioning objects</h2>

The coordinate system in the engine is like looking at a graph, (0, 0) will be the center of the screen, (1, 1) being towrd top right, (-1, -1) being bottom left.

<h3>Positioning 1 object</h3>
set:

```js

playerObject.position = {
  type: "set",
  position: [-5, 5]
};

```
random:

```js
playerObject.position = {
	type: "random",
	rangeX: [-30, 30],
	rangeY: [-30, 30]
};
````

<h3>Positioning multiple objects</h3>

```js

treeObjects.position =  {
	type: "set",
	positions: [ [-10,10], [-15,10], [-5,10], [0,10], [5,10], [10,10], [15,10], [20,10], [25,10], [30,10]]
};
```

<h2>Controller</h2>

<h3>Methods</h3>

| name              | description                                                            | arguments            | passed arguments|
| :---              | :---                                                                   | :----                | :---            |
| attachKeyDown     | Attach a function to a key, gets called when that key is pressed down  |  Key name(String), function(Function)  | None            |
| attachKeyUp       | Attach a function to a key, gets called when that key is realased      |  Key name(String), function(Function)  | None            |
| attachMouseButton | Attach a function to a button, gets called when that button is pressed |  X, Y, W, H(Number), function(Function) | x(Number), y(Number)|
| detachKeyUp       | detach key  up   | Key name          |
| detachKeyDown     | detach key  down | Key name          |


<h3>Examples</h3>

```js
game.setup = function(engine, display, controller, world){
  
  const player  = world.objects.player;
  
  const marioTexture = display.createTexture("mario.png");
  const lugiTexture = display.createTexture("lugi.png");
  
  player.attachImage(marioTexture);

  controller.attachKeyUp("1", function(){
    
    player.attachImage(marioTexture);
    
  });
  
  controller.attachKeyUp("2", function(){
    
    player.attachImage(lugiTexture);
    
  });
  
  controller.attachKeyDown(" ", function(){ //space
    
    player.accelerate(0, 20); //make mario move up
    
  });
  
  const canvas = display.canvas;
  
  controller.attachMouseButton(0, 0, canvas.width, canvas.height, function(x, y){ //0, 0 being top left
    
    if(x > canvas.width/2){ //if pressed on the right side of the screen
      player.accelerate(20, 0); //move right
    } else {
      player.accelerate(-20, 0); //else move left
    }
    
  });
  

};
```

<h2>Collision</h2>

Adding collision to your object was made super simple, first you need to make sure that the objects you create is an instance that extends the Entity class. In this example we would be using the `Player` class which we created earlier, and a `Tree` class we will create now.Tree

```js
class Player extends Entity {
  constructor(){
    super();
    //Must always call super
  }
  touchedTree(tree){
    tree.remove();
    //Entity Method wich will remove the object from the game
  }
}

class Tree extends Entity {}

game.world = {
  
  classes: {
    Player,
    Tree
  },

  objects: [
    {
			name: "player",
			class: "Player",
      
      collision: {
			  
			  trees: {
			    
			   	call: "touchedTree",
			   	elastic: true //elastic indicates if it can't go through that object or not
			    
			  },
			  
			}
    },
    {
      name: "trees",
      class: "Tree",
      
      array: true,
      amount: 20,
      
      position: {
        
        type: "random",
        rangeX: [-20, 20],
        rangeY: [-20, 20],
        
      }
    }
  ],
};
```

<h2>Tile Maps</h2>

I know that some people might think that tile maps are an old style, but with this engine the tile map is only used to position the objects, meaning you can move objects as you wish later on.

<h3>How it works</h3>
	
The way it works is it takes 5 keys

<h4> Example </h4>
Example using `Player, Stone, Tree` classes:
```js
game.world = {
  objects: [
    {
			name: "stones",
			type: "class",
			class: "Stone",
			array: true,
			amount: 0,
			
			collision: {
			  
			  player: {
			    
			    call: "touchedPlayer",
			    elastic: true
			    
			  },
			}
			
		},
		{
			name: "trees",
			type: "class",
			class: "Tree",
			array: true,
			amount: 0,
			
			
			collision: {
			  
			  player: {
			    
			    call: "touchedPlayer",
			    elastic: true
			    
			  },
			}
			
		},
		{
			name: "player",
			type: "class",
			class: "Player",
			
		},
	],
    
    classes: {
      Tree,
      Stone,
      Player,
      
    },
    
    tileMap : {
      
      columns: 5,
      tileSize: 5,
      
      startCoords: [0, 0],
      
      values: [
        Grass,
        Tree,
        Stone],
        
      tiles: [
        [0, 1], 1, 1, 1, 1,
        1, 0, 0, 0, 1,
        1, 2, 2, 1, 1,
        1, 0, 1, 0, 1,
        1, 1, 1, 1, 1,
        ]
    }
    
  }
```

In this example, the `Tree` class is the same one we been using, and `Stone` class is the exact same but with a different sprite.
As you might have notices the first value of the tiles array is an array as well that is becaue it's a list of objects on the same position in an order from bottom to top. In this case its a tree tile ontop of a grass tile.

![Image of example above](https://github.com/bahaaaldin214/Quixotic-Engine/edit/master/examples/images/tileMap.png "TileMap example")

<h2>Display</h2>

`TODO:`

<h2>Game</h2>

`TODO:`
