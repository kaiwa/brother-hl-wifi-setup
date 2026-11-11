# brother-wlan

Configure the WLAN of a Brother HL-1210W (and similar Brother laser printers) over USB from Linux, without Brother's Windows-only Wireless Device Setup Wizard.

The protocol was reverse-engineered from the wizard (`uwdsw.exe`): it sets proprietary PJL objects with
`@PJL DEFAULT OBJBRNET="<id>:<value>"`, where string values are hex bytes joined with `-`.

## Requirements

- Python 3 (standard library only)
- Printer connected over USB (`/dev/usb/lp0` by default)
- Optional: `nmcli`, for `--password-from-nm`

The printer device must be writable by your user:

```sh
echo 'SUBSYSTEM=="usbmisc", KERNEL=="lp[0-9]*", ATTRS{idVendor}=="04f9", MODE="0666"' \
  | sudo tee /etc/udev/rules.d/99-brother-lp.rules
sudo udevadm control --reload && sudo udevadm trigger --subsystem-match=usbmisc
```

## Usage

```sh
./brother-wlan                    # printer scans, you pick a network and enter the passphrase
./brother-wlan MySSID             # use this SSID (channel taken from the printer's scan)
./brother-wlan --scan             # only list the networks the printer sees
```

Options:

| Option | Meaning |
| --- | --- |
| `--device /dev/usb/lp0` | printer device |
| `--password-from-nm` | take the passphrase from the NetworkManager connection named like the SSID |
| `--dry-run` | print what would be sent, send nothing (passphrase masked) |

The printer only sees 2.4 GHz networks. It uses WPA/WPA2-PSK and DHCP. After the settings are sent it restarts its WLAN and joins the network within about a minute.
