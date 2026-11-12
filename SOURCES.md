# Sources

| Component | Source | Revision |
| --- | --- | --- |
| Linux 6.12 | [armbian/linux-rockchip](https://github.com/armbian/linux-rockchip) `rk-6.12-rkr1` | `112eaf45d8a2319be444f9e23602f5b400a6e3cc` |
| U-Boot | [rockchip-linux/u-boot](https://github.com/rockchip-linux/u-boot) | `1c535d65b8509f388d09e49fb6961f49fda35a1d` |
| DDR, SPL, TEE, USB loader and packaging tools | [rockchip-linux/rkbin](https://github.com/rockchip-linux/rkbin) | `3e288fe814e059dd06833495f845cab04ac20a5c` |

Selected rkbin components: DDR 750 MHz v1.08, SPL v1.12, standard TEE v2.50,
and USB plug v1.04. Archive and license hashes are in `patches/buildroot/`;
binary hashes are in `bootstrap/rkbin.sha256`.

Nerves Buildroot and the application cross-toolchain dependencies are declared in
`mix.exs` and locked in `mix.lock`. Buildroot's external ARMv7 toolchain is pinned
to `15.3.0` in `nerves_defconfig`, independently of the `15.3.1` Mix package.

## AIC8800 USB Wi-Fi and Bluetooth

`package/aic8800` pins Radxa's vendor-driver packaging at
`fce88024ed40279def034e203128e0892fdbcac3` from
[radxa-pkg/aic8800](https://github.com/radxa-pkg/aic8800).
It builds the USB firmware loader, Wi-Fi driver and Linux HCI Bluetooth driver,
installing only the AIC8800D80 firmware set. Archive and license hashes live in
`package/aic8800/aic8800.hash`. Radxa's `debian/copyright` declares `src/*`
GPL-2 (including the supplied firmware) and packaging GPL-3+; both that notice
and the repository license are collected by Buildroot.

The recipe applies the pinned Debian patch series, except its USB firmware-path
and log-level patches, which do not apply to the archive's CRLF sources.
The driver retains its default `/lib/firmware/aic8800D80` location. No stock
EM300 kernel modules or privately captured firmware files are redistributed.

## Buildroot source and license collection

The [CI workflow](.github/workflows/ci.yml) follows the official Nerves flow:
`make source`, `make all`, `make legal-info`, then `make system`, through
`mix nerves.artifact.build`. Buildroot notices are included in the artifact;
large source archives are not. There is no custom source-archive publisher.

Buildroot's legal-info report is **not a guarantee of complete corresponding
source**. In particular, Buildroot does not collect source archives for its
prebuilt external toolchain. Review the report's warnings, licenses and manifests
before distribution, and arrange any required corresponding-source distribution
or source offer. The external toolchain's upstream build recipes are in
[nerves-project/toolchains at v15.3.0](https://github.com/nerves-project/toolchains/tree/v15.3.0).
The Rockchip DDR/SPL/TEE/USB loader blobs remain proprietary, not source-built.

On Linux, build locally with `mix deps.get && mix nerves.artifact.build`.
The Buildroot reports and collected sources are under
`_build/target_dev/nerves/nerves_system_em300/build/legal-info/`. Retain these
when preparing corresponding-source distribution; also obtain any required
external-toolchain sources and notices. A successful CI build is not licensing
clearance. Review these obligations before manually publishing a draft.

## References

- [Nerves 2 migration guide](https://github.com/nerves-project/nerves/blob/v2/guides/advanced/updating-projects.md#nerves-system-maintainers)
- [nerves_system_trellis](https://github.com/nerves-project/nerves_system_trellis): A/B raw-FIT updates
- [nerves_system_rpi2](https://github.com/nerves-project/nerves_system_rpi2): Cortex-A7 toolchain and package structure
- [Rockchip kernel](https://github.com/rockchip-linux/kernel): RK3506 SoC device-tree definitions
- Rockchip RK3506 Technical Reference Manual, revision 1.2

Component licenses remain governed by their respective upstream projects.
