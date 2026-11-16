# Houji Bluetooth DT reconstruction

qcom/houji-sm8650-bluetooth.dtsi reconstructs the Bluetooth description from
the user-provided global OS3.0.305.0.WNCMIXM Fastboot archive:
houji_global_images_OS3.0.305.0.WNCMIXM_20261015.0000.00_16.0_global_ef90c126ec.tgz.

The input is the stock vendor_boot DTB after applying the stock houji DTBO.
All four stock base variants carry the same Bluetooth properties. They are
used as the source of board wiring, not another phone's guessed GPIO values.

The core shennong release lacked bt_kiwi, its GPIO17 sleep pinctrl group and
the SLIM NGD/BT endpoint, and left the SLIM controller disabled. The new
description restores the original regulator providers, PDC voltage votes,
mailbox, GPIO16/17/18 controls, wake GPIO and pinctrl, enables the original
controller settings, and adds ngd@1 with the slim217,221 endpoint and exact
RX/TX channel IDs. Existing controller registers, DMA and IRQs are retained.
Stock numeric phandles are resolved to actual existing source provider labels;
new labels identify the added sleep group and endpoint.

Full dtbs compiles, and both product base DTBs accept the houji overlay.
Across the 7 Bluetooth/controller/pinctrl node paths,
51 properties per tree match all four stock and
both source merged variants after provider-reference normalization. The
temporary comparison is out/houji-bluetooth-dt-validation/report.json.

The device late stock-module group supplies unchanged MIXM btpower and
bt_fm_slim. Their source/stock provider CRCs are verified during the build.
This does not establish runtime CFI, regulator sequencing, coexistence,
firmware, SLIM audio or Bluetooth HAL compatibility. Those need device tests.
