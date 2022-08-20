# Generate XRP Ledger Paper Account

Generate a r... XRP ledger account and matching Family Seed (secret) based on your own random input data (text & mouse / touch movement).
Inspired by [bitaddress.org](https://bitaddress.org)

By [Wietse Wind](https://wietse.com)
If you want to send me a sip with your newly generated & activated account:
  - `rwietsevLFg8XSmG3bEZzFein1g8RBqWDZ`

## Please note that the best way to run this, is by getting the source, installing the dependencies (see "Compile" below) and run this offline on your own device.

- Demo: https://www.xrpaddress.org
- Release: https://github.com/WietseWind/XRP-Paper-Account/releases

## Run for development
```
npm install
npm run serve
```

If you get an error (because of using npm 7) like:

```
Error: Rule can only have one resource source (provided resource and test + include + exclude)
```

... run:

```
npm update --legacy-peer-deps
```

### Compile (output in `dist` folder)
```
npm install
npm run build
```
