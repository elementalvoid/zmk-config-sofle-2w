# Sofle v2.0w ZMK Configuration

This is my configuration for the Sofle v2.0w (as found here: <https://github.com/josefadamcik/SofleKeyboard/pull/180>).

## My configuration

- Either raise/lower layer plus the thumb for a sticky `MEH` key.
- The top right key on the right side is bound to `F24`.
- The `adjust` layer is effectively disabled (all keys mapped to `&none`)
as I don't use it.
- Deep sleep is enabled.

For a case, I'm using the [bottom from here](https://www.printables.com/model/825787-sofle-v2-case-remix)
(without a top).

## Custom `nice!view` peripheral image

Used [this as a starting point](https://www.reddit.com/r/ErgoMechKeyboards/comments/15t3o6k/custom_art_on_niceview_displays/)
but implemented as a copy/update of the builtin shield so that the existing
GitHub Workflow is supported.

Copied from the [upstream shield](https://github.com/zmkfirmware/zmk/tree/main/app/boards/shields/nice_view)

## Building and configuring

Refer to the [v2 build guide](https://josefadamcik.github.io/SofleKeyboard/build_guide.html)
for installation instructions.

Note that the Bill of Materials (BOM) is slightly different:

- Uses nice!nano v2.0
- ALPS power switch
- JST connectors for battery
- Removal of the TRRS connectors
- Kailh MX or Choc hotswap switch support
- Optional nice!view

Regarding the batter/JST connections:

The JST can be soldered two different ways on the PCB. I took advantage of this
to ensure that my batteries were using the same +/- orientation in their
connector so that they were interchangeable between sides. This required
that the connector on the board was installed differently per side. Minor thing
to remember during build for the ease of battery interchange later.

To use the nice!view:

1. Enable `nice_view_adapter` in `build.yaml` of zmk-config.
2. Override `cs-gpios` in `sofle.keymap` file of zmk-config:

    ```c
    &nice_view_spi { cs-gpios = <&pro_micro 0 GPIO_ACTIVE_HIGH>; };
    ```

## References

- [Sofle Keyboard Main Repository](https://github.com/josefadamcik/SofleKeyboard)
- [ZMK Docs](https://zmk.dev/docs)

## Thanks

A huge thanks to @GarrettFaucher for the wireless mod! 🥇 And,
of course @josefadamcik for the Sofle.
