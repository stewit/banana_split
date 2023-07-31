# Banana Split fork 🍌

https://stewit.github.io/banana_split/

**This is a fork of Banana split (https://bs.parity.io, https://github.com/paritytech/banana_split) adding some optional convenience overrides that may negatively impact security depending on your setup if activated. If you are unsure please always use the original project!!!**

Compared to the original, this fork values guaranteed restorability by laymen / non-technical people higher than security.
* Allows to deactivate the browser offline mode enforcement. Use from browsers that hide or even do not have offline mode, e.g. on mobile devices.
* Allows to deactivate local file enforcement. Local file detection may not work on some setups.
* Additionally prints the qr code json on each shard sheet and allows to manually enter this json during restore instead of scanning the qr code. Use this if scanning qr codes does not work in your restoration setup.


# Banana Split

Banana Split makes your paper backups more resilient and secure using [Shamir’s secret sharing](https://en.wikipedia.org/wiki/Shamir%27s_Secret_Sharing).

It splits your secret (e.g., paper backup) into `N` pieces, but only requires a majority of those pieces (`N/2+1`) to recover your secret.

## Why Banana Split?
There’s a few problems with normal paper backups:
If you have the whole paper backup somewhere, you’re vulnerable to someone finding it and stealing it—without you ever knowing they did (e.g., they can just take a photo of it).
If you split your paper backup into two, what happens if one of those pieces goes missing?

For example:

You use Banana Split to split your paper backup into 5 pieces. You then give 5 friends a piece. It only takes 3 of those friends to come together to retrieve the paper backup. If 2 of your friends end up losing their piece, it’s okay. And if 2 of your friends collude and try to retrieve your backup, they won’t be able to.
That is to say:

**Any 3 friends will know everything; any 2 friends will know nothing.**

## Usage
### How do you make a Banana Split?
1. Save the Banana Split [HTML page](https://stewit.github.io/banana_split/) and **open it in offline mode**.
2. Type your secret into Banana Split.
Your secret will be encrypted with an auto-generated passphrase and split it into the number of QR codes you specify.
3. Print out your QR codes.
4. **Write the passphrase by hand on every sheet**.
Doing this by hand protects against an attacker intercepting things you’re sending to the printer.

### How do you retrieve a Banana Split?
Recovery can be done on any device with a webcam (or by entering the qr code json manually).

1. Save the Banana Split [HTML page](https://stewit.github.io/banana_split/) and **open it in offline mode**.
2. Using your webcam, scan in a majority of your QR code printouts (e.g., if you had 7 printouts, you’ll need 4). Or enter the qr code json manually for the majority of printouts.
3. Enter your passphrase.
4. Viola! Your secret is restored.

## ⚠️ Important
###  Work local and offline

Banana Split is a self-contained HTML page.
To ensure your precious data is never sent outside, it only works when opened from your local hard drive while your browser is in Offline mode. (Note: This can be overriden in this fork)

### Without the passphrase, you won’t be able to retrieve your secret

To retrieve your secret, you’ll need to have a majority of the printouts and know the passphrase.
This is why it is critical for you to write the passphrase on every printout.

### Keep the HTML document

This HTML page is a fully self-contained application under GPLv3 license.
Please feel free to copy and share it with anyone you want to use it. The more copies out there, the less likely it will be compromised or taken down.

## Development
#### Project setup
```
yarn install
```

#### Compiles and hot-reloads for development
```
NODE_OPTIONS=--openssl-legacy-provider yarn run serve
```

#### Compiles and minifies for production
```
NODE_OPTIONS=--openssl-legacy-provider yarn run build
```

#### Run unit tests
```
yarn run test:unit
```

#### Run end-to-end UI tests
```
NODE_OPTIONS=--openssl-legacy-provider yarn run test:e2e
```

#### Lints and fixes files
```
yarn run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
