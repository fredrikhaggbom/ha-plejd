# Plejd component for Home Assistant

This is a simple Plejd component for Home Assistant, interfacing with the
bluetooth le protocol.

## Getting started

This has only been tested on a raspberry pi 3b+ running hassbian, it has to
run on a device with a bluetooth le module.

It requires that you know the Plejd crypto key and the device ids.

## Installing

Make sure to have bluepy installed in your python environment. Bluepy comes
with a helper called bluepy-helper, to allow scanning for devices this has
to be given additional capabilities:
```
$ sudo setcap 'cap\_net\_raw,cap\_net\_admin+eip' /srv/homeassistant/lib/python3.5/site-packages/bluepy/bluepy-helper
```

Run this as a custom component, put the files light.py and \_\_init\_\_.py in
custom\_components/plejd in your configuration.yaml add something like:

```
light:
  - platform: plejd
    crypto_key: !secret plejd
    devices:
      11:
        name: bedroom
      13:
        name: kitchen_1
      14:
        name: kitchen_2
      16:
        name: bathroom
```

## Gathering information

Obtaining the crypto key and the device ids is a crucial step to get this
running, this can be extracted from an android phone running the plejd app:

1. Turn on USB debugging and connect the phone to a computer.
2. Extract a backup from the phone:
```
$ adb backup com.plejd.plejdapp
```
3. Unpack the backup:
```
$ dd if=backup.ab bs=1 skip=24 | python -c "import zlib,sys;sys.stdout.write(zlib.decompress(sys.stdin.read()))" | tar -xv
```
4. Extract the cryptoKey:
```
$ cat apps/com.plejd.plejdapp/f/*/*.site  | jq '.PlejdMesh.CryptoKey' | sed 's/-//g'
```
5. Extract the outputAddresses:
```
$ cat apps/com.plejd.plejdapp/f/*/*.site  | jq '.PlejdMesh.outputAdresses' | grep -v '\$type' | jq '.[][]'
```

Steps 4 and 5 above can obviously be done manually instead of extracting the
fields using jq and shell tricks.

## License

```
Copyright 2019 Klas Lindfors <klali@avm.se>

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
