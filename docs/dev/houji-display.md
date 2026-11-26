# Houji display DT integration

The source-kernel OTA built on 2026-11-26 included msm_drm.ko but omitted
all display techpack descriptions from its base DTBs and houji overlay.
The merged trees had no qcom,sde-kms controller, DSI display or N3 panel.
A display module alone cannot bind hardware without these device nodes.

qcom/display links to the existing MiCode display-devicetree checkout.
The houji overlay includes display/houji-sde-display.dtsi, which supplies
the Pineapple SDE/DSI controller description and the original houji panel,
power, GPIO and connector wiring. This keeps the upstream files intact and
uses the same external-tree layout as the camera descriptions.

Validation: the complete dtbs target passes, including overlay application
to pineapple, pineapple-v2, pineapplep and pineapplep-v2. Decompiled merged
trees contain qcom,sde-kms, the DSI display and the bootloader-selected
qcom,mdss_dsi_n3_42_0d_0a_dsc_cmd panel. Existing upstream DTC warnings remain.
This establishes DT presence and overlay resolution; real-device boot and
driver/HAL compatibility still require runtime validation.
