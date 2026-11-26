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

## MMRM provider

With the display description present, the source kernel reaches recovery
and loads msm_drm, but SDE probe fails with -EINVAL: mmrm_client_register
reports "mmrm probe_init not done". The MMRM module is loaded; its device
node was absent as well. qcom/houji-mmrm.dtsi restores the eight provider
properties and 33 clock clients from CN OS3.0.307.0.WNCCNXM. The four stock
base DTBs carry identical data, without provider phandle references.
The complete dtbs build passes, and every property matches all four newly
merged houji trees. The MMRM driver also needs the Pineapple platform match;
the older imported driver only recognizes Waipio and Kalama internally.

## GPU hardware description

The original merged tree also reduced msm_gpu to a cooling-cell declaration;
it had no compatible string, registers, GMU or KGSL context-bank wiring.
The GPU module therefore could not bind despite compiling successfully.
qcom/gpu links to graphics-devicetree in the same pinned OnePlus SM8650
checkout already used for the graphics driver. The product Pineapple and
Pineapple-v2 bases include their corresponding controller and power-level
files. This retains separate 7.9.0 / 7.9.1 compatibles and chip IDs, matching
the corresponding CN stock base variants; the test phone is SoC 557 v2.0.
Full dtbs and all board-overlay applications pass. Both product merged
trees have active GPU, GMU and KGSL context-bank nodes. GPU firmware and
userspace operation still require device validation.
