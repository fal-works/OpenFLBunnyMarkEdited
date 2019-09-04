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
		var numBunnies = #if bunnies Std.parseInt(haxe.macro.Compiler.getDefine("bunnies")) #else 100 #end;
		var numBunnies2 = #if bunnies2 Std.parseInt(haxe.macro.Compiler.getDefine("bunnies2")) #else numBunnies * 2 #end;

		switch(phase) {
			case 0:
				useNewTilemap(numBunnies2);
			case 1:
				useNewTilemap(numBunnies);
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