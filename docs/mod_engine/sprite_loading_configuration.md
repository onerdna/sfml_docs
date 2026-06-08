# Sprite loading configuration

Since version 0.2.0, SFML allows specifying properties for sprites that are loaded by the Mod Engine. Create a `.toml` file named to match the sprite file name without the extension.

!!! note "Example"
    For a sprite `my_sprite.png` the corresponding configuration file must be named as `my_sprite.toml`.

If no configuration file exists, SFML uses the following default settings:

```toml
rect_x_min = 0
rect_y_min = 0
# rect_width = doesn't have a default value. If not set, then SFML uses texture's width as the value for this option.
# rect_height = doesn't have a default value. If not set, then SFML uses texture's height as the value for this option.
pivot_x = 0.5
pivot_y = 0.5
pixels_per_unit = 100
```

Any unspecified parameter defaults to the values above. These settings correspond to Unity’s `Sprite.Create` arguments.

Visit Unity documentation for details on how these options impact sprite rendering: https://docs.unity3d.com/6000.0/Documentation/ScriptReference/Sprite.Create.html
