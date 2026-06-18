# Satellite Link Budget Calculator

A browser based link budget tool for GEO satellite links. It computes the end to end dB budget of an uplink/downlink chain and updates every result live as you change the inputs. Everything runs client side in a single self contained `index.html`, with no build step and no server.

**Live demo:** https://senolgulgonul.github.io/linkbudget/

## Features

- **Geometry block.** Derives elevation, azimuth and slant range from earth station latitude/longitude and satellite longitude (spherical Earth, ECEF to local ENU).
- **Uplink.** Earth station EIRP from HPA power, feeder loss and TX antenna; uplink C/N; plus power flux density at the satellite and **SFD margin** against a required saturation flux density.
- **Downlink.** Earth station G/T from RX antenna and system noise temperature; downlink C/N.
- **Independent TX and RX antennas.** Separate diameter and efficiency for the transmit and receive earth stations.
- **DVB-S and DVB-S2 ModCod tables embedded in the code.** Pick a standard and a ModCod and the spectral efficiency and required threshold are filled in automatically. A manual mode is also available.
- **Combined link.** Uplink, downlink and optional interference are summed in the linear domain to give total C/N0, C/N, Es/N0, Eb/N0 and the link margin.
- **Signal path diagram** and a **dB waterfall table** showing every contributing term.

## What it computes

| Quantity | Expression |
|---|---|
| Antenna gain | `G(dBi) = 10·log10( η · (π·D·f / c)² )` |
| EIRP | `EIRP = P_HPA(dBW) − L_feeder + G_tx` |
| Free space path loss | `L_fs = 92.45 + 20·log10(d_km) + 20·log10(f_GHz)` |
| PFD at satellite | `PFD = EIRP − 10·log10(4π·d²) − L_atm` |
| SFD margin | `SFD margin = PFD − SFD_required` |
| Carrier to noise density | `C/N0 = EIRP − L_fs − L_atm + G/T − k`, with `k = −228.6 dBW/(K·Hz)` |
| Earth station G/T | `G/T = G_rx − 10·log10(T_sys)` |
| Combined C/N0 | `(C/N0)_tot = −10·log10( 10^(−up/10) + 10^(−dn/10) + 10^(−C/I0/10) )` |
| Modem metrics | `Es/N0 = C/N0 − 10·log10(Rs)`, `Eb/N0 = C/N0 − 10·log10(Rb)`, `Rb = Rs·S_eff` |
| Link margin | `margin = achieved threshold − required threshold` |

## Standards and thresholds

The required threshold and how the margin is taken depend on the selected standard, with the values embedded in the page:

- **DVB-S** (QPSK + Viterbi inner code + RS(204,188) outer code): the target is **Eb/N0** (BER 2×10⁻⁴ after Viterbi, QEF after RS). Net spectral efficiency `S_eff = 2·r_inner·(188/204)`. Margin is computed on Eb/N0.
- **DVB-S2** (ETSI EN 302 307, FECFRAME 64800): the target is **Es/N0** (ideal AWGN values). `S_eff` comes straight from the ModCod table. Margin is computed on Es/N0.
- **Manual**: enter `S_eff` and the threshold yourself, and choose whether the margin is based on Es/N0 or Eb/N0.

## Conventions and assumptions

- Free space loss uses the textbook constant **92.45** (for c = 2.998×10⁸ m/s).
- Antenna gain uses the exact aperture formula with an explicit efficiency, rather than a fixed gain constant.
- Atmospheric, rain and pointing losses are entered as a single lumped term per link. Detailed ITU-R models (P.618, P.676, P.840) are out of scope.
- The link margin uses the **combined** uplink + downlink C/N. For a downlink only result, set the uplink so it does not dominate, or read the per link C/N rows directly.
- Geometry assumes a spherical Earth and neglects station altitude.

This tool is intended for teaching and preliminary design.

## Usage

Open `index.html` in any modern browser. To self host on GitHub Pages, place `index.html` at the repository root, then enable **Settings → Pages → Deploy from a branch → `main` / root**. The page is then served at the live demo URL above.

The only external dependency is Google Fonts, loaded over HTTPS; if it is blocked the page falls back to system fonts and still works.

## Validation

The calculations have been cross checked against worked textbook and exam examples, including DVB-S2 8PSK 3/4 and 8PSK 5/6 downlink budgets and uplink SFD margins. Results agree to within about 0.05 dB, the small difference coming from the choice of free space loss constant (92.45 versus 92.5).

## Course

Built for **EEE539 Satellite Communication**, Department of Electrical and Electronics Engineering, Ostim Technical University, Ankara.

## License

MIT License. See `LICENSE`.

## Author

Şenol Gülgönül
