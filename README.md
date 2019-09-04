# Original code

[openfl/openfl-samples](https://github.com/openfl/openfl-samples)

© 2013-2019 Joshua Granick and other OpenFL contributors

[MIT License](https://github.com/openfl/openfl-samples/blob/master/LICENSE.md)


# Compilation

`lime test android -Duse_tilemap -Dbunnies=3000`


# Problem

When running with e.g. `-Dbunnies=3000`,

1. The program starts with 3000 bunnies and runs smoothly with 61 FPS.
2. After the first touch, a new `Tilemap` will be used with 6000 bunnies. The FPS is ca. 26, which is (maybe) fine.
3. After the second touch, another new `Tilemap` will be used with 3000 bunnies. This is expected to run smoothly again, but the actual FPS fluctuates between ca. 35-50.


# Tested environments

[1]
- Haxe 3.4.7
- lime 7.5.0
- openfl 8.9.1
- hxcpp 4.0.8
- Android API 26 (rev. 2)
- Android NDK r15c
- Android build-tools 29.0.2
- Java JDK 8u221

[2]
- lime 7.6.0 (fix https://github.com/openfl/lime/issues/1346 manually)
- openfl 8.9.2
- Android API 29 (rev. 3)
- Other: same as [1]

Device
- CPU: MSM8917 (1.4GHz - Quad)
- RAM 2GB
- Android OS 8.1.0

# What is changed from the original BunnyMark

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