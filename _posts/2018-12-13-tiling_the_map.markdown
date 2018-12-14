---
layout: post
title:      "Tiling The Map"
date:       2018-12-13 21:36:35 -0500
permalink:  tiling_the_map
---


A common way to generate a large map for an interactive application is to build it from a set of tiles. This is a classic technique for generating complex maps while limiting the amount of texture resources that are required. This post will cover the tiling approach I've used for the Last Ditch open source simulation application. It is available here: [Last Ditch Repo](https://github.com/ecssiah/last-ditch).

The first step is to generate some tilesets to be used for the map. Only one tileset is required, but I have used multiple tilesets, one for each category of tiles in the application. I have a `map_tileset` that holds all the tiles which make up elements like the floor and walls of the map. There is another that called `obj_tileset` that contains the tiles related to objects placed on the map. In Last Ditch, everything is a tile. This is generally not the case in most applications that use tile maps. They will usually generate the map using tiles, but then overlay other sprites on top of the map. The method used in this post can still be used for those applications, but more rendering would be done on top of the tile rendering.

A tileset image is an image with tiles laid out in a grid across the image. This will allow a simple 2 dimensional "uv" coordinate to locate each tile for rendering. My tilesets use 64x64 pixel tiles, and they are 25x15 tiles in total. This produces 1600x960 images. It is helpful to make use of the grid system that is available in many image manipulation programs like GIMP or PhotoShop. This way you can easily see the borders of each pixel while working on the tileset.

In the application, I load each of these textures using the `SDL_image` library by first loading them into a surface with `IMG_Load` and then creating a texture from that surface using `SDL_CreateTextureFromSurface`. That gives me a `SDL_Texture*` pointer that I store into a tileset map.

```
void RenderSystem::LoadTilesets()                                                
{                                                                                
  tilesets_["floor"] = LoadTexture("map_tileset");                                 
  tilesets_["wall"] = tilesets_["floor"];                                        
  tilesets_["obj"] = LoadTexture("obj_tileset");                                 
  tilesets_["chr"] = LoadTexture("chr_tileset");                                 
}
```

To store map information, I used a struct that contained an `unordered_map<string, Layer>`. Each Layer contains a 2 dimensional array of Tile structs. The Tile structs are then loaded with information about the tile, including the location it will be pulled from when rendering it from the tileset. This information is stored in the Tile in the form of an `SDL_Rect`. Which is a simple rectange struct that defines the `x` and `y` coordinates of the tile in the tileset, and it also defines the `width` and `height` of the tile. To get a better idea of the details, the application repo can be referenced. The map is geneated in the `MapSystem` class, and it is rendered in the `RenderSystem` class. The map information is stored in the `Map` component.

In the `MapSystem`, there is a function that sets the information for each active tile in the map.

```
void MapSystem::SetTile(string layer, int x, int y, string type) 
{                 
  Tile& tile = map_.layers[layer].tiles[x][y];                                     
  tile.active = true;                                                              
                                                                                   
  tile.src.x = TileData[type].uv[0] * TILE_SIZE;                                   
  tile.src.y = TileData[type].uv[1] * TILE_SIZE;                                   
} 
```

It gets a reference to the tile. It then sets the tile to active and pulls the uv coordinates for the tile from a `TileData` unordered_map where the information for each tile type is stored. These uv coordinates are multiplied by the size of each tile and then assigned to the src `SDL_Rect` for the tile.

In the `RenderSystem`, the tile is rendered if it is active using this function:

```
void RenderSystem::RenderTile(string layer, int x, int y)
{
  Tile& tile = map_.layers[layer].tiles[x][y];
                                                                                 
  if (tile.active) {
    float scale_factor{camera_.zoom * TILE_SIZE};
                                                                                 
    SDL_Rect dst;
    dst.x = (x + camera_.pos.x) * scale_factor + HALF_SCREEN_SIZE_X;
    dst.y = (y + camera_.pos.y) * scale_factor + HALF_SCREEN_SIZE_Y;
    dst.w = scale_factor;
    dst.h = scale_factor;
                                                                                 
    SDL_RenderCopyEx(
      renderer_, tilesets_[layer],
      &tile.src, &dst,
      0, nullptr, SDL_FLIP_NONE
    );
  }
}
```

This function gets a reference to the tile at this location, then it checks to see if the tile is active before trying to render it. If the tile is active, then it's position in screen coordinates is calculated and stored into a "destination" `SDL_Rect`. The camera position is applied to the value so that the camera's view is accounted for, and the position and size are scaled based on the camera's zoom. The `x` and `y` values are also translated by half the width of the screen, so that the camera is centered on its position, rather than being centered at the upper left side of the screen.

Finally, the tile is rendered using `SDL_RenderCopyEx`, which takes a texture and renders it to the screen. The "Ex" version of this function allows you to rotate or mirror the tile around a specified point. This feature is not yet being used in the example, but it will eventually be helpful when tiles need to be rotated.

The last thing that needs to be done is to call this function for each tile in each layer. This is done in `RenderSystem` as well:

```
void RenderSystem::RenderMap()                                                   
{                                                                                
  for (auto x{0}; x < TILES_PER_LAYER; ++x) {                                    
    for (auto y{0}; y < TILES_PER_LAYER; ++y) {                                  
      RenderTile("floor", x, y);                                                 
      RenderTile("wall", x, y);                                                  
      RenderTile("obj", x, y);                                                   
      RenderTile("chr", x, y);                                                   
    }                                                                            
  }                                                                              
}       
```

Contributions to Last Ditch are welcome through the github repo.


