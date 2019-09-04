# Original code

[openfl/openfl-samples](https://github.com/openfl/openfl-samples)

© 2013-2019 Joshua Granick and other OpenFL contributors

[MIT License](https://github.com/openfl/openfl-samples/blob/master/LICENSE.md)


# Compilation

`lime test android -Duse_tilemap -Dbunnies=3000`


# Change

Before
```haxe
	private function stage_onMouseDown(event:MouseEvent):Void
	{
		addingBunnies = true;
	}
```

After
```haxe
	#if !use_tilemap
	private function stage_onMouseDown(event:MouseEvent):Void
	{
		addingBunnies = true;
	}
	#else
	var phase:Int = 0;

	private function stage_onMouseDown(event:MouseEvent):Void
	{
		switch(phase) {
			case 0:
				useNewTilemap(6000);
			case 1:
				useNewTilemap(3000);				
			default: 
				useNewTilemap(Std.int(Math.random() * 7) * 1000);
		}

		++phase;
	}

	private function useNewTilemap(numBunnies:Int) {
		removeChild(tilemap);
		tilemap = new Tilemap(stage.stageWidth, stage.stageHeight, tileset);
		bunnies = [];

		for (i in 0...numBunnies)
			addBunny();

		addChild(tilemap);
	}
	#end
```