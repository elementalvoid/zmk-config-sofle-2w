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

## ZMK / Zephyr 4.1 upgrade notes

This config was migrated for ZMK's move to **Zephyr 4.1** (ZMK main, 2025-12-09 —
see the [Zephyr 4.1 Update](https://zmk.dev/blog/2025/12/09/zephyr-4-1) post).
What changed and why:

- **Board ID:** `nice_nano_v2` no longer exists. ZMK now uses board revisions,
  so the board is `nice_nano` (revision 2.0.0 by default) and **must** be given
  the `//zmk` variant qualifier: `nice_nano//zmk`.

  This is not cosmetic. With the bare `nice_nano` ID, Zephyr silently skips
  board-specific shield overlays — including the `nice_view_adapter`'s, which is
  what defines the `&nice_view_spi` bus. The build then fails with
  `undefined node label 'nice_view_spi'`.

- **Encoders:** `CONFIG_EC11` is now auto-selected from the devicetree
  (`DT_HAS_ALPS_EC11_ENABLED`) and does **not** need to be set. However
  `CONFIG_EC11_TRIGGER_GLOBAL_THREAD=y` is still required and load-bearing: the
  new default trigger mode is `EC11_TRIGGER_NONE`, which leaves the driver
  without interrupt support and the encoders non-functional.

- **LVGL v8 → v9:** the custom `nice_view_custom` shield was ported to the
  new LVGL drawing API (`canvas_draw_*` helpers, `LV_COLOR_FORMAT_*`,
  `lv_image_*`, `LV_USE_IMAGE`, uint8_t canvas buffers). Custom peripheral art
  (balloon/mountain/ninja) is retained.

- **`zmk_endpoints_selected()`** was renamed to `zmk_endpoint_get_selected()`.

- **Snippets:** `snippet: zmk-usb-logging` in `build.yaml` is unchanged and still valid.

### Encoder sensitivity

The upstream Sofle shield now sets `triggers-per-rotation = <20>` on the sensors
node, which this config inherits (it was previously unset). Both encoders keep
the custom `steps = <30>`. If volume or scroll feels too fast/slow after
flashing, tune `triggers-per-rotation` in `sofle.keymap`:

```c
&sensors {
    triggers-per-rotation = <18>;
};
```

### Building locally

The build used to be verified by GitHub Actions only. It can also be built
locally with `west`; the ZMK CI container (`zmkfirmware/zmk-build-arm:4.1`,
Zephyr SDK 0.16.9) is the reference toolchain, and matching that SDK version
locally avoids libc mismatch errors.

## References

- [Sofle Keyboard Main Repository](https://github.com/josefadamcik/SofleKeyboard)
- [ZMK Docs](https://zmk.dev/docs)

## Thanks

A huge thanks to @GarrettFaucher for the wireless mod! 🥇 And,
of course @josefadamcik for the Sofle.
