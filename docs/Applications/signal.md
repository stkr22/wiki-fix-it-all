# Signal

## Build Signal

As always it is never just "Working". I had to built the java libraries form scratch to work on my raspberry pi.

Steps are:

- Install GraalVM  install build-essential libz-dev zlib1g-dev
- Fetching & compiling (sources) libsignal-client-java-0.2.3 & zkgroup-0.7.2
- Fetching (sources) signal-cli-0.8.1
- Copying the results of libsignal-client-java-0.2.3 & zkgroup-0.7.2 compilations (libsignal_jni.so & libzkgroup.so) into signal-cli-0.8.1/lib/src/main/resources
- Finally compiling signal-cli-0.8.1 with gradle assembleNativeImage

Sources:

- <https://github.com/exquo/signal-libs-build/releases>
- <https://github.com/AsamK/signal-cli/issues/615>
- <https://github.com/bbernhard/signal-cli-rest-api/tree/master/ext/libraries/libsignal-client>
- <https://github.com/bbernhard/signal-cli-rest-api/tree/master/ext/libraries/zkgroup>
- <https://github.com/bbernhard/signal-cli-rest-api>
- <https://github.com/bbernhard/signal-cli-rest-api/blob/master/Dockerfile>
- <https://wiki.fhem.de/wiki/Signalbot#Probleme_bei_normaler_Installation>
- <https://www.graalvm.org/reference-manual/native-image/#prerequisites>

## Set up Signal

First install the library:

``` bash
export VERSION=LATEST_VERSION
wget https://github.com/AsamK/signal-cli/releases/download/v"${VERSION}"/signal-cli-"${VERSION}".tar.gz
sudo tar xf signal-cli-"${VERSION}".tar.gz -C /opt
sudo ln -sf /opt/signal-cli-"${VERSION}"/bin/signal-cli /usr/local/bin/
```

Then we need to register a number. Note that I always needed a capture so probably get one upfront on (<https://signalcaptchas.org/registration/generate.html>):

``` bash
signal-cli -u {{ YOUR_NUMBER }} register --captcha {{ YOUR_CAPTCHA }}
```

++f12++ click console and look for a warning that includes a very long address(Prevented navigation to “signalcaptcha://).

Keys can be found here:
ls -lia .local/share/signal-cli/

Sources:

- <https://github.com/AsamK/signal-cli/wiki/Quickstart>
- <https://github.com/AsamK/signal-cli/wiki/Linking-other-devices-%28Provisioning%29>
- <https://github.com/AsamK/signal-cli/wiki/DBus-service>
- <https://signald.org/articles/captcha/>
- <https://fabiobarbero.eu/posts/signalbot/>
