# What is this?
This repository hosts the [Swagger API docs](https://lyczak.github.io/ClueApiDocs/) that I've started making to document the private API used by the [Clue](https://helloclue.com) period tracking app. The app was a little bit annoying to reverse engineer because it keeps it's own internal SSL root certificates, making intercepting HTTPS traffic impossible without decompiling the app, loading in a self-signed root certificate, and recompiling it. As time permits, I will add more endpoints to hopefully make this more useful.
# How can I contribute?
Spying on requests coming out of the app is a little challenging because it comes bundled with it's own CA root certificates that it uses to check the authenticity of the API server's certificate. To perform a MITM attack using a debugging proxy, you will have to modify the app to accept the debugging proxy's self-signed certificate. For the Android version of the app, this could hypothetically be done in the following way:

1. Find an Apk file for the Clue app.
2. Use [Apktool](https://ibotpeaches.github.io/Apktool/) to disassemble (`java -jar apktool.jar d clueapp.apk`) the Apk file into a directory.
3. Get a copy of your debugging proxy's signing certificate.
4. Convert the certificate to DER format (`openssl x509 -in mitm.cer -out mitm.der -outform DER`).
5. In the disassembled app directory, replace the file `./res/raw/certificate/certificate_amazon_root_ca1` with the converted DER debugging proxy certificate (I'm not sure if the name/file needs too be the same but I'd assume it does).
6. Use Apktool to build the modified app directory into a new Apk file (`java -jar apktool.jar b clueapp`).
7. Use [Uber Apk Signer](https://github.com/patrickfav/uber-apk-signer) to sign the modified Apk (`java -jar uber-apk-signer.jar --apks clueapp/dist/clueapp_modified.apk`)
8. Install the modified Apk onto an Android device or emulator.
9. Setup the Android device or emulator to pass traffic through your debugging proxy, and enable SSL proxying for `https://api.helloclue.com:443`.

Optionally, you could also find information regarding application logic and API models by generating a JAR file from the Clue app's Apk using something like [dex2jar](https://github.com/pxb1988/dex2jar). API endpoints are listed in `com.biowink.clue.data.account.api.ApiServiceV2` and API models in `com.biowink.clue.data.account.json.RequestBody` and `ResponseBody`. Other API-endpoint-related things include `com.biowink.clue.sync.recovery.api.SyncRecoveryService` and the `/doctors-report` endpoint but I don't recall where that was documented.